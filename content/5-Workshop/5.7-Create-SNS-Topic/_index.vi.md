---
title: "Khởi tạo Amazon SNS Topic"
date: 2026-08-24
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

### Mục tiêu

Khởi tạo dịch vụ Amazon Simple Notification Service (Amazon SNS) để làm kênh thông báo trung gian, tiếp nhận cảnh báo từ CloudWatch Alarms và gửi thông điệp tự động tới email của quản trị viên cũng như kích hoạt hàm AWS Lambda phản ứng sự cố.

---

## 1. Tổng quan

Trong kiến trúc bảo mật tự động hóa, **Amazon SNS (Simple Notification Service)** đóng vai trò là một Pub/Sub Messaging Service trung tâm. Khi hệ thống phát hiện lưu lượng truy cập bất thường vượt ngưỡng thiết lập, CloudWatch Alarm sẽ phát tin nhắn cảnh báo (Publish) tới SNS Topic.

Lúc này, **Amazon SNS Topic** sẽ chịu trách nhiệm phân phối thông điệp đồng thời đến hai nhóm nhận (Subscribers):

- **Email Subscription:** Gửi cảnh báo tức thì qua Email để thông báo cho đội ngũ QTV/DevOps.
- **AWS Lambda Subscription:** Trigger trực tiếp hàm Python Lambda để thực thi quy trình phân tích log và tự động chặn IP vi phạm.

---

## 2. Quy trình triển khai

Quy trình cấu hình Amazon SNS Topic được chia thành hai bài thực hành riêng biệt:

- **Bài 5.7.1:** Khởi tạo Amazon SNS Topic (`WAFAlertTopic`) tại Region `us-east-1 (N. Virginia)`.
- **Bài 5.7.2:** Đăng ký nhận thông báo (Subscription) qua địa chỉ Email cá nhân và xác thực liên kết (Confirm Subscription).

---

## 3. Nội dung thực hành

Thực hiện lần lượt các bài thực hành sau:

- **[5.7.1. Khởi tạo SNS Topic](5.7.1-Create-SNS-Topic)**
- **[5.7.2. Tạo và xác thực Email Subscription](5.7.2-Create-Email-Subscription)**

---

## 4. Kết quả mong đợi

Sau khi hoàn thành chương này, bạn sẽ đạt được:

- **Amazon SNS Topic** có tên `WAFAlertTopic` được khởi tạo thành công tại Region `us-east-1`.
- **Email Subscription** được liên kết thành công và ở trạng thái **Confirmed**.
- Kênh truyền thông báo sẵn sàng để tích hợp với CloudWatch Alarms và AWS Lambda ở các bài tiếp theo.
