---
title: "Worklog - Tuần 2"
date: 2026-08-10
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:

- Nghiên cứu chuyên sâu bộ dịch vụ nòng cốt (AWS Core Services): IAM, S3, EC2.
- Làm chủ quản lý truy cập và bảo mật nâng cao với IAM (Users, Groups, Roles, Policies, MFA).
- Hiểu rõ cơ chế lưu trữ đối tượng với Amazon S3 (Bucket, Storage Classes, Bucket Policy, Lifecycle Rules).
- Nắm vững máy chủ ảo Amazon EC2 (Instance Types, Key Pairs, Security Groups, EBS) và thực hành triển khai Web Server cơ bản.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu nâng cao về IAM: Phân biệt IAM User, IAM Group, IAM Role và IAM Policy (JSON syntax).<br>- Thực hành tạo User mới, gán quyền hạn chế, thiết lập URL đăng nhập tùy chỉnh (Alias) và kiểm tra log đăng nhập. | 10/08/2026 | 10/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| 3 | - Tìm hiểu dịch vụ lưu trữ đối tượng Amazon S3: Khái niệm S3 Bucket, Object Key, Storage Classes (Standard, IA, Glacier).<br>- Thực hành tạo Bucket, upload/download dữ liệu, cấu hình Public Access và S3 Bucket Policy. | 11/08/2026 | 11/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| 4 | - Tìm hiểu Amazon EC2: Kiến trúc Instance Type (t2.micro, t3.micro), AMI (Amazon Machine Image), Key Pairs, EBS Volume.<br>- Thực hành khởi tạo một EC2 Instance chạy hệ điều hành Amazon Linux 2 / Ubuntu. | 12/08/2026 | 12/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| 5 | - Cấu hình Security Group cho EC2: Mở các cổng HTTP (80), HTTPS (443), SSH (22).<br>- Kết nối SSH vào EC2 instance sử dụng PuTTY / Terminal với Key Pair (.pem / .ppk).<br>- Cài đặt Apache/Nginx web server đơn giản trên EC2 để kiểm tra kết nối từ Internet. | 13/08/2026 | 13/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| 6 | - Cấu hình S3 Static Website Hosting tích hợp với EC2.<br>- Đánh giá, tổng kết bài thực hành Lab AWS Core Services và dọn dẹp các tài nguyên EC2/S3 đã tạo. | 14/08/2026 | 14/08/2026 | https://cloudjourney.awsstudygroup.com/ |

### Kết quả đạt được tuần 2:

- Thành thạo phân quyền người dùng trong IAM: Tạo thành công User, Group, cấp quyền chuẩn Least Privilege, tự tạo được IAM Role gắn cho EC2 Instance.
- Hiểu sâu về lưu trữ S3: Đã tạo S3 Bucket, cấu hình phân lớp dữ liệu (Storage Class), cài đặt thành công S3 Static Website Hosting.
- Khởi tạo và quản lý thành công máy chủ EC2: Biết cách chọn AMI, Instance Type, tạo Key Pair và cấu hình Security Group chuẩn an toàn.
- Hoàn thành bài Lab thực hành: Cài đặt thành công Web Server (Nginx/Apache) trên EC2, SSH kết nối máy chủ từ xa an toàn và hiển thị trang web thành công.