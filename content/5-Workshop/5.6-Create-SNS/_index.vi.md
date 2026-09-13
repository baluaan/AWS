---
title: "Triển khai SNS "
date: 2026-09-11
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

### Mục tiêu

Triển khai AWS SNS để cảnh báo kịp thời ngay khi hàm AWS Lambda phát hiện một IP nguồn vượt ngưỡng tấn công (≥ 5 lần thất bại/1 phút) và thực hiện chặn IP, SNS sẽ lập tức gửi email thông báo sự cố đến quản trị viên.

---
## 1. Tổng quan

Amazon SNS là dịch vụ quản lý tin nhắn theo mô hình Publish/Subscribe (Pub/Sub) được AWS cung cấp đầy đủ và có khả năng mở rộng cao. Dịch vụ này cho phép phân tách (decouple) các Publishers với các Subscribers.
Trong đề tài này, AWS SNS sẽ phát cảnh báo thời gian thực về mail khi có lượt đăng nhập "Failed password" vượt ngưỡng 5 lần / phút.

---
## 2. Quy trình triển khai

1. Tạo **CloudWatch alarm**
Truy cập **CloudWatch** > **Arlarm** > **Create arlarm**
Nhập các thông tin ở **Step 1**:
  - Namespace: SecurityMonitoring
  - Metric name: SSHBruteForce
  - Stastistic: Sum
  - Period: 1
  - Đặt ngưỡng: 5 lần/ phút
  - Chọn: Next
![Step1](/images/5/6/1.png)
Tiếp theo tới **Step 2**:
  - Giữ mặc định tất cả
  - Send a notification: SSH-Attack-Alerts
  - email: caphonglon2004@gmail.com
  - Chọn: Next
![Step2](/images/5/6/2.png)
Cuối cùng tới **Step 3**:
  - Name: SSH-BruteForce-Detected
  - Chọn: Next
  - CHọn: Create
![Step3](/images/5/6/3.png)

2. Tạo **AWS SNS**
Truy cập **Amazon SNS** > **Topics** > **Create topic**
Nhập các thông tin:
  - Type: Standard
  - Name: SSH-Attack-Alerts
  - Display name: canh-bao
  - Chọn: Create
![Create Topic](/images/5/6/4.png)
3. Tạo **Subscriptions**
Truy cập **Amazon SNS** > **Subscriptions** > **Create Subscriptions**
Điền các thông tin: 
  - Topic ARN: chọn cái vừa tạo
  - Protocol: Email
  - Email: caphonglon2004@gmail.com
  - Chọn: Create
![Create subcription](/images/5/6/5.png)


## 3. Sau khi hoàn thành chương này, bạn sẽ đạt được:
- Giám sát thời gian thực giúp quản trị viên nắm bắt ngay lập tức các cuộc tấn công Brute Force nguy hiểm mà không cần phải chủ động ngồi túc trực đọc log hay kiểm tra AWS Console thủ công.
- Nhận được cảnh báo qua mail khi có lượt đăng nhập "Failed password" vượt 5 lần/ phút.


