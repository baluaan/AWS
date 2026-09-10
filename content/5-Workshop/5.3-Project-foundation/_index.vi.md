---
title: "Chuẩn bị dự án"
date: 2026-08-24
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

### Mục tiêu

Trong phần này, bạn sẽ chuẩn bị mã nguồn website tĩnh và tệp cấu hình script tự động hóa bằng Python cho AWS Lambda trước khi tiến hành triển khai lên hạ tầng AWS. Bạn cũng sẽ kiểm tra cấu trúc mã nguồn để đảm bảo quá trình triển khai diễn ra liền mạch.

---

## 1. Chuẩn bị mã nguồn website tĩnh

Dự án này sử dụng một trang web tĩnh (HTML/CSS/JS) làm đối tượng bảo vệ. Mã nguồn trang web mẫu được lưu trữ trên GitHub:
[https://github.com/vutungvn/portfolio](https://github.com/vutungvn/portfolio)

Để tải mã nguồn trang web về máy cục bộ, bạn mở Terminal hoặc PowerShell và thực hiện các lệnh sau:

```bash
# Clone kho lưu trữ chứa mã nguồn website
git clone https://github.com/vutungvn/portfolio.git

# Di chuyển vào thư mục dự án
cd portfolio

```

Giao diện sau khi chạy lên thành công:

![Chạy ứng dụng](/images/Workshop/Project-foundation/run-localhost.png)

---

## 2. Chuẩn bị mã nguồn hàm AWS Lambda (Python)

Tạo tệp `lambda_function.py` lưu trên máy cục bộ. Đoạn mã Python này sẽ được triển khai lên AWS Lambda ở Bước 5.8 để trích xuất IP vi phạm từ CloudWatch Logs và tự động cập nhật WAF IP Set.

```python
import os
import boto3
import json
import time

# Khởi tạo clients AWS SDK
wafv2 = boto3.client('wafv2', region_name='us-east-1')
logs = boto3.client('logs', region_name='us-east-1')
sns = boto3.client('sns', region_name='us-east-1')

def lambda_handler(event, context):
    # Lấy thông số từ Biến Môi Trường (Environment Variables)
    ip_set_name = os.environ['IP_SET_NAME']
    ip_set_id = os.environ['IP_SET_ID']
    log_group_name = os.environ['LOG_GROUP_NAME']
    sns_topic_arn = os.environ['SNS_TOPIC_ARN']

    print(f"Bắt đầu quét CloudWatch Log Group: {log_group_name}")

    # Truy vấn CloudWatch Logs trong khoảng 5 phút gần nhất
    now = int(time.time() * 1000)
    start_time = now - (5 * 60 * 1000)

    query = "fields clientIp | filter action = 'BLOCK' | stats count(*) by clientIp"
    start_query_response = logs.start_query(
        logGroupName=log_group_name,
        startTime=start_time,
        endTime=now,
        queryString=query
    )

    query_id = start_query_response['queryId']

    # Chờ truy vấn hoàn tất
    response = None
    while response is None or response['status'] == 'Running':
        time.sleep(1)
        response = logs.get_query_results(queryId=query_id)

    extracted_ips = []
    for result in response['results']:
        for field in result:
            if field['field'] == 'clientIp':
                ip = field['value']
                # Định dạng chuẩn CIDR cho WAF IP Set
                formatted_ip = f"{ip}/128" if ":" in ip else f"{ip}/32"
                extracted_ips.append(formatted_ip)

    if not extracted_ips:
        print("Không tìm thấy IP vi phạm mới trong logs.")
        return {'statusCode': 200, 'body': 'No IPs to block'}

    # Lấy thông tin IP Set hiện tại từ AWS WAF
    ip_set = wafv2.get_ip_set(
        Name=ip_set_name,
        Scope='CLOUDFRONT',
        Id=ip_set_id
    )

    current_addresses = ip_set['IPSet']['Addresses']
    lock_token = ip_set['LockToken']

    # Thêm IP vi phạm mới vào danh sách chặn
    updated_addresses = list(set(current_addresses + extracted_ips))

    # Gọi API Cập nhật WAF IP Set
    wafv2.update_ip_set(
        Name=ip_set_name,
        Scope='CLOUDFRONT',
        Id=ip_set_id,
        Addresses=updated_addresses,
        LockToken=lock_token
    )

    print(f"Đã cập nhật thành công WAF IP Set. Danh sách IP bị chặn: {updated_addresses}")
    return {'statusCode': 200, 'body': f"Blocked IPs: {extracted_ips}"}
```

---

## 3. Xác định các tham số môi trường cần thiết

Khi triển khai trên AWS Console ở các bước sau, bạn sẽ cần sử dụng các tham số cấu hình thống nhất sau:

| Tham số             | Giá trị                             |
| ------------------- | ----------------------------------- |
| Region bắt buộc     | `us-east-1` (US East - N. Virginia) |
| Tên WAF IP Set      | `AutoBlockedIPSetV6`                |
| Tên WAF Web ACL     | `WebsiteProtectionACL`              |
| Tên Log Group       | `aws-waf-logs-cloudfront`           |
| Tên SNS Topic       | `WAFAlertTopic`                     |
| Tên Lambda Function | `WAFAutoBlockFunction`              |

---

## 4. Kết quả mong đợi

Sau khi hoàn thành phần này, bạn sẽ:

- Chuẩn bị thành công dự án website tĩnh.
- Chuẩn bị sẵn đoạn mã nguồn Python (`lambda_function.py`) xử lý logic chặn IP tự động.
- Nắm rõ danh sách các tên tài nguyên và thông số cấu hình chuẩn để triển khai trên AWS Management Console ở các phần tiếp theo.
