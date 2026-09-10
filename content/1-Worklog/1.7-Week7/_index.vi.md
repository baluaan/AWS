---
title: "Worklog - Tuần 7"
date: 2026-08-29
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7:

- Phát hiện sự cố & Kích hoạt cảnh báo: Cấu hình Metric Filters trong CloudWatch Logs để phân tích log truy cập WAF và thiết lập CloudWatch Alarms nhằm phát hiện các IP có tần suất truy cập vượt ngưỡng cho phép.

- Phát triển logic tự động hóa ứng phó sự cố: Lập trình và triển khai AWS Lambda Function bằng Python (`boto3`) để trích xuất địa chỉ IP vi phạm, tự động gọi API của AWS WAF (`GetIPSet` & `UpdateIPSet`) để thêm IP vào danh sách chặn.

- Tích hợp hệ thống thông báo thời gian thực: Khởi tạo Amazon SNS Topic và cấu hình các điểm nhận thông báo (Email/Webhook) để gửi cảnh báo tức thì cho quản trị viên ngay khi xử lý sự cố.

- Phân quyền & Thắt chặt bảo mật IAM: Áp dụng nguyên tắc quyền tối thiểu (Least Privilege) để xây dựng IAM Roles và Inline Policies chi tiết cho Lambda.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                                              | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                          |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | --------------------------------------- |
| 2   | - Cấu hình bộ lọc chỉ số & cảnh báo CloudWatch.<br>- Tạo Metric Filter trên CloudWatch Log Group của WAF log.<br>- Thiết lập CloudWatch Alarm kích hoạt khi một IP vượt ngưỡng số lượng yêu cầu quy định.              | 31/08/2026   | 31/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 3   | - Phát triển logic xử lý tự động với Lambda.<br>- Viết mã nguồn Python sử dụng thư viện `boto3` để thực hiện thao tác `GetIPSet` và `UpdateIPSet`.<br>- Triển khai hàm trích xuất IP và chuyển đổi định dạng CIDR.     | 01/09/2026   | 01/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 4   | - Tích hợp luồng tự động hóa ứng phó sự cố.<br>- Cấu hình CloudWatch Alarm tự động gọi Lambda Function khi chuyển sang trạng thái Alarm.<br>- Xây dựng cơ chế hết hạn (TTL) để gỡ chặn IP tạm thời.                    | 02/09/2026   | 02/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 5   | - Thiết lập hệ thống thông báo SNS & Phân quyền IAM.<br>- Khởi tạo Amazon SNS Topic và cấu hình đăng ký nhận email cảnh báo.<br>- Định nghĩa IAM Role và phân quyền nghiêm ngặt cho Lambda theo chuẩn Least Privilege. | 03/09/2026   | 03/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 6   | - Kiểm thử chức năng.<br>- Kiểm thử chạy giả lập Lambda với dữ liệu mẫu từ cảnh báo.<br>- Xác nhận IP được thêm tự động vào WAF IP Set và tối ưu hóa luồng thực thi mã nguồn.                                          | 04/09/2026   | 04/09/2026      | https://cloudjourney.awsstudygroup.com/ |

### Kết quả đạt được tuần 7:

- Cấu hình thành công hệ thống phát hiện & cảnh báo: Xây dựng thành công CloudWatch Metric Filters trên WAF access logs và thiết lập các CloudWatch Alarms phát hiện chính xác các đợt tăng lưu lượng truy cập bất thường.

- Triển khai hoàn tất hạ tầng ứng phó sự cố với Lambda: Viết và triển khai thành công mã nguồn Python (`boto3`) trên Lambda, cho phép trích xuất IP tấn công và tự động cập nhật danh sách chặn (IP Set) trên AWS WAF.

- Tích hợp kênh thông báo sự cố tức thì: Kênh cảnh báo qua Amazon SNS hoạt động ổn định, tự động gửi thông tin chi tiết về email quản trị ngay khi phát hiện và chặn IP độc hại.

- Thắt chặt phân quyền bảo mật IAM: Hoàn thiện phân quyền IAM cho Lambda Function theo đúng nguyên tắc Least Privilege, đảm bảo hệ thống tự động hóa chỉ có các quyền tối thiểu cần thiết để vận hành.
