---
title: "Workshop"
date: 2026-08-24
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Triển Khai Bảo Vệ Static Website Và Tự Động Chặn IP Vi Phạm Với AWS WAF & Lambda

#### Tổng quan

Trong workshop này, chúng ta sẽ xây dựng và triển khai giải pháp **Automated Threat Protection** (Phản ứng và ngăn chặn tấn công tự động) cho website tĩnh theo kiến trúc Cloud-Native Serverless trên hạ tầng AWS.

Giải pháp sử dụng các dịch vụ cốt lõi của AWS bao gồm **Amazon S3**, **Amazon CloudFront**, **AWS WAF**, **Amazon CloudWatch**, **Amazon SNS**, **AWS Lambda** và **AWS IAM** nhằm thiết lập cơ chế giám sát lưu lượng, phát hiện hành vi truy cập bất thường (như HTTP Flood / Rate-limit breach) và tự động cập nhật danh sách IP đen (WAF IP Set V6) ở lớp Edge mà không cần sự can thiệp thủ công từ quản trị viên.

Trong suốt bài workshop này, bạn sẽ thực hành trọn vẹn quy trình triển khai: từ chuẩn bị nền tảng dự án, cấu hình hạ tầng lưu trữ S3 & CDN CloudFront, thiết lập các bộ quy tắc bảo mật WAF Web ACL, cài đặt kênh thông báo SNS, lập trình tự động hóa với AWS Lambda (Python 3.12), đến việc cấu hình CloudWatch Alarm, thực thi kịch bản giả lập tấn công kiểm thử thực tế và dọn dẹp an toàn tài nguyên sau thử nghiệm.

#### Nội dung

1. [Tổng quan Workshop](5.1-Workshop-overview/)
2. [Điều kiện chuẩn bị](5.2-Prerequisite/)
3. [Chuẩn bị dự án](5.3-Project-foundation/)
4. [Host website tĩnh trên Amazon S3](5.4-Host-Static-Website-S3/)
5. [Phân phối dữ liệu qua Amazon CloudFront](5.5-Distribute-via-CloudFront/)
6. [Cấu hình AWS WAF & Logging](5.6-Configure-AWS-WAF-Logging/)
7. [Khởi tạo Amazon SNS Topic](5.7-Create-SNS-Topic/)
8. [Tạo IAM Role & Hàm AWS Lambda](5.8-Create-IAM-Role-Lambda/)
9. [Cấu hình CloudWatch Alarm](5.9-Configure-CloudWatch-Alarm/)
10. [Kiểm thử hệ thống](5.10-Testing/)
11. [Dọn dẹp tài nguyên](5.11-Cleanup/)
