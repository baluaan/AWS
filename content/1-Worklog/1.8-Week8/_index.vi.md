---
title: "Worklog - Tuần 8"
date: 2026-09-13
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8:

- Tổng hợp và kết nối toàn bộ kiến thức AWS Cloud đã học qua 7 tuần thực tập.
- Đánh giá và áp dụng mô hình chuẩn 5 trụ cột của kiến trúc AWS Well-Architected Framework vào dự án thực hành.
- Rà soát, tối ưu hóa bảo mật và kiểm soát chi phí toàn bộ các tài nguyên đã khởi tạo.
- Hoàn thiện báo cáo tổng kết kỳ thực tập AWS và dọn dẹp sạch sẽ tài nguyên trên AWS Console tránh phát sinh chi phí ngoài ý muốn.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Nghiên cứu tài liệu AWS Well-Architected Framework: 5 trụ cột cốt lõi (Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization).<br>- Đánh giá lại sơ đồ kiến trúc các bài Lab đã làm theo tiêu chuẩn AWS Well-Architected. | 21/09/2026 | 21/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| 3 | - Kiểm tra và tối ưu hóa bảo mật: Sử dụng AWS Trusted Advisor / IAM Access Analyzer để rà soát các Security Group bị mở sai quy định, kiểm tra Root Account và cấp quyền IAM. | 22/09/2026 | 22/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| 4 | - Tối ưu hóa chi phí: Rà soát AWS Cost Explorer, kiểm tra các tài nguyên dư thừa (EBS Volumes không gắn vào máy chủ, Elastic IP không dùng, NAT Gateway nhàn rỗi). | 23/09/2026 | 23/09/2026 | Ứng dụng AWS Cost Explorer |
| 5 | - Tổng hợp toàn bộ tài liệu Worklog, sơ đồ kiến trúc, mã nguồn Lambda/Dockerfile và kết quả thực hành của 8 tuần.<br>- Đóng gói báo cáo tổng kết kỳ thực tập AWS Cloud. | 24/09/2026 | 24/09/2026 | Tài liệu cá nhân |
| 6 | - Thực hiện quy trình dọn dẹp tài nguyên triệt để (Resource Teardown): Xóa EC2, ECS Cluster, ECR Images, ALB, NAT Gateway, Custom VPC, CloudWatch Alarms và S3 Buckets.<br>- Báo cáo tổng kết tuần cuối với người hướng dẫn (Mentor). | 25/09/2026 | 25/09/2026 | AWS Management Console |

### Kết quả đạt được tuần 8:

- Hiểu rõ phương pháp luận thiết kế hệ thống theo chuẩn AWS Well-Architected Framework.
- Phát hiện và khắc phục thành công các lỗ hổng bảo mật cơ bản cũng như các điểm lãng phí chi phí trên tài khoản thực hành.
- Hoàn thành bộ báo cáo tổng kết kỳ thực tập chi tiết, hệ thống hóa đầy đủ kiến thức từ Core Services, Networking, Serverless đến Container.
- Dọn dẹp hoàn toàn 100% tài nguyên thực hành trên AWS Console, đảm bảo số dư credit không bị phát sinh chi phí ngoài ý muốn.