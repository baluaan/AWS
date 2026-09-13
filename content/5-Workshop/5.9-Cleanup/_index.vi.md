---
title: "Xóa tài nguyên hệ thống"
date: 2026-09-13
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

### Mục tiêu

Thực hiện xóa toàn bộ các tài nguyên và dịch vụ đã triển khai trên AWS theo đúng thứ tự phụ thuộc, đảm bảo không bị vướng mắc về quyền hạn, tránh gây ra lỗi liên kết dữ liệu và ngăn ngừa việc phát sinh chi phí ngoài ý muốn sau khi hoàn thành đề tài.

---

## 1. Tổng quan thứ tự xóa tài nguyên

Khi dọn dẹp hệ thống, quy tắc quan trọng nhất là **xóa ngược lại với quy trình khởi tạo**. Việc này giúp vô hiệu hóa các luồng kích hoạt tự động (Triggers) trước, sau đó mới giải phóng dữ liệu, hạ tầng mạng và cuối cùng là xóa các chính sách phân quyền IAM.

---

## 2. Thứ tự thực hiện chi tiết

### Bước 1: Amazon CloudWatch (Subscription Filter & Metric Filter)
- **Hành động:** Hủy liên kết và xóa **Subscription Filter** (`ssh`) cùng **Metric Filter** tại Log Group `/aws/ec2/security/auth`.
- **Lý do:** Dừng việc tự động đẩy dữ liệu log và kích hoạt (trigger) hàm AWS Lambda khi có sự kiện mới phát sinh.

### Bước 2: AWS Lambda (Function)
- **Hành động:** Xóa hàm Lambda (`ssh-auto-block`).
- **Lý do:** Đảm bảo không còn mã nguồn nào chạy ẩn để gọi API truy vấn DynamoDB, chèn quy tắc chặn vào Network ACL hoặc phát tin nhắn sang SNS.

### Bước 3: Amazon DynamoDB (Table)
- **Hành động:** Xóa bảng bộ đếm (`SSHBruteforceCounter`).
- **Lý do:** Giải phóng bảng lưu trữ trạng thái bộ đếm sau khi Lambda đã bị loại bỏ hoàn toàn.

### Bước 4: Amazon Network ACL (NACL Rules)
- **Hành động:** Gỡ bỏ các quy tắc `DENY` đã được tự động chèn hoặc gỡ liên kết Subnet (nếu tạo NACL riêng `SSH-Auto-Block-NACL`).
- **Lý do:** Khôi phục lại trạng thái cấu hình mạng ban đầu cho Subnet.

### Bước 5: Amazon SNS (Topic & Subscription)
- **Hành động:** Xóa **Email Subscription** và **SNS Topic**.
- **Lý do:** Hủy kênh nhận thông báo cảnh báo sự cố qua Email.

### Bước 6: Amazon CloudWatch (Log Group)
- **Hành động:** Xóa Log Group `/aws/ec2/security/auth` và `/aws/ec2/system/syslog`.
- **Lý do:** Dọn dẹp toàn bộ dữ liệu nhật ký hệ thống đã lưu trữ trên đám mây.

### Bước 7: Amazon EC2 (Instance)
- **Hành động:** Thực hiện **Terminate** máy chủ EC2 Ubuntu (`AWS-Security-Monitoring`).
- **Lý do:** Thu hồi máy chủ mục tiêu và giải phóng địa chỉ IP động cũng như dung lượng ổ cứng EBS liên quan.

### Bước 8: AWS IAM (Roles & Policies)
- **Hành động:** Xóa các Inline Policy và IAM Role (`Lambda-SSH-AutoBlock-Role`, `EC2-CloudWatchAgent-Role`).
- **Lý do:** **Xóa cuối cùng.** Nếu xóa IAM Role trước, các dịch vụ đang chạy có thể bị lỗi do mất quyền truy cập trong quá trình hủy tài nguyên.

---
## 3. kết quả mong đợi

- Toàn bộ dịch vụ trong đề tài gồm (AWS CloudWatch, AWS Lambda, AWS DynamoDB, NACL, AWS SNS, AWS EC2, IAM).
- Đảm bảo không còn chi phí phát sinh khi không sử dụng

- > **Lưu ý:** Việc xóa tài nguyên là vĩnh viễn và không thể khôi phục.