---
title: "Worklog - Tuần 5"
date: 2026-08-31
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:

- Nghiên cứu Amazon CloudWatch (Dịch vụ giám sát tài nguyên và ứng dụng) và AWS CloudTrail (Dịch vụ ghi log kiểm vết hoạt động người dùng/API).
- Hiểu các thành phần chính của CloudWatch: Metrics, Logs, Alarms, Dashboards.
- Xây dựng hệ thống cảnh báo tự động khi tài nguyên vượt ngưỡng hiệu năng (CPU High, Memory, Disk).
- Thực hành truy vết nhật ký thao tác trên CloudTrail để phục vụ công tác điều tra an toàn thông tin.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu Amazon CloudWatch Metrics và Logs: Cách thu thập thông số hiệu năng của EC2, S3, Lambda.<br>- Tìm hiểu CloudWatch Agent để thu thập log hệ điều hành và Memory Metrics. | 31/08/2026 | 31/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| 3 | - Cấu hình CloudWatch Alarm: Thiết lập ngưỡng cảnh báo khi CPU EC2 Instance vượt quá 80%.<br>- Tích hợp Amazon SNS (Simple Notification Service) để gửi Email thông báo tự động khi Alarm chuyển trạng thái ALARM. | 01/09/2026 | 01/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| 4 | - Thiết lập CloudWatch Dashboard trực quan: Tạo các Widget biểu đồ hiển thị CPU Utilization, Network In/Out và Disk Read/Write cho các máy chủ. | 02/09/2026 | 02/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| 5 | - Tìm hiểu AWS CloudTrail: Khái niệm Trail, Management Events, Data Events.<br>- Tạo mới một CloudTrail để ghi lại toàn bộ sự kiện API trong AWS Account và lưu trữ log tập trung tại S3 Bucket. | 03/09/2026 | 03/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| 6 | - Thực hành truy vết với CloudTrail Event History: Tìm kiếm lịch sử hành động xóa S3 Bucket hoặc Terminate EC2 Instance để xác định IAM User/IP thực hiện.<br>- Đánh giá, tổng kết kiến thức Observability trên AWS. | 04/09/2026 | 04/09/2026 | https://cloudjourney.awsstudygroup.com/ |

### Kết quả đạt được tuần 5:

- Hiểu rõ vai trò quan trọng của CloudWatch trong việc duy trì tính sẵn sàng và hiệu năng của hệ thống.
- Cấu hình thành công hệ thống cảnh báo thời gian thực: Tự động gửi Email qua SNS khi máy chủ EC2 bị cạn kiệt tài nguyên (CPU Utilization high).
- Tự tay thiết kế Dashboard quan sát trực quan cho toàn bộ hạ tầng AWS của tài khoản thực hành.
- Khai thác thành thạo AWS CloudTrail: Biết cách truy vết ai đã làm gì, vào thời gian nào và từ IP nào trên hạ tầng AWS Cloud.