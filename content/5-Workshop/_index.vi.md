---
title: "Workshop"
date: 2026-09-11
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Triển Khai Giám Sát, Phát Hiện Và Tự Động Ngăn Chặn Tấn Công SSH Trên AWS

#### Tổng quan

Trong workshop này, chúng ta sẽ xây dựng và triển khai giải pháp **AWS SSH Automated Threat Protection** (Phản ứng và tự động ngăn chặn tấn công SSH) cho máy chủ EC2 Linux theo kiến trúc Cloud-Native Serverless trên hạ tầng AWS.

Giải pháp sử dụng các dịch vụ cốt lõi của AWS bao gồm **Amazon EC2 (Ubuntu)**, **Amazon CloudWatch Logs**, **Subscription Filter**, **AWS Lambda**, **Amazon DynamoDB**, **Amazon SNS**, **Network ACL (NACL)** và **AWS IAM** nhằm thiết lập cơ chế giám sát lưu lượng đăng nhập SSH, phát hiện hành vi truy cập bất thường (Brute-Force SSH) và tự động gửi cảnh báo qua email cũng như tạo quy tắc chặn (`DENY`) địa chỉ IP độc hại (`/32`) ở lớp mạng Subnet mà không cần sự can thiệp thủ công từ quản trị viên.

Trong suốt bài workshop này, bạn sẽ thực hành trọn vẹn quy trình triển khai: từ chuẩn bị nền tảng dự án, cấu hình máy chủ EC2 Ubuntu & đẩy log đăng nhập hệ thống (`/var/log/auth.log`) về CloudWatch Logs, cấu hình bảng DynamoDB theo dõi số lần đăng nhập thất bại trong cửa sổ thời gian 1 phút, lập trình hàm AWS Lambda (Python 3.12) xử lý giải mã & cập nhật Network ACL, gửi email thông báo qua Amazon SNS, đến việc thực thi kịch bản giả lập tấn công kiểm thử thực tế và dọn dẹp an toàn tài nguyên sau thử nghiệm.

#### Nội dung

1. [Tổng quan Workshop](5.1-Workshop-overview/)
2. [Điều kiện chuẩn bị](5.2-Prerequisite/)
3. [Chuẩn bị dự án](5.3-Project-foundation/)
4. [Khởi tạo máy chủ EC2 Ubuntu & Cấu hình SSH](5.4-Launch-EC2-Ubuntu/)
5. [Cấu hình CloudWatch Agent & Thu thập Log `/var/log/auth.log`](5.5-Configure-CloudWatch/)
6. [Khởi tạo Amazon Topic Amazon SNS](5.6-Create-SNS/)
7. [Khởi tạo tự động chặn IP](5.7-Create-Automation/)
8. [Kiểm thử hệ thống & Ngăn chặn IP tự động tại Subnet NACL](5.8-Testing-Automation/)
9. [Dọn dẹp tài nguyên](5.9-Cleanup/)