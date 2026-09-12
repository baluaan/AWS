---
title: "Nhật ký công việc"
date: 2026-07-18
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

<!-- # Nhật ký công việc -->

Phần này ghi lại toàn bộ quá trình thực tập kéo dài **8 tuần**, được triển khai theo một lộ trình học tập và thực hành có hệ thống — từ việc xây dựng nền tảng vững chắc về các dịch vụ AWS cốt lõi, nâng cao năng lực bảo mật hạ tầng đám mây, cho đến khi thiết kế, triển khai và đánh giá thành công một giải pháp tự động hóa an toàn thông tin hoàn chỉnh.

Trong **5 tuần đầu**, tôi tập trung nghiên cứu chuyên sâu về kiến trúc mạng và bảo mật trên AWS, bao gồm các dịch vụ trọng yếu như IAM, VPC, Transit Gateway, Hybrid DNS, Site-to-Site VPN, S3 Cross-Region Replication (CRR), AWS Security Hub, quản trị dữ liệu và tự động hóa quy trình với AWS Lambda. Đây là giai đoạn tích lũy kiến thức nền tảng, đóng vai trò quan trọng trong việc chuẩn bị cho đề tài thực tập chính thức.

Trong **tuần 6 và tuần 7**, tôi mở rộng năng lực hạ tầng bằng việc nghiên cứu các giải pháp chịu lỗi, cân bằng tải và container hóa ứng dụng. Tôi đã triển khai thành công Application Load Balancer (ALB) kết hợp Auto Scaling Group (ASG) để đảm bảo tính sẵn sàng cao (High Availability), đồng thời làm chủ công nghệ Docker (Dockerfile, ECR) và điều phối container trên môi trường Serverless với Amazon ECS Fargate.

Tại **tuần 8**, tôi hoàn thiện và nghiệm thu thành công Đề tài thực tập tổng kết: **"Hệ thống tự động giám sát, phát hiện và phản ứng sự cố tấn công SSH Brute-Force trên máy chủ EC2"**. Đề tài được xây dựng bằng cách kết hợp các dịch vụ Cloud-Native bao gồm **Amazon EC2, Amazon CloudWatch Logs & Metric Filters, Amazon CloudWatch Alarms, AWS Lambda (Python/boto3), AWS DynamoDB** và **Amazon SNS**. Hệ thống có khả năng tự động phân tích log hệ thống, phát hiện các chuỗi truy cập SSH thất bại bất thường, lập tức kích hoạt Lambda để cô lập IP tấn công thông qua Security Group/NACL và gửi cảnh báo thời gian thực tới quản trị viên.

Nội dung chi tiết của từng tuần được trình bày như sau:

**Tuần 1:** [Tổng quan AWS: Thiết lập tài khoản, Bảo mật IAM & Quản lý ngân sách Budgets](1.1-week1/)

**Tuần 2:** [AWS Core Services: Chuyên sâu IAM, Lưu trữ Amazon S3 & Máy chủ ảo EC2](1.2-week2/)

**Tuần 3:** [AWS Networking: Mạng ảo Custom VPC, Subnets, Gateways & Bảo mật SG/NACLs](1.3-week3/)

**Tuần 4:** [Mô hình Serverless: AWS Lambda, Boto3 SDK & Tự động hóa lịch trình EC2](1.4-week4/)

**Tuần 5:** [AWS Observability: Giám sát CloudWatch, Cảnh báo SNS & Kiểm vết CloudTrail](1.5-week5/)

**Tuần 6:** [High Availability: Cân bằng tải ELB/ALB & Tự động co giãn Auto Scaling Group](1.6-week6/)

**Tuần 7:** [Containerization: Đóng gói Docker, Lưu trữ ECR & Triển khai AWS ECS Fargate](1.7-week7/)

**Tuần 8:** [Dự án Tổng kết: Triển khai hệ thống tự động giám sát và chặn tấn công SSH trên máy chủ EC2](1.8-week8/)