---
title: "Tổng quan Workshop"
date: 2026-08-24
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### Mục tiêu

Workshop này hướng dẫn triển khai giải pháp **Automated Threat Protection** (Phản ứng và ngăn chặn tấn công tự động) cho website tĩnh trên nền tảng AWS bằng cách sử dụng kiến trúc Cloud-Native Serverless, các dịch vụ bảo mật được quản lý (Managed Services) và quy trình tự động hóa ứng phó sự cố. Sau khi hoàn thành workshop, bạn sẽ có thể xây dựng một hệ thống bảo vệ ứng dụng web hoàn chỉnh với khả năng tự động phát hiện, gửi cảnh báo và chặn địa chỉ IP vi phạm ở lớp Edge mà không cần sự can thiệp thủ công.

---

## 1. Giới thiệu bài toán và giải pháp

Hiện nay, các website và ứng dụng web luôn là mục tiêu hàng đầu của các cuộc tấn công mạng tự động như HTTP Flood, Brute Force, Web Scraping hay DDoS. Khi xảy ra tấn công, quy trình phản ứng truyền thống đòi hỏi kỹ sư vận hành phải kiểm tra log thủ công, trích xuất IP độc hại và thêm vào firewall bằng tay. Quy trình này mất nhiều thời gian, dẫn đến thời gian gián đoạn dịch vụ kéo dài và gây quá tải hệ thống.

Thay vì xử lý thủ công, workshop này áp dụng giải pháp **Automated Threat Protection** dựa trên kiến trúc Serverless Security trên AWS. Website tĩnh được lưu trữ an toàn trên **Amazon S3 (Origin)** và phân phối toàn cầu thông qua CDN **Amazon CloudFront**.

Lưu lượng truy cập qua CloudFront được kiểm soát bởi **AWS WAF**. Khi có lưu lượng bất thường vượt quá ngưỡng cho phép, **Amazon CloudWatch** sẽ phát hiện và chuyển sang trạng thái báo động, gửi tín hiệu đến **Amazon SNS**. **Amazon SNS** vừa gửi email thông báo tức thời cho quản trị viên, vừa kích hoạt hàm **AWS Lambda**. Hàm Lambda tự động truy vấn **CloudWatch Logs**, bóc tách địa chỉ IP vi phạm và cập nhật trực tiếp vào **AWS WAF IP Set** để chặn truy cập vĩnh viễn ở lớp Edge.

---

## 2. Kiến trúc hệ thống

Kiến trúc giải pháp **Automated Threat Protection** được triển khai theo mô hình Serverless trên AWS (Region `us-east-1`), được chia thành 3 nhóm chức năng chính:

- **Edge Protection Layer (Lớp bảo vệ ranh giới):** Bao gồm **Amazon S3** (Origin lưu trữ website tĩnh), **Amazon CloudFront** (CDN phân phối nội dung) và **AWS WAF** (Tường lửa ứng dụng Web kết hợp WAF IP Set).
- **Monitoring & Detection Layer (Lớp giám sát & Phát hiện):** Bao gồm **CloudWatch Logs Stream** (Log Group `aws-waf-logs-cloudfront`) thu thập log truy cập từ WAF và **CloudWatch Metric Alarm** giám sát chỉ số lưu lượng bất thường.
- **Automation & Alerting Layer (Lớp tự động hóa & Cảnh báo):** Bao gồm **Amazon SNS Topic** đóng vai trò điều phối trung tâm để gửi **Email Notification** tới người quản trị và kích hoạt (**Trigger**) hàm **AWS Lambda** tự động xử lý.

**Hình 1 – Kiến trúc hệ thống Automated Threat Protection**

![Kiến trúc hệ thống](/images/Workshop/Workshop-overview/system_architecture.png)

---

## 3. Quy trình hoạt động của hệ thống

Luồng xử lý chính của hệ thống diễn ra theo các bước sau:

1. Người dùng gửi yêu cầu HTTP/HTTPS truy cập website thông qua **Amazon CloudFront Distribution**.

2. CloudFront chuyển tiếp yêu cầu đến **Amazon S3 Bucket (Origin)** để lấy nội dung website tĩnh và trả về cho người dùng.

3. **AWS WAF** kiểm tra toàn bộ lưu lượng truy cập qua CloudFront. Khi lượng request từ một IP vượt quá ngưỡng quy định, WAF kích hoạt Rate-based Rule trả về mã lỗi `403 Forbidden` và đẩy toàn bộ access log về **CloudWatch Logs**.

4. **CloudWatch Alarm** liên tục giám sát chỉ số `BlockedRequests`. Khi chỉ số này vượt ngưỡng thiết lập trong khoảng thời gian quy định, Alarm chuyển sang trạng thái _In Alarm_.

5. CloudWatch Alarm gửi ngay tín hiệu cảnh báo đến **Amazon SNS Topic** (`WAFAlertTopic`).

6. **Amazon SNS** gửi email thông báo sự cố tức thì đến hộp thư của quản trị viên (Gmail).

7. Đồng thời, **Amazon SNS** làm Trigger kích hoạt hàm **AWS Lambda** (`WAFAutoBlockFunction`) thực thi.

8. Hàm Lambda truy vấn **CloudWatch Log Group** (`aws-waf-logs-cloudfront`), lọc và trích xuất địa chỉ IP (`clientIp`) gây ra vi phạm.

9. Lambda định dạng địa chỉ IP chuẩn CIDR (IPv6/128 hoặc IPv4/32) và gọi API WAF (`UpdateIPSet`) để cập nhật IP vi phạm vào danh sách **WAF IP Set V6** (`AutoBlockedIPSetV6`).

10. AWS WAF áp dụng IP Set Rule mới, tự động chặn vĩnh viễn tất cả các yêu cầu tiếp theo từ địa chỉ IP vi phạm ngay tại lớp Edge của CloudFront.

---

## 4. Các dịch vụ được sử dụng

Workshop sử dụng các dịch vụ AWS sau:

### Lưu trữ và Phân phối

- Amazon S3 (Origin Storage)
- Amazon CloudFront (Content Delivery Network - CDN)

### An ninh và Bảo mật

- AWS WAF (Web Application Firewall - Rate-based Rule & IP Set Rule)
- AWS IAM (Identity and Access Management - Least Privilege Policy)

### Giám sát và Cảnh báo

- Amazon CloudWatch (Logs Group `aws-waf-logs-cloudfront` & Metrics Alarm)
- Amazon SNS (Simple Notification Service)

### Tính toán Serverless & Tự động hóa

- AWS Lambda (Runtime Python 3.12 với Boto3 SDK)

### Công cụ kiểm thử

- PowerShell / Bash CLI (`Invoke-WebRequest` / `curl`)

---

## 5. Kết quả đạt được

Sau khi hoàn thành workshop, bạn sẽ có thể:

- Host thành công một website tĩnh trên Amazon S3 và phân phối an toàn qua Amazon CloudFront.
- Cấu hình AWS WAF kết hợp Rate-based Rule và IP Set Rule để lọc và xử lý lưu lượng bất thường ở lớp Edge.
- Bật WAF Logging và dẫn log tập trung về Amazon CloudWatch Logs Group.
- Thiết lập CloudWatch Alarm giám sát chỉ số request bị chặn và gửi tín hiệu sang Amazon SNS.
- Khởi tạo Amazon SNS Topic để gửi email cảnh báo tự động cho quản trị viên.
- Xây dựng và phân quyền cho hàm AWS Lambda bằng Python để tự động trích xuất IP vi phạm từ CloudWatch Logs và cập nhật WAF IP Set.
- Thực thi kịch bản giả lập tấn công (HTTP Flood) để kiểm thử toàn bộ luồng chặn tự động của hệ thống.
- Thực hiện quy trình dọn dẹp tài nguyên an toàn sau khi hoàn thành workshop để tránh phát sinh chi phí.
