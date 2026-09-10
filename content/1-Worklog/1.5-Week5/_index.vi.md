---
title: "Worklog - Tuần 5"
date: 2026-08-15
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:

- Nắm vững ranh giới bảo mật IAM & kiểm soát truy cập: Triển khai ranh giới quyền hạn IAM (permission boundaries) để giới hạn quyền của người dùng và thực thi các hạn chế quản trị một cách an toàn.

- Triển khai mã hóa dữ liệu khi lưu trữ & ghi nhật ký kiểm toán: Sử dụng AWS Key Management Service (KMS) kết hợp với Amazon S3, AWS CloudTrail và Amazon Athena để mã hóa phía máy chủ và kiểm toán bảo mật.

- Chuyển đổi lược đồ cơ sở dữ liệu & di chuyển dữ liệu: Thực hiện quy trình di chuyển cơ sở dữ liệu bằng AWS Database Migration Service (DMS) thông qua cấu hình endpoint nguồn/đích và giám sát các tác vụ nhân bản (replication).

- Xây dựng hồ dữ liệu (Data Lake) & quy trình phân tích trên cloud: Thiết lập các pipeline chuyển đổi dữ liệu bằng Glue DataBrew, Glue Data Catalog, Amazon Athena để truy vấn và Amazon QuickSight để trực quan hóa.

- Quản lý cơ sở dữ liệu NoSQL & tích hợp AWS CLI: Thực hiện quản lý Amazon DynamoDB thông qua AWS Management Console, AWS CloudShell và cấu hình AWS CLI.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                                      | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                          |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | --------------------------------------- |
| 2   | - Giới hạn quyền người dùng bằng ranh giới quyền hạn IAM.<br>- Tạo chính sách hạn chế.<br>- Tạo người dùng IAM bị giới hạn.<br>- Kiểm tra giới hạn của người dùng IAM.                                         | 17/08/2026   | 17/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 3   | - Mã hóa dữ liệu khi lưu trữ với AWS KMS.<br>- Tạo Key Management Service.<br>- Tạo Amazon S3. <br> - Tạo AWS CloudTrail và Amazon Athena.                                                                     | 18/08/2026   | 18/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 4   | - Chuyển đổi lược đồ & di chuyển cơ sở dữ liệu.<br>- Chọn nguồn DMS.<br>- Chọn đích DMS.<br>- Giám sát các tác vụ di chuyển DMS.                                                                               | 19/08/2026   | 19/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 5   | - Xây dựng hồ dữ liệu (Data Lake) với dữ liệu của bạn.<br>- Thiết lập DataBrew.<br>- Cấu hình vai trò (roles) cho AWS Glue. <br> - Tạo Data Catalog.<br> - Cài đặt Athena.<br> - Trực quan hóa với QuickSight. | 20/08/2026   | 20/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 6   | - Làm việc với Amazon DynamoDB.<br>- Quản lý bằng AWS Management Console.<br>- Sử dụng AWS CloudShell. <br> - Cấu hình AWS CLI.                                                                                | 21/08/2026   | 21/08/2026      | https://cloudjourney.awsstudygroup.com/ |

### Kết quả đạt được tuần 5:

- Thực thi thành công ranh giới quyền hạn IAM: Đã tạo các chính sách hạn chế tùy chỉnh và giới hạn người dùng IAM, xác minh ranh giới quyền hạn thông qua kiểm thử truy cập thực tế.

- Triển khai mã hóa KMS & kiểm toán CloudTrail: Đã cấu hình khóa AWS KMS để mã hóa Amazon S3 khi lưu trữ, tích hợp AWS CloudTrail để theo dõi hoạt động, và truy vấn nhật ký kiểm toán bằng Amazon Athena.

- Hoàn thành di chuyển cơ sở dữ liệu toàn diện: Đã cấu hình endpoint nguồn và đích của AWS DMS, thực thi thành công các tác vụ di chuyển dữ liệu và giám sát hiệu suất di chuyển.

- Xây dựng hồ dữ liệu vận hành & bảng điều khiển (Dashboard): Đã cấu hình vai trò IAM cho AWS Glue, Data Catalog và AWS Glue DataBrew cho các hoạt động ETL, chạy truy vấn trong Athena và xây dựng các bản trực quan hóa tương tác trong Amazon QuickSight.

- Thành thạo Amazon DynamoDB & các thao tác AWS CLI: Đã tạo và quản lý các bảng NoSQL DynamoDB thông qua AWS Management Console, AWS CloudShell, và AWS CLI được cấu hình cục bộ.
