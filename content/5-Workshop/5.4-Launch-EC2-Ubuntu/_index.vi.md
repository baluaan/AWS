---
title: "Khởi tạo máy chủ Amazon EC2 Ubuntu"
date: 2026-08-24
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Mục tiêu

Xây dựng hạ tầng máy chủ mục tiêu (Target Host) cho hệ thống bằng cách sử dụng dịch vụ Amazon EC2 chạy hệ điều hành Ubuntu Server, đóng vai trò là máy chủ SSH chịu sự giám sát và bảo vệ tự động trước các cuộc tấn công dò quét mật khẩu (Brute Force).

---

## 1. Tổng quan

Máy chủ mục tiêu là nơi vận hành dịch vụ SSH (`sshd`) và tiếp nhận các kết nối từ người dùng cũng như các lượt dò quét không hợp lệ từ kẻ tấn công. Trong dự án này, **Amazon EC2 (Elastic Compute Cloud)** chạy hệ điều hành **Ubuntu Server** được lựa chọn làm máy chủ bảo vệ nhờ tính linh hoạt, phổ biến và khả năng tích hợp sâu với các dịch vụ giám sát của AWS.

Mô hình triển khai áp dụng tiêu chuẩn bảo mật và giám sát tập trung:
- Máy chủ EC2 được gắn **IAM Role** tích hợp chính sách "CloudWatchAgentServerPolicy" cho phép đẩy log hệ thống an toàn về CloudWatch.
- Mọi nhật ký đăng nhập SSH đều được ghi nhận trực tiếp tại file hệ thống "/var/log/auth.log".
- **CloudWatch Agent** cài đặt trên EC2 sẽ đóng vai trò là trình thu thập dữ liệu (Log Collector) đẩy dữ liệu log theo thời gian thực về CloudWatch Logs Group để phục vụ phân tích tự động ở các chương tiếp theo.

---


## 2. Quy trình triển khai

1. Khởi tạo VPC theo các bước sau:
Truy cập **VPC** > **Yours VPCs** chọn **Create VPC**
- Cấu hình như sau:
  - Chọn: VPC and more
  - Name: project-AWS-vpc
  - Chọn: Create VPC
![Khởi tạo VPC](/static/images/5/5.4.1.png)
2. Khởi tạo IAM role cho EC2 theo các bước sau:
Truy cập **IAM** > **Roles** chọn **Create Role**
- Cấu hình như sau:
  - Chọn service: ec2
  - Permission: CloudWatchAgentServerpolicy
  - Chọn **Next** 
  - Name: EC2-CloudWatch-Agent-Role
![khởi tạo IAM](/static/images/5/5.4.8.png)
3. Quy trình khởi tạo máy EC2 unbutu được chia thành các bước sau:

**Bước 1:** Khởi tạo EC2:
- Truy cập **EC2** > **Instances** và chọn **Launch Intances**
- Cài đặt các thông số như sau:
  - Name: AWS-Security-Monitoring
  - AMI: Ubuntu Server 26.04 LTS
  - Instance type: t3.micro 
  - Key pair: unbutu.pem
  - Network: chọn **edit** sau đó chọn vpc vừa tạo và chọn subnet public
  - Chọn: **Launch instance**
![Khởi tạo EC2](/static/images/5/5.4.2.png)
![Tiếp tục khởi tạo](/static/images/5/5.4.3.png)
**Bước 2:** Cấu hình máy EC2 vừa tạo
- Thực hiện theo các lệnh sau: 
  - sudo apt update
  - sudo apt install -y wget
  - wget https://amazoncloudwatch-agent.s3.amazonaws.com/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
  - sudo dpkg -i -E ./amazon-cloudwatch-agent.deb
![Tải môi trường Cloudwatch](/static/images/5/5.4.4.png)
  - sudo nano /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.d/file_amazon-cloudwatch-agent.json
  Sau đó dán đoạn lệnh này vào:
```json
{
  "agent": {
    "metrics_collection_interval": 60
  },
  "metrics": {
    "metrics_collected": {
      "cpu": {
        "measurement": [
          "cpu_usage_idle",
          "cpu_usage_user",
          "cpu_usage_system"
        ],
        "metrics_collection_interval": 60
      },
      "disk": {
        "measurement": [
          "used_percent"
        ],
        "resources": [
          "/"
        ],
        "metrics_collection_interval": 60
      },
      "mem": {
        "measurement": [
          "mem_used_percent"
        ],
        "metrics_collection_interval": 60
      }
    }
  },
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/auth.log",
            "log_group_name": "/aws/ec2/security/auth",
            "log_stream_name": "{instance_id}"
          },
          {
            "file_path": "/var/log/syslog",
            "log_group_name": "/aws/ec2/system/syslog",
            "log_stream_name": "{instance_id}"
          }
        ]
      }
    }
  }
}
```
![Cấu hình giám sát đăng nhập](/static/images/5/5.4.5.png)
- Dùng đoạn lệnh để khởi động file vừa tạo:
```json 
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
-a fetch-config \
-m ec2 \
-c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.d/file_amazon-cloudwatch-agent.json \
-s
```
4. Gán role IAM cho máy EC2
Truy cập EC2 vừa tạo sau đó chọn **Action** > **Security** > **Modify IAM Role**
![Mở giao diện gán role](/static/images/5/5.4.6.png)
Lựa chọn IAM role vừa tạo 
![Lựa chọn role](/static/images/5/5.4.7.png)
---

## 3. Kết quả mong đợi

Sau khi hoàn thành chương này, bạn sẽ đạt được:

- Một **EC2 Unbutu** được tạo thành công tại Region `us-east-1a`.
- Cấu hình **EC2 Cloudwatch** trên máy được giám sát.
- Gán role cho máy EC2 đã tạo.
- Hoàn thành việc tạo máy giám sát đê thuận lợi cho các công việc ở chương sau.
