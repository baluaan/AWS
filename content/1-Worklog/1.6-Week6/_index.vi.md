---
title: "Worklog - Tuần 6"
date: 2026-08-22
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu tuần 6:

- Lập kế hoạch dự án & Phân tích an ninh mạng: Khảo sát các kịch bản tấn công web phổ biến (DDoS, HTTP Flood, Brute Force) và thiết kế mô hình kiến trúc hạ tầng tự động hóa trên AWS.

- Triển khai lớp bảo vệ vùng biên: Cấu hình Amazon CloudFront Distribution làm điểm phân phối nội dung và kết nối với AWS WAF Web ACL để lọc lưu lượng HTTP/HTTPS.

- Khởi tạo quy tắc lọc & Danh sách chặn: Thiết lập các WAF Managed Rules cơ bản và khởi tạo IP Set rỗng làm tiền đề cho việc tự động chặn IP ở các giai đoạn sau.

- Thiết lập cơ chế ghi nhật ký & Giám sát: Bật WAF Logging, cấu hình chuyển giao dữ liệu log về CloudWatch Logs và theo dõi các chỉ số lưu lượng mạng ban đầu.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                          |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | --------------------------------------- |
| 2   | - Lập kế hoạch dự án & thiết kế kiến trúc.<br>- Phân tích bài toán bảo mật ứng dụng web.<br>- Vẽ sơ đồ kiến trúc tổng quan hệ thống tự động hóa phản ứng tấn công.                          | 24/08/2026   | 24/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 3   | - Triển khai phân phối vùng biên với CloudFront.<br>- Khởi tạo Amazon CloudFront distribution.<br>- Trỏ gốc ứng dụng web về CDN để tối ưu hóa lưu lượng và chặn truy cập từ xa.             | 25/08/2026   | 25/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 4   | - Cấu hình tường lửa AWS WAF.<br>- Tạo Web ACL trên AWS WAF và đính kèm vào CloudFront.<br>- Định nghĩa các AWS Managed Rule Groups tiêu chuẩn.<br>- Khởi tạo IP Set rỗng (Blocked IP Set). | 26/08/2026   | 26/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 5   | - Cấu hình hệ thống ghi log tập trung.<br>- Bật tính năng WAF Logging.<br>- Định tuyến log truy cập trực tiếp về Amazon CloudWatch Log Group.                                               | 27/08/2026   | 27/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 6   | - Đánh giá chỉ số lưu lượng.<br>- Phân tích cấu trúc dữ liệu log thu thập được từ CloudWatch.<br>- Tinh chỉnh cấu hình vùng biên.                                                           | 28/08/2026   | 28/08/2026      | https://cloudjourney.awsstudygroup.com/ |

### Kết quả đạt được tuần 6:

- Hoàn thành thiết kế kiến trúc tổng quan: Đã phân tích thành công các kịch bản tấn công web và thống nhất sơ đồ kiến trúc ứng phó sự cố an toàn thông tin tự động hóa dựa trên các dịch vụ Cloud-Native.

- Triển khai thành công phân phối vùng biên: Đã cấu hình Amazon CloudFront làm CDN phía trước hệ thống, đảm bảo khả năng chặn lưu lượng xấu ngay từ vòng ngoài (Edge Location).

- Khởi tạo hạ tầng AWS WAF & IP Set: Đã thiết lập WAF Web ACL liên kết với CloudFront, cấu hình các bộ quy tắc bảo vệ cơ bản và khởi tạo sẵn danh sách IP đen (IP Set) phục vụ cho tự động hóa.

- Triển khai hệ thống ghi log tập trung: Đã bật tính năng WAF Logging và tích hợp thành công dữ liệu log truy cập thời gian thực về CloudWatch Logs.
