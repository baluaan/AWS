---
title: "Cấu hình CloudWatch Agent"
date: 2026-09-11
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Mục tiêu

Cấu hình và vận hành **Amazon CloudWatch Agent** trên máy chủ EC2 Ubuntu để thu thập, định dạng và tự động đẩy nhật ký hệ thống (`/var/log/auth.log`) về **CloudWatch Logs Group**, tạo tiền đề dữ liệu cho các bước phân tích và phản ứng tự động.

---

## 1. Tổng quan

Tầng thu thập nhật ký (Log Collection Layer) giữ vai trò là mắt xích khởi đầu cho toàn bộ quy trình phản ứng sự cố tự động. Trong kiến trúc này, **Amazon CloudWatch Agent** đóng vai trò là một trình thu thập dữ liệu (Daemon/Agent) chạy ngầm trên máy chủ EC2 Ubuntu, trực tiếp theo dõi các thay đổi của tệp nhật ký hệ thống `/var/log/auth.log`.

Việc cấu hình CloudWatch Agent mang lại các lợi ích quan trọng:
- **Thu thập thời gian thực (Real-time Streaming):** Tự động đẩy từng dòng log phát sinh từ dịch vụ SSH (`sshd`) về dịch vụ CloudWatch Logs của AWS với độ trễ cực thấp.
- **Tập trung hóa quản lý nhật ký:** Gom toàn bộ sự kiện truy cập và đăng nhập thất bại của máy chủ về **CloudWatch Log Group** `/aws/ec2/security/auth`, giúp việc truy vấn, lọc thông tin và thiết lập **Subscription Filter** và **Metric filters**.
- **Phân tách trách nhiệm:** Đảm bảo dữ liệu log vẫn được lưu giữ an toàn trên đám mây ngay cả khi máy chủ EC2 bị gián đoạn hoặc bị tấn công từ bên ngoài.

---

## 2. Quy trình triển khai

1. Triển khai **Metric filters** theo các các bước sau:
Truy cập vào **CloudWatch** > **Log management** > `/aws/ec2/security/auth` > **Metric filter** > **Create metric filter**
![Truy cập vào Metric filter](/static/images/5/5/5.5.2.png)
Điền các thông tin:
 - Filter pattern: Failed password
 - Name: SSHBruteForceDetection
Điền các thông tin như hình ảnh
![Cấu hình](/static/images/5/5/5.5.1.png)
2. Triển khai **Subscription filters** theo các các bước sau:
Truy cập vào **CloudWatch** > **Log management** > `/aws/ec2/security/auth` > **Subscription filters** > **Create** > ` Create Lambda subcription filter`
![Truy cập vào cấu hình](/static/images/5/5/1.png)
Thực hiện cấu hình:
 - 


---

## 3. Kết quả mong đợi

Sau khi hoàn thành chương này, bạn sẽ đạt được:


