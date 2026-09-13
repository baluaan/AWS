---
title: "Khởi tạo Phản ứng tự động"
date: 2026-09-11
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

### Mục tiêu

Khởi tạo các dịch vụ Amazon Lambda, DynamoDb, IAM, CloudWatch, NACL nhằm tạo ra quy trình phản ứng dự cố tự động khi bị tấn công ssh.
Thành công trong việc chặn ip của kẻ tấn công

---

## 1. Tổng quan

Trong kiến trúc này:
- **Amazon CloudWatch** CloudWatch Agent thu thập `/var/log/auth.log` từ máy chủ EC2. Subscription Filter quét sự kiện Failed password và chuyển payload tới AWS Lambda.
- **AWS Lambda** Giải mã log, trích xuất IP vi phạm qua Regex, tương tác với DynamoDB để đếm số lần thất bại, gửi email cảnh báo qua SNS và tự động chèn rule DENY vào NACL.
- **Amazon DynamoDB** ưu trữ bộ đếm số lần đăng nhập sai theo IP và mốc thời gian (trong 1 phút) nhằm phục vụ việc kiểm tra ngưỡng vi phạm (≥ 5 lần/1 phút).
- **AWS IAM** Cấp quyền an toàn cho EC2 đẩy log về CloudWatch và cấp quyền cho Lambda thao tác với DynamoDB, Network ACL, CloudWatch Logs.
- **Network ACL** Ngăn chặn triệt để gói tin SSH của IP vi phạm ngay từ tầng mạng VPC trước khi kết nối tới được máy chủ EC2.

---

## 2. Quy trình triển khai

1. Khởi tạo IAM role theo các bước sau:

**Bước 1** Truy cập **IAM** > **Roles** chọn **Create Role**
- Cấu hình như sau:
  - Trusted entity type: AWS service 
  - Use case: Lambda
  - Chọn: Next
![Step1](/images/5/7/8.png)

**Bước 2** Thêm permission
  - Chọn: Create inline policy
  - Policy: 
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateNetworkAclEntry",
                "ec2:DescribeNetworkAcls"
            ],
            "Resource": "*"
        }
    ]
}
```
  - Chọn: Next 
![Step2](/images/5/7/9.png)

**Bước 3** 
Đặt role name: `Lambda-SSH-AutoBlock-Role` 
Đặt tên Inline policy: `LambdaSSHAutoBlockRole`
Chọn: **Create**
![Role name](/images/5/7/10.png)
![Inline policy name](/images/5/7/11.png)
Vào IAM role vừa tạo để add thêm policy `AWSLAmbdaBasicExcutionRole`
![Add Policy](/images/5/7/14.png)

2. Khởi tạo và cấu hình **Network ACLs**

**Bước 1** Khởi tạo Network ACL
Truy cập **VPC** > **Network ACLs** > **Create network ACL**
- Name: Name: SSH-Auto-Block-NACL
- VPC: chọn VPC tạo ở bước tạo EC2
- Tag: chọn Key: Name, Value: Name: SSH-Auto-Block-NACL
- Create network ACL
![Step1](/images/5/7/4.png)

**Bước 2** Cấu hình Network ACL mới tạo
Truy cập theo **VPC** > **Network ACLs** > **acl-050ea4da0cd7e08aa / Name: SSH-Auto-Block-NACL** Đầu tiên chọn: **Edit subnet associations**
Chọn `Subnet public 1` do tạo máy EC2 bằng Subnet đó
Sau đó **Save change**
![Step2](/images/5/7/5.png)
Tiếp theo đến cấu hình `Inbound` và `outbound` rule
`Add new rule` với cấu hình: 
  - Rule number: 100
  - Type: All trafic
  - Destination: 0.0.0.0/0
  - Allow
![Inbound rule](/images/5/7/6.png)
![Outbound rule](/images/5/7/7.png)

3. Khởi tạo và cấu hình Lambda funtion
**Bước 1** Tạo Lambda funtion
Truy cập từng bước như sau: **Lambda** > **Functions** > **Create function**
  - Chọn: Author from scratch
  - Funtion name: ssh-auto-block
  - Chọn: python 3.14
  - Additional setting > Custom execution role: Lambda-SSH-AutoBlock-Role
  - Save
  - Create Funtion
![Step1](/images/5/7/12.png)

**Bước 2** Cấu hình 
Truy cập vào funtion vừa tạo `ssh-auto-block`
Tìm mục `Configuaration` chọn vào ` Environment variable` và chọn `edit`
Cấu hình:
  - Key: NACL_ID
  - Value: giá trị id của NACL vừa tạo `acl-050ea4da0cd7e08aa`
  - Save
![Step2](/images/5/7/13.png)

4. Tạo Subcription filters
Truy cập từng bước theo đường dẫn **CloudWatch** > **Log management** > **/aws/ec2/security/auth**
Chọn **Subcription filters**
Chọn `Create lambda subcription filter`
![Vào mục tạo filter](/images/5/7/15.png)

Sau khi vào mục tạo filter:
- Đặt destination: ssh-auto-block
- Subcription filter pattern: `"Failed password"
- Subcription filter name: ssh
- Sau đó chọn `Start streaming`
![Cấu hình](/images/5/7/16.png)

5. Tạo DynamoDB 
**Bước 1** Truy cập từng bước **DynamoDB** > **Tables** > **Create table**
Cấu hình bảng dùng để đếm số lần tấn công để kết hợp với Lambda
  - Name: SSHBruteforceCounter
  - Partition key: id - string
  - Create
![Create DynamoDB](/images/5/7/18.png)

**Bước 2** Liên kết môi trường
Truy cập vào funtion `ssh-auto-block`
Tìm mục `Configuaration` chọn vào ` Environment variable` và chọn `edit`
Cấu hình:
  - Key: TABLE_NAME
  - Value: SSHBruteforceCounter
  - Save
![Step2](/images/5/7/19.png)

**Bước 3** Thêm permission vào IAM role
Truy cập vào `Lambda-SSH-AutoBlock-Role` sau đó `add permission` theo `create inline policy`
Thêm code để có quyên cập nhật bảng DynamoDB
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:UpdateItem"
            ],
            "Resource": "arn:aws:dynamodb:*:133665990651:table/SSHBruteForceCounter"
        }
    ]
}
```
![Add permission](/images/5/7/20.png)

Sau đó đặt tên cho permission vừa tạo:
Name: LambdaSSHBruteForceDynamoDB
Sau đó: Create policy
![Đặt tên](/images/5/7/21.png)
6. Dán code cho funtion
Truy cập vào funtion `ssh-auto-block`
Vào mục code:
Sau đó `Deploy`
```python
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
![code](/images/5/7/22.png)
---


## 4. Kết quả mong đợi

Sau khi hoàn thành chương này, bạn sẽ đạt được:

- Tạo được các rule NACL để chặn hoặc cho phép lưu lượng vào ra.
- Tạo được bảng DynamoDB để đếm các trường hợp `Failed Password`, kêt hợp với Lambda funtion để kich hoạt chặn ip gây hại.
- Hoàn thiện cấu hình chức năng tự động chặn ip khi bị tấn công ssh.




