---
title: "Điều kiện chuẩn bị"
date: 2026-09-11
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

### Mục tiêu

Đảm bảo người thực hiện có đầy đủ quyền truy cập vào AWS Management Console, chuẩn bị môi trường máy chủ kiểm thử (Amazon EC2 Ubuntu), cấu hình công cụ giả lập tấn công SSH Brute Force và sẵn sàng các tài nguyên mã nguồn hàm Lambda xử lý tự động.

---

## 1. Công cụ và tài nguyên cần chuẩn bị

Hệ thống phản ứng tự động này thao tác chính trên **AWS Management Console (Web UI)** kết hợp với máy trạm và máy chủ EC2 để kiểm thử thực tế. Bạn cần chuẩn bị các yếu tố sau:

- **Tài khoản AWS (AWS Account):** Có quyền khởi tạo và quản lý các dịch vụ Amazon EC2, CloudWatch Logs, AWS Lambda, Amazon DynamoDB, Amazon SNS, VPC (Network ACL) và IAM (khuyên dùng quyền "AdministratorAccess").
- **Máy chủ mục tiêu (Amazon EC2):** 01 Instance chạy hệ điều hành **Ubuntu Server 22.04 LTS / 24.04 LTS** đóng vai trò là máy chủ SSH bị tấn công.
- **Trình duyệt Web (Web Browser):** Google Chrome, Microsoft Edge hoặc Mozilla Firefox bản mới nhất.
- **Công cụ giả lập tấn công SSH (Terminal / PowerShell / Hydra):** Dùng để thực hiện các lượt đăng nhập SSH sai mật khẩu liên tục.
  - Đối với Windows: **PowerShell** / **PuTTY** / **MobaXterm** hoặc **Hydra** (nếu kiểm thử tự động).
  - Đối với macOS/Linux: **Terminal** (sử dụng lệnh "ssh" hoặc công cụ "hydra").
- **Trình chỉnh sửa mã nguồn (Code Editor):** Visual Studio Code hoặc Notepad++ để chỉnh sửa kịch bản Lambda ("lambda_function.py") xử lý log SSH và cập nhật DynamoDB/NACL.
- **Địa chỉ Email (Gmail):** Dùng để đăng ký nhận thông báo cảnh báo sự cố chặn IP từ Amazon SNS Topic.

---

## 2. Các bước chuẩn bị chi tiết

### Bước 1: Đăng nhập AWS Console và chọn Region làm việc

1. Đăng nhập vào [AWS Management Console](https://aws.amazon.com/console/).
2. Chọn khu vực (Region) triển khai hệ thống (ví dụ: **Asia Pacific (Singapore) — ap-southeast-1** hoặc **US East (N. Virginia) — us-east-1**) ở góc trên bên phải màn hình Console.

> **LƯU Ý QUAN TRỌNG:** Tất cả các tài nguyên bao gồm EC2 Instance, CloudWatch Logs Group, Lambda Function, DynamoDB Table, SNS Topic và Network ACL phải được tạo **cùng một Region và trên cùng một VPC/Subnet** để đảm bảo khả năng liên kết và phản ứng chính xác.

**Checkpoint:** Tên Region trên thanh công cụ hiển thị thống nhất (ví dụ: **Asia Pacific (Singapore) ap-southeast-1**).

---

### Bước 2: Chuẩn bị máy chủ EC2 Ubuntu và cấu hình CloudWatch Agent

1. Khởi tạo một EC2 Instance Ubuntu (ví dụ: "t2.micro" hoặc "t3.micro").
2. Gán **IAM Role** cho EC2 Instance có chính sách "CloudWatchAgentServerPolicy" để phép đẩy log hệ thống.
3. Cài đặt **CloudWatch Agent** lên EC2 để tự động theo dõi và đẩy dữ liệu file log "/var/log/auth.log" về CloudWatch Logs Group.

Kiểm tra file log SSH trên máy chủ EC2 bằng lệnh: "sudo tail -f /var/log/auth.log"

**Checkpoint:** File "/var/log/auth.log" ghi nhận đầy đủ các sự kiện SSH thành công và thất bại ("Failed password").

---

### Bước 3: Kiểm tra công cụ giả lập tấn công SSH trên máy cục bộ

Mở Terminal (macOS/Linux) hoặc PowerShell (Windows) để kiểm tra khả năng kết nối SSH tới máy chủ EC2:

Kiểm tra lệnh SSH căn bản bằng cách chạy lệnh: "ssh invalid_user@<EC2_PUBLIC_IP>"

Hoặc sử dụng công cụ Hydra để giả lập tấn công Brute Force tự động bằng lệnh: "hydra -l admin -P passwords.txt <EC2_PUBLIC_IP> ssh -t 4"

**Checkpoint:** Lệnh SSH bị từ chối do sai mật khẩu và EC2 ghi nhận dòng log "Failed password for ..." vào "/var/log/auth.log".

---

### Bước 4: Chuẩn bị tệp mã nguồn xử lý sự cố (AWS Lambda)

Chuẩn bị tệp mã nguồn Python ("lambda_function.py") trên máy cục bộ với các chức năng chính:
- Giải mã và giải nén payload dữ liệu gửi từ **CloudWatch Subscription Filter**.
- Sử dụng **Regex** bóc tách địa chỉ IP nguồn ("clientIp").
- Ghi nhận và tăng bộ đếm thất bại theo mốc thời gian trong **Amazon DynamoDB**.
- Kiểm tra ngưỡng vi phạm (≥ 5 lần/1 phút): gửi thông báo qua **Amazon SNS** và tự động thêm quy tắc "DENY" CIDR "/32" vào **Network ACL**.

**Checkpoint:** Mã nguồn Python Lambda đã hoàn thiện, sẵn sàng tải lên dịch vụ AWS Lambda.

---

## 3. Kết quả mong đợi

Sau khi hoàn thành chương này, bạn sẽ đạt được các điều kiện:

- Đăng nhập thành công vào AWS Management Console tại Region đã chọn.
- Máy chủ EC2 Ubuntu đã hoạt động, tích hợp CloudWatch Agent và ghi nhận log SSH "/var/log/auth.log".
- Môi trường máy trạm sẵn sàng thực hiện các lượt giả lập tấn công SSH Brute Force.
- Sẵn sàng địa chỉ Email nhận thông báo từ Amazon SNS.
- Mã nguồn xử lý Lambda (Python) đã sẵn sàng để triển khai và liên kết với DynamoDB & Network ACL.