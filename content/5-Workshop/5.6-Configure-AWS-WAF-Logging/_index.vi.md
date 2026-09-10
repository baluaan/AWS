---
title: "Cấu hình AWS WAF & Logging"
date: 2026-08-24
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

### Mục tiêu

Xây dựng tường lửa ứng dụng web (AWS WAF) tại lớp Edge để bảo vệ Amazon CloudFront Distribution, thiết lập các quy tắc kiểm soát lưu lượng (IP Set & Rate-based Rule) và cấu hình ghi nhật ký (Logging) tập trung sang Amazon CloudWatch Logs.

---

## 1. Tổng quan

Lớp bảo mật tường lửa (WAF & Logging Layer) là thành phần cốt lõi đảm bảo an toàn cho hạ tầng ứng dụng trước các mối đe dọa từ Internet. Trong hệ thống này, **AWS WAF (Web Application Firewall)** được gắn trực tiếp vào Amazon CloudFront Distribution để kiểm tra toàn bộ các request HTTP/HTTPS đi vào trước khi tới S3 Origin.

Để phòng chống tấn công từ chối dịch vụ (HTTP Flood/DDoS) và chuẩn bị dữ liệu cho quy trình tự động hóa, chúng ta sẽ cấu hình 3 thành phần chính:

- **IP Set (`AutoBlockedIPSetV6`):** Khởi tạo danh sách IP trống ban đầu làm nơi lưu trữ các địa chỉ IP vi phạm do AWS Lambda tự động cập nhật.
- **Web ACL (`WebsiteProtectionACL`):** Khởi tạo tường lửa chính, liên kết với CloudFront Distribution và thiết lập các quy tắc kiểm soát (Rate-based Rule để giới hạn request và IP Set Rule để chặn IP vi phạm).
- **WAF Access Logging (`aws-waf-logs-cloudfront`):** Kích hoạt tính năng đẩy toàn bộ nhật ký truy cập WAF về Amazon CloudWatch Logs làm nguồn dữ liệu đầu vào cho hệ thống phân tích và tự động hóa.

---

## 2. Quy trình triển khai

Quy trình cấu hình AWS WAF và Logging được chia thành 3 phần bài học riêng biệt:

- **Bài 5.6.1:** Khởi tạo WAF IP Set (`AutoBlockedIPSetV6`) tại Scope CloudFront (`us-east-1`).
- **Bài 5.6.2:** Tạo Web ACL (`WebsiteProtectionACL`), thiết lập quy tắc Rate-based Rule, gắn IP Set và liên kết bảo vệ CloudFront Distribution.
- **Bài 5.6.3:** Khởi tạo CloudWatch Log Group `aws-waf-logs-cloudfront` và kích hoạt WAF Access Logging.

---

## 3. Nội dung thực hành

Thực hiện lần lượt các bài thực hành sau:

- **[5.6.1. Tạo WAF IP Set](5.6.1-Create-WAF-IP-Set)**
- **[5.6.2. Tạo và cấu hình Web ACL](5.6.2-Create-and-configure-Web-ACL)**
- **[5.6.3. Cấu hình WAF Access Logging sang CloudWatch Logs](5.6.3-Configure-WAF-Logging-CloudWatch)**

---

## 4. Kết quả mong đợi

Sau khi hoàn thành chương này, bạn sẽ đạt được:

- **WAF IP Set** (`AutoBlockedIPSetV6`) được khởi tạo thành công tại Scope `CLOUDFRONT`.
- **Web ACL** (`WebsiteProtectionACL`) được cấu hình đầy đủ các quy tắc bảo vệ và đã gắn thành công vào CloudFront Distribution.
- **WAF Access Logs** được cấu hình ghi nhận tự động và đẩy toàn bộ nhật ký về **CloudWatch Log Group** chuẩn tên `aws-waf-logs-cloudfront`.
- Hạ tầng tường lửa và ghi log sẵn sàng cho việc thiết lập CloudWatch Alarms và hàm Lambda tự động hóa ở các chương sau.
