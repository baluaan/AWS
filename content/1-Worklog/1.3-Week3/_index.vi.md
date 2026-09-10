---
title: "Worklog - Tuần 3"
date: 2026-08-01
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:

- Nắm vững kết nối nâng cao & Lưu trữ Hybrid trên AWS: Đạt được kỹ năng thực hành thực tế với AWS Transit Gateway, AWS File Storage Gateway và quy trình dịch chuyển máy ảo lai (VM Import/Export).

- Triển khai giải pháp Tự động hóa Sao lưu & Bảo vệ Dữ liệu: Cấu hình các chính sách sao lưu tập trung bằng AWS Backup, kết hợp với dịch vụ lưu trữ Amazon S3 và cơ chế gửi cảnh báo/thông báo.

- Cấu hình Lưu trữ Đối tượng & Lưu trữ Website Tĩnh: Thành thạo quản trị Amazon S3, bao gồm cấu hình Bucket Policy, thiết lập Block Public Access, bật tính năng Static Website Hosting và quản lý quyền truy cập ACLs.

- Thực thi Dịch chuyển Máy ảo từ On-Premises lên Cloud: Thực hành xuất (export), tải lên (upload), nhập (import) và triển khai các hình ảnh máy ảo (AMI) giữa môi trường On-premises và AWS.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                            | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                          |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | --------------------------------------- |
| 2   | - Thiết lập AWS Transit Gateway.<br>- Tạo Transit Gateway.<br>- Tạo Transit Gateway route tables.<br>- Thêm Transit Gateway routes vào VPC route tables.                                             | 03/08/2026   | 03/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 3   | - Triển khai AWS Backup cho hệ thống.<br>- Tạo S3 Bucket.<br>- Tạo Backup plan.<br>- Thiết lập thông báo.<br>                                                                                        | 04/08/2026   | 04/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 4   | - Triển khai File Storage Gateway.<br>- Tạo EC2 cho Storage Gateway.<br>- Tạo Storage Gateway.<br> - Tạo File Shares. <br> - Kết nối File Shares ở máy On-premise.                                   | 05/08/2026   | 05/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 5   | - Khởi đầu với amazon S3.<br>- Bật tính năng static website.<br>- Cấu hình Block Public Access. <br> - Cấu hình public object.                                                                       | 06/08/2026   | 06/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 6   | - Export máy ảo từ On-premise.<br>- Tải máy ảo lên AWS.<br>- Import máy ảo vào AWS. <br> - Triển khai EC2 Instance từ AMI. <br> - Thiết lập ACL cho S3 Bucket. <br> - Export máy ảo từ EC2 Instance. | 07/08/2026   | 07/08/2026      | https://cloudjourney.awsstudygroup.com/ |

### Kết quả đạt được tuần 3:

- Cấu hình thành công AWS Transit Gateway: Tạo tài nguyên Transit Gateway, thiết lập các bảng tuyến đường (route table) và cập nhật route table của VPC để cho phép định tuyến giữa các mạng linh hoạt.

- Triển khai Hệ thống Quản lý Sao lưu Tự động: Khởi tạo S3 Bucket làm nơi lưu trữ và cấu hình các kế hoạch sao lưu tự động với AWS Backup, đi kèm hệ thống thông báo sự kiện.

- Thiết lập File Storage Gateway: Triển khai Storage Gateway trên EC2, cấu hình các File Share và kết nối thành công File Share tới các máy trạm On-premises phục vụ truy cập hybrid.

- Triển khai Website Tĩnh trên S3 & Cấu hình Bảo mật: Bật thành công tính năng Static Website Hosting trên Amazon S3, đồng thời cấu hình chuẩn xác các quy tắc Block Public Access và phân quyền ở cấp độ đối tượng.

- Hoàn thành Quy trình Dịch chuyển & Quản lý Máy ảo End-to-End: Thực hiện trọn vẹn quy trình export/import máy ảo giữa môi trường On-premises và AWS, khởi chạy EC2 Instance từ các AMI đã chuyển đổi, quản lý S3 ACLs và export ngược instance thành định dạng máy ảo.
