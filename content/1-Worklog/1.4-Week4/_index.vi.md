---
title: "Worklog - Tuần 4"
date: 2026-08-24
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:

- Nghiên cứu mô hình kiến trúc không máy chủ (Serverless Architecture) và dịch vụ AWS Lambda.
- Nắm vững cách thức hoạt động của Lambda: Triggers, Execution Role, Event Sources, Environment Variables.
- Ứng dụng AWS Lambda trong bài toán tự động hóa vận hành và tối ưu hóa chi phí điện toán EC2.
- Thực hành bài Lab tự động bật/tắt EC2 Instance theo lịch trình thiết lập sẵn.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu khái niệm Serverless Computing, so sánh giữa Serverless và Server-based (EC2).<br>- Học về kiến trúc AWS Lambda: Runtime (Python/Node.js), Memory allocation, Timeout, Event Driven. | 24/08/2026 | 24/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| 3 | - Tạo hàm AWS Lambda đơn giản bằng Python (Boto3 SDK).<br>- Cấu hình IAM Execution Role cho Lambda cấp quyền quản lý EC2 (StartInstances, StopInstances, DescribeInstances). | 25/08/2026 | 25/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| 4 | - Viết mã Python trong Lambda function để tự động quét các EC2 Instance có tag `Environment: Dev` và thực hiện Stop/Start.<br>- Chạy thử nghiệm hàm Lambda trực tiếp trên AWS Console và kiểm tra log qua CloudWatch Logs. | 26/08/2026 | 26/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| 5 | - Tích hợp Amazon EventBridge (CloudWatch Events) làm Trigger lịch trình (Cron Expression / Rate) để kích hoạt Lambda.<br>- Cấu hình lịch tự động tắt EC2 vào 19:00 hàng ngày và bật lại vào 07:00 sáng ngày làm việc để tối ưu chi phí. | 27/08/2026 | 27/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| 6 | - Đo lường chi phí tiết kiệm được của EC2 khi áp dụng giải pháp bật/tắt tự động.<br>- Hoàn thiện báo cáo Lab, kiểm tra lại toàn bộ cấu hình Lambda và dọn dẹp tài nguyên thử nghiệm. | 28/08/2026 | 28/08/2026 | https://cloudjourney.awsstudygroup.com/ |

### Kết quả đạt được tuần 4:

- Hiểu rõ bản chất Serverless: Không cần quản lý hạ tầng máy chủ, tự động mở rộng (auto-scaling) và tính chi phí theo thời gian thực thi.
- Thành thạo viết script Python với Boto3 SDK để tương tác với các tài nguyên trên AWS thông qua Lambda.
- Xây dựng thành công giải pháp tự động hóa tối ưu chi phí: Sử dụng EventBridge + Lambda để tự động Start/Stop máy chủ EC2 Dev/Test ngoài giờ làm việc.
- Thành thạo kỹ năng đọc log và debug hàm Lambda thông qua Amazon CloudWatch Logs.