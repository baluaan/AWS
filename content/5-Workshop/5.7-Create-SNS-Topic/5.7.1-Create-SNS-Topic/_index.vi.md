---
title: "Khởi tạo SNS Topic"
date: 2026-08-24
weight: 1
chapter: false
pre: " <b> 5.7.1. </b> "
---

Bài viết này hướng dẫn chi tiết các bước khởi tạo một Amazon Simple Notification Service (Amazon SNS) Topic trên AWS Management Console. SNS Topic này đóng vai trò làm kênh truyền thông báo trung gian để tiếp nhận cảnh báo từ CloudWatch Alarms.

---

## 1. Khởi tạo Amazon SNS Topic

### Bước 1: Truy cập dịch vụ Amazon SNS

1. Đăng nhập vào **AWS Management Console**.
2. Trên thanh tìm kiếm, nhập `SNS` và chọn dịch vụ **Simple Notification Service**.
3. Tại menu điều hướng bên trái, chọn **Topics**.
4. Đảm bảo Region đang làm việc là **US East (N. Virginia) us-east-1**.

![Truy cập Amazon SNS Topics](/images/Workshop/5.7-SNS/5.7.1-sns-topics-menu.png)

---

### Bước 2: Thiết lập thông số Topic

1. Tại giao diện Topics, nhấn nút **Create topic**.
2. **Type:** Chọn loại **Standard** (phù hợp cho việc gửi thông báo qua Email và kích hoạt Lambda function với tốc độ phản hồi cao).
3. **Name:** Nhập `WAFAlertTopic`.
4. **Display name:** Nhập `WAF Alert Topic` (Tên này sẽ hiển thị ở phần tiêu đề/người gửi trong email thông báo gửi về cho quản trị viên).

![Cấu hình thông số SNS Topic](/images/Workshop/5.7-SNS/5.7.1-configure-sns-topic.png)

---

### Bước 3: Hoàn tất khởi tạo

1. Kéo xuống cuối trang và giữ nguyên các tham số cấu hình nâng cao mặc định.
2. Nhấn nút **Create topic**.

![Nhấn Create Topic](/images/Workshop/5.7-SNS/5.7.1-finish-create-topic.png)

---

## 2. Kiểm tra thông tin SNS Topic

Sau khi khởi tạo thành công, giao diện chi tiết của Topic sẽ hiển thị các thông số quan trọng:

- **Name:** `WAFAlertTopic`
- **ARN:** `arn:aws:sns:us-east-1:<ACCOUNT_ID>:WAFAlertTopic`
- **Type:** Standard

> **Lưu ý:** Bạn nên sao chép chuỗi **ARN** của SNS Topic này để sử dụng cấu hình cho CloudWatch Alarms ở bài 5.8 và phân quyền IAM Policy ở các bài sau.

![Kiểm tra thông tin SNS Topic ARN](/images/Workshop/5.7-SNS/5.7.1-verify-sns-topic.png)

---

## 3. Kết quả mong đợi

Sau khi hoàn thành bài thực hành này:

- **Amazon SNS Topic** có tên `WAFAlertTopic` được khởi tạo thành công tại Region `us-east-1` với kiểu `Standard`.
- Chuỗi **ARN** của Topic được khởi tạo sẵn sàng cho việc đăng ký Email Subscription ở bài 5.7.2 và liên kết với CloudWatch Alarms ở các chương tiếp theo.
