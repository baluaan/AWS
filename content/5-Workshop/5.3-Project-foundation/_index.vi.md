---
title: "Chuẩn bị dự án"
date: 2026-09-11
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

### Mục tiêu

Trong phần này, bạn sẽ chuẩn bị tệp cấu hình script tự động hóa bằng Python cho AWS Lambda và sẵn sàng các kịch bản kiểm thử giả lập tấn công SSH trước khi tiến hành triển khai lên hạ tầng AWS. Bạn cũng sẽ nắm rõ danh sách tham số và tên tài nguyên cấu hình chuẩn để đảm bảo quá trình triển khai diễn ra liền mạch.

---

## 1. Chuẩn bị kịch bản kiểm thử SSH Brute Force trên máy cục bộ

Dự án này sử dụng máy chủ Amazon EC2 Ubuntu làm đối tượng bảo vệ khỏi các cuộc tấn công dò quét mật khẩu SSH. Để chuẩn bị cho bước kiểm thử hệ thống tự động phản ứng, bạn chuẩn bị sẵn các câu lệnh kiểm thử trên Terminal hoặc PowerShell cục bộ:

Thực hiện lệnh kiểm thử kết nối SSH thủ công (giả lập đăng nhập sai mật khẩu):
"ssh invalid_user@<EC2_PUBLIC_IP>"

Hoặc chuẩn bị sẵn công cụ Hydra để thực hiện giả lập tấn công Brute Force tự động với tần suất cao (để kích hoạt ngưỡng vi phạm ≥ 5 lần/1 phút):
"hydra -l admin -P passwords.txt <EC2_PUBLIC_IP> ssh -t 4"

---

## 2. Chuẩn bị mã nguồn hàm AWS Lambda (Python)

Tạo tệp "lambda_function.py" lưu trên máy cục bộ. Đoạn mã Python này sẽ được tải lên dịch vụ AWS Lambda ở các bước tiếp theo nhằm giải mã dữ liệu log từ CloudWatch, bóc tách địa chỉ IP vi phạm, cập nhật bộ đếm vào Amazon DynamoDB, gửi thông báo qua Amazon SNS và tự động chèn quy tắc "DENY" vào Network ACL (NACL).
```json
import boto3
import os
import ipaddress
import base64
import gzip
import json
import re
import time

ec2 = boto3.client("ec2")
dynamodb = boto3.resource("dynamodb")

NACL_ID = os.environ["NACL_ID"]
TABLE_NAME = os.environ["TABLE_NAME"]

table = dynamodb.Table(TABLE_NAME)

THRESHOLD = 5
WINDOW_SECONDS = 60


def lambda_handler(event, context):

    print("Received CloudWatch Logs event")

    # ==========================================
    # 1. Decode CloudWatch Logs event
    # ==========================================

    try:

        compressed_payload = base64.b64decode(
            event["awslogs"]["data"]
        )

        payload = gzip.decompress(
            compressed_payload
        )

        data = json.loads(payload)

    except Exception as e:

        print("Failed to decode CloudWatch event:")
        print(str(e))

        return {
            "statusCode": 400,
            "message": "Invalid CloudWatch Logs event"
        }


    # ==========================================
    # 2. Process each SSH log
    # ==========================================

    for log_event in data["logEvents"]:

        message = log_event["message"].strip()

        print("SSH LOG:")
        print(message)


        # ==========================================
        # 3. Extract attacker IP
        # ==========================================

        match = re.search(
            r"Failed password.*from\s+(\d{1,3}(?:\.\d{1,3}){3})",
            message
        )

        if not match:

            print("No attacker IP found")

            continue


        attacker_ip = match.group(1)

        print("ATTACKER IP:", attacker_ip)


        # ==========================================
        # 4. Validate IP
        # ==========================================

        try:

            ipaddress.ip_address(attacker_ip)

        except ValueError:

            print("Invalid IP:", attacker_ip)

            continue


        # ==========================================
        # 5. Create 1-minute bucket
        # ==========================================

        current_time = int(time.time())

        minute_bucket = current_time // WINDOW_SECONDS

        item_id = f"{attacker_ip}#{minute_bucket}"

        expiration = current_time + 120


        print("Counter ID:", item_id)


        # ==========================================
        # 6. Increase failure counter
        # ==========================================

        response = table.update_item(

            Key={
                "id": item_id
            },

            UpdateExpression="""
                ADD failure_count :one
                SET expires_at = if_not_exists(expires_at, :expiration)
            """,

            ExpressionAttributeValues={
                ":one": 1,
                ":expiration": expiration
            },

            ReturnValues="ALL_NEW"
        )


        failure_count = response["Attributes"]["failure_count"]


        print(
            "Failed attempts:",
            failure_count,
            "/",
            THRESHOLD
        )


        # ==========================================
        # 7. Check threshold
        # ==========================================

        if failure_count < THRESHOLD:

            print(
                f"{attacker_ip}: "
                f"{failure_count}/{THRESHOLD} "
                f"attempts - NOT BLOCKED"
            )

            continue


        # ==========================================
        # 8. Threshold reached
        # ==========================================

        print(
            f"THRESHOLD REACHED: "
            f"{attacker_ip}"
        )


        cidr = attacker_ip + "/32"


        # ==========================================
        # 9. Check existing NACL rules
        # ==========================================

        response = ec2.describe_network_acls(
            NetworkAclIds=[NACL_ID]
        )

        used_rules = []

        already_blocked = False


        for acl in response["NetworkAcls"]:

            for entry in acl["Entries"]:

                used_rules.append(
                    entry["RuleNumber"]
                )

                if (
                    entry.get("CidrBlock") == cidr
                    and entry.get("RuleAction") == "deny"
                    and entry.get("Egress") is False
                ):

                    already_blocked = True

                    break


        if already_blocked:

            print(
                "IP already blocked:",
                cidr
            )

            continue


        # ==========================================
        # 10. Find available rule number
        # ==========================================

        rule_number = 50

        while rule_number in used_rules:

            rule_number += 1


        # ==========================================
        # 11. Create NACL DENY rule
        # ==========================================

        try:

            ec2.create_network_acl_entry(

                NetworkAclId=NACL_ID,

                RuleNumber=rule_number,

                Protocol="-1",

                RuleAction="deny",

                Egress=False,

                CidrBlock=cidr
            )

            print(
                "SUCCESSFULLY BLOCKED:",
                attacker_ip
            )

            print(
                "NACL rule:",
                rule_number
            )

        except Exception as e:

            print(
                "Failed to create NACL rule:"
            )

            print(str(e))


    return {

        "statusCode": 200,

        "message":
            "SSH brute-force detection processed"

    }
```
---


## 3. Kết quả mong đợi

Sau khi hoàn thành phần này, bạn sẽ:

- Chuẩn bị sẵn sàng các kịch bản lệnh kiểm thử tấn công SSH Brute Force trên máy trạm cục bộ.
- Tạo sẵn tệp mã nguồn Python ("lambda_function.py") xử lý logic bóc tách IP và kích hoạt phản ứng tự động.
- Nắm rõ danh sách các tên tài nguyên, bảng lưu trữ và thông số cấu hình chuẩn để triển khai nhất quán trên AWS Management Console ở các phần tiếp theo.