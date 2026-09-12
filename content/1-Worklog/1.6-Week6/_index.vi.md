---
title: "Worklog - Tuần 6"
date: 2026-09-07
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu tuần 6:

- Tìm hiểu giải pháp cân bằng tải Elastic Load Balancer (ELB) và cơ chế tự động mở rộng Auto Scaling Group (ASG).
- Phân biệt các loại Load Balancer: Application Load Balancer (ALB), Network Load Balancer (NLB).
- Cấu hình ALB để phân phối lưu lượng truy cập tới nhiều EC2 Instances thuộc các Availability Zones khác nhau.
- Thiết lập Auto Scaling Group kết hợp với Target Group để tự động co giãn số lượng máy chủ theo tải hệ thống.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu tổng quan Elastic Load Balancing (ELB): Nguyên lý hoạt động, Target Group, Health Checks.<br>- So sánh sự khác biệt giữa ALB (Layer 7 - HTTP/HTTPS) và NLB (Layer 4 - TCP/UDP). | 07/09/2026 | 07/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| 3 | - Khởi tạo 2 EC2 Instances chạy Web Server ở 2 Subnet/AZ khác nhau.<br>- Tạo Application Load Balancer (ALB) công khai, cấu hình Target Group và gắn 2 EC2 Instances vào Target Group.<br>- Kiểm tra khả năng cân bằng tải bằng cách truy cập IP/DNS Name của ALB. | 08/09/2026 | 08/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| 4 | - Tìm hiểu AWS Auto Scaling Group (ASG): Khái niệm Launch Template / Launch Configuration, Desired Capacity, Min Capacity, Max Capacity.<br>- Tạo Launch Template đóng gói sẵn cấu hình EC2 Web Server. | 09/09/2026 | 09/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| 5 | - Khởi tạo Auto Scaling Group gắn với Launch Template và Target Group của ALB.<br>- Cấu hình Scaling Policies (Dynamic Scaling dựa trên Target Tracking: Giữ CPU trung bình ở mức 50%). | 10/09/2026 | 10/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| 6 | - Thực hành Lab Stress Test: Dùng công cụ (stress/ab) ép CPU của EC2 tăng cao để kiểm tra cơ chế Auto Scaling tự động thêm Instance mới.<br>- Kiểm tra cơ chế Scale In khi tải giảm và dọn dẹp tài nguyên. | 11/09/2026 | 11/09/2026 | https://cloudjourney.awsstudygroup.com/ |

### Kết quả đạt được tuần 6:

- Hiểu rõ nguyên lý hoạt động của ELB và kiến trúc chịu lỗi cao (High Availability) trên nhiều AZs.
- Cấu hình thành công Application Load Balancer (ALB) điều hướng traffic thông minh và kiểm tra Health Check chuẩn xác.
- Tạo thành công Launch Template và cấu hình Auto Scaling Group (ASG) co giãn linh hoạt theo nhu cầu thực tế.
- Kiểm thử thành công kịch bản High Availability: Hệ thống tự động khởi tạo máy chủ mới khi bị quá tải và tự động giảm số lượng máy chủ khi hết tải để tối ưu chi phí.