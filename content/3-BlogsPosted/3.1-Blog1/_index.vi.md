---
title: "Blog 1"
date: 2026-08-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Tối ưu chi phí EC2 & RDS bằng AWS Lambda và Amazon EventBridge

## Bối cảnh & Vấn đề

Trong quản lý hạ tầng cloud, các môi trường non-production (Dev, Test, Staging) thường chỉ cần hoạt động trong giờ làm việc (ví dụ: 8:00 – 18:00, từ Thứ Hai đến Thứ Sáu). Tuy nhiên, các tài nguyên này lại thường bị để chạy **24/7 (168 giờ/tuần)**, trong khi thực tế chỉ được sử dụng khoảng **~50 giờ/tuần**.

Việc để các compute instance và database cluster ở trạng thái idle vào ban đêm và cuối tuần dẫn đến lãng phí chi phí cloud lên tới **~70%** cho các workload non-production.

Thực hiện việc này bằng tay — yêu cầu developer tự tắt/mở instance mỗi ngày — dễ xảy ra lỗi, thiếu nhất quán, và làm giảm năng suất của kỹ sư.

Vậy làm thế nào để tự động hoá việc lập lịch tài nguyên ở quy mô enterprise, đồng thời vẫn cho các team quyền kiểm soát workload nào cần được quản lý?

**Vậy đâu là cách tiếp cận đúng?**

## Kiến trúc giải pháp: Tự động hoá FinOps theo hướng sự kiện (Event-Driven)

![Kiến trúc tối ưu chi phí EC2 & RDS tự động](/images/blog/blog1.png)

Giải pháp sử dụng phương pháp **Serverless & Event-Driven**, kết hợp **Amazon EventBridge**, **AWS Lambda (Python/Boto3)**, và **AWS Resource Tagging**:

- **Lựa chọn dựa trên Tag**: Chỉ các tài nguyên được gắn tag `AutoScheduler = true` mới được xử lý, giúp bảo vệ các hạ tầng quan trọng khỏi bị tác động ngoài ý muốn.
- **Kích hoạt theo lịch**: Các rule cron của EventBridge sẽ kích hoạt Lambda function vào thời điểm start/stop đã định sẵn (ví dụ: Stop lúc 19:00, Start lúc 7:00).
- **Hỗ trợ đa dịch vụ**: Xử lý đồng thời cả EC2 instance độc lập và Amazon RDS DB instance/cluster.
- **Không phát sinh chi phí hạ tầng idle**: Toàn bộ automation dựa trên các dịch vụ serverless, hoạt động hoàn toàn trong giới hạn AWS Free Tier.

## 1. Cấu hình IAM Policy & Role

Để cho phép AWS Lambda kiểm tra và quản lý các EC2 và RDS instance một cách an toàn, hãy tạo một IAM Role với policy theo nguyên tắc least-privilege.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "EC2SchedulerPermissions",
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances",
        "ec2:StartInstances",
        "ec2:StopInstances"
      ],
      "Resource": "*"
    },
    {
      "Sid": "RDSSchedulerPermissions",
      "Effect": "Allow",
      "Action": [
        "rds:DescribeDBInstances",
        "rds:StartDBInstance",
        "rds:StopDBInstance",
        "rds:DescribeDBClusters",
        "rds:StartDBCluster",
        "rds:StopDBCluster"
      ],
      "Resource": "*"
    },
    {
      "Sid": "LoggingPermissions",
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    }
  ]
}
```

## 2. Logic tự động hoá chính: AWS Lambda (Python 3.12)

Lambda function duy nhất dưới đây sẽ đánh giá động payload đầu vào (`"action": "start"` hoặc `"action": "stop"`) để lọc và thực thi việc thay đổi trạng thái cho cả tài nguyên EC2 và RDS.

```python
import os
import logging
import boto3

logger = logging.getLogger()
logger.setLevel(logging.INFO)

ec2 = boto3.client('ec2')
rds = boto3.client('rds')

TARGET_TAG_KEY = os.environ.get('TARGET_TAG_KEY', 'AutoScheduler')
TARGET_TAG_VALUE = os.environ.get('TARGET_TAG_VALUE', 'true')


def lambda_handler(event, context):
    action = event.get('action', '').lower()
    if action not in ['start', 'stop']:
        logger.error(f"Invalid action '{action}'. Expected 'start' or 'stop'.")
        return {'statusCode': 400, 'body': 'Invalid action'}

    logger.info(f"Starting resource {action} sequence...")

    # Manage EC2 Instances
    manage_ec2_instances(action)

    # Manage RDS DB Instances
    manage_rds_instances(action)

    return {'statusCode': 200, 'body': f"Resource {action} execution completed."}


def manage_ec2_instances(action):
    filters = [
        {'Name': f'tag:{TARGET_TAG_KEY}', 'Values': [TARGET_TAG_VALUE]},
        {'Name': 'instance-state-name', 'Values': ['stopped' if action == 'start' else 'running']}
    ]

    response = ec2.describe_instances(Filters=filters)
    instance_ids = []

    for reservation in response.get('Reservations', []):
        for instance in reservation.get('Instances', []):
            instance_ids.append(instance['InstanceId'])

    if not instance_ids:
        logger.info(f"No EC2 instances eligible for action: {action}")
        return

    if action == 'start':
        ec2.start_instances(InstanceIds=instance_ids)
        logger.info(f"Successfully initiated START for EC2 Instances: {instance_ids}")
    else:
        ec2.stop_instances(InstanceIds=instance_ids)
        logger.info(f"Successfully initiated STOP for EC2 Instances: {instance_ids}")


def manage_rds_instances(action):
    response = rds.describe_db_instances()

    for db in response.get('DBInstances', []):
        db_id = db['DBInstanceIdentifier']
        db_status = db['DBInstanceStatus']

        # Verify tag existence
        arn = db['DBInstanceArn']
        tags_response = rds.list_tags_for_resource(ResourceName=arn)
        tag_list = tags_response.get('TagList', [])

        has_target_tag = any(
            t['Key'] == TARGET_TAG_KEY and t['Value'].lower() == TARGET_TAG_VALUE.lower()
            for t in tag_list
        )

        if not has_target_tag:
            continue

        if action == 'start' and db_status == 'stopped':
            rds.start_db_instance(DBInstanceIdentifier=db_id)
            logger.info(f"Successfully initiated START for RDS Instance: {db_id}")
        elif action == 'stop' and db_status == 'available':
            rds.stop_db_instance(DBInstanceIdentifier=db_id)
            logger.info(f"Successfully initiated STOP for RDS Instance: {db_id}")
```

## 3. Lập lịch với Amazon EventBridge

Tạo hai rule Amazon EventBridge để kích hoạt cùng một Lambda function với payload JSON khác nhau.

### Rule 1: Dừng tài nguyên Non-Prod (Rule buổi tối)

- **Schedule expression:** `cron(0 12 ? * MON-FRI *)` → 12:00 UTC = 19:00 UTC+7
- **Target:** Lambda function
- **JSON input cố định:**

```json
{
  "action": "stop"
}
```

### Rule 2: Khởi động tài nguyên Non-Prod (Rule buổi sáng)

- **Schedule expression:** `cron(0 0 ? * MON-FRI *)` → 00:00 UTC = 07:00 UTC+7
- **Target:** Lambda function
- **JSON input cố định:**

```json
{
  "action": "start"
}
```

## Kết quả & Phân tích tác động

Bằng cách chuyển các tài nguyên non-production từ mô hình chạy 24/7 sang mô hình theo lịch ~50 giờ/tuần (7:00 – 19:00, Thứ Hai – Thứ Sáu), thời gian vận hành được giảm đáng kể.

| Chỉ số                               | Trước khi tự động hoá (Baseline) | Sau khi tự động hoá bằng Lambda & EventBridge |
| ------------------------------------ | -------------------------------- | --------------------------------------------- |
| Số giờ hoạt động mỗi tuần            | 168 giờ/tuần                     | ~60 giờ/tuần (đã tính buffer)                 |
| Thời gian compute billable mỗi tháng | ~720 giờ/tháng                   | ~260 giờ/tháng                                |
| Mức giảm chi phí compute mỗi tháng   | 0%                               | ~64%                                          |
| Sự can thiệp của con người           | Cao (start/stop bằng tay)        | Không (100% event-driven)                     |
| Tác động vận hành                    | Rủi ro quên tắt tài nguyên       | Tự động & có thể audit đầy đủ qua CloudWatch  |

## Điểm quan trọng & Best Practice

- **Thực thi chính sách tagging:** Áp dụng các quy tắc tagging chuẩn bằng AWS Organizations Tag Policies hoặc AWS Config rules, để đảm bảo các instance non-production mới tạo cũng được scheduler nhận diện tự động.
- **Xử lý Multi-AZ & Aurora cluster:** Với các DB cluster Multi-AZ hoặc Aurora, hãy sử dụng các API `describe_db_clusters` và `stop_db_cluster` tương ứng.
- **Graceful shutdown:** Đảm bảo developer không chạy các background job dài hạn, không có checkpoint, ngoài giờ làm việc — vì các job này có thể bị gián đoạn bởi việc tắt tự động.
- **Lưu ý về việc RDS tự khởi động lại:** Amazon RDS sẽ tự động khởi động lại các DB instance đã dừng sau 7 ngày để áp dụng các bản cập nhật bảo trì. Lịch cron của EventBridge xử lý vấn đề này bằng cách liên tục thực thi lệnh stop vào các ngày không làm việc.

## Tài liệu tham khảo

- [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/)
- [Amazon EventBridge Schedule Expressions](https://docs.aws.amazon.com/eventbridge/)
- [Boto3 Documentation — EC2 Client](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2.html)
- [Boto3 Documentation — RDS Client](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/rds.html)
