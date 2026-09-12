---
title: "Worklog - Tuần 7"
date: 2026-09-14
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7:

- Tiếp cận công nghệ đóng gói ứng dụng Container và kiến thức Docker cơ bản (Docker Image, Container, Dockerfile).
- Tìm hiểu dịch vụ điều phối Container trên AWS - Amazon Elastic Container Service (AWS ECS).
- Phân biệt hai mô hình triển khai ECS: EC2 Launch Type và Fargate Launch Type (Serverless Container).
- Thực hành triển khai một ứng dụng container hóa hoàn chỉnh lên AWS ECS Fargate.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu kiến thức cơ bản về Docker: Containerization vs Virtualization (VM), Docker Engine.<br>- Viết Dockerfile cơ bản, xây dựng Docker Image (`docker build`) và chạy thử nghiệm Container (`docker run`) ở môi trường local. | 14/09/2026 | 14/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| 3 | - Tìm hiểu dịch vụ lưu trữ Docker Image của AWS - Amazon Elastic Container Registry (ECR).<br>- Tạo ECR Repository, thực hiện authenticate Docker CLI với ECR, tag image và push Docker Image lên ECR. | 15/09/2026 | 15/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| 4 | - Tìm hiểu kiến trúc AWS ECS: ECS Cluster, Task Definition, Task, Service.<br>- So sánh mô hình chạy ECS với EC2 Capacity vs AWS Fargate (Serverless compute for containers). | 16/09/2026 | 16/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| 5 | - Khởi tạo ECS Cluster trên AWS.<br>- Tạo Task Definition chỉ định Docker Image từ Amazon ECR, cấu hình CPU/RAM và Port Mapping.<br>- Tạo ECS Service chạy Task trên hạ tầng AWS Fargate. | 17/09/2026 | 17/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| 6 | - Tích hợp ECS Service với Application Load Balancer (ALB) để phục vụ lưu lượng truy cập người dùng.<br>- Truy cập ứng dụng qua ALB DNS, đánh giá kết quả triển khai, dọn dẹp Cluster và ECR để kết thúc chuỗi thực hành. | 18/09/2026 | 18/09/2026 | https://cloudjourney.awsstudygroup.com/ |

### Kết quả đạt được tuần 7:

- Nắm vững khái niệm Containerization và làm chủ các câu lệnh Docker cơ bản (Dockerfile, Image, Container).
- Thành thạo thao tác quản lý kho chứa ảnh Amazon ECR: Push/Pull Docker Images an toàn.
- Hiểu rõ kiến trúc và các thành phần cốt lõi của Amazon ECS (Task Definition, Task, Service, Cluster).
- Khởi tạo và triển khai thành công ứng dụng Container hóa lên AWS ECS Fargate tích hợp ALB, sẵn sàng cho các mô hình ứng dụng Microservices hiện đại.