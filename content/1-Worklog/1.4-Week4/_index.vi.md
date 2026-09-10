---
title: "Worklog - Tuần 4"
date: 2026-08-08
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:

- Nắm vững lưu trữ doanh nghiệp & dịch chuyển dữ liệu đa vùng: triển khai Amazon FSX for Windows File Server (SSD/HDD) chế độ Multi-AZ, cấu hình tối ưu hóa dữ liệu (deduplication) và thiết lập cơ chế nhân bản đa vùng (Cross-Region Replication - CRR) trên Amazon S3.

- Triển khai quản lý an toàn mạng & tuân thủ an ninh: thực hành cấu hình AWS Security Hub để đánh giá, theo dõi và đo lường điểm số an toàn thông tin theo các tiêu chuẩn bảo mật đám mây.

- Tự động hóa tối ưu hóa chi phí & quản trị tài nguyên: sử dụng AWS Lambda kết hợp với chiến lược gắn thẻ (tagging) để tự động hóa quản lý vòng đời máy chủ EC2 nhằm giảm thiểu chi phí vận hành.

- Tối ưu hóa tổ chức & quản lý tài nguyên AWS: thiết lập quy chuẩn gắn thẻ và tạo các nhóm tài nguyên (Resource Groups) để theo dõi, quản lý tập trung và tăng cường quy trình quản trị đám mây.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                            | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                          |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | --------------------------------------- |
| 2   | - Triển khai FSX trên Windows.<br>- Tạo một SSD Multi-AZ file system.<br>- Tạo một HDD Multi-AZ file system.<br>- Tạo file share. <br>- Giám sát hiệu năng. <br>- Kích hoạt chống dữ liệu trùng lặp. | 10/08/2026   | 10/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 3   | - Khởi đầu Với Amazon S3.<br>- Tạo S3 Bucket.<br>- Bật tính năng static website.<br>- Cấu hình Block Public Access.<br> - Cấu hình public object. <br> - Sao chép S3 Object sang region khác.        | 11/08/2026   | 11/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 4   | - Bắt đầu với AWS Security Hub.<br>- Kích hoạt Security Hub.<br>- Điểm từng bộ tiêu chuẩn.<br> - Dọn dẹp tài nguyên.                                                                                 | 12/08/2026   | 12/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 5   | - Tối ưu chi phí EC2 với Lambda.<br>- Tạo Tag cho Instance.<br>- Tạo Role cho Lambda. <br> - Tạo Lambda Function.                                                                                    | 13/08/2026   | 13/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 6   | - Quản lý tài nguyên bằng Tag và Resource Groups.<br>- Sử dụng Tag.<br>- Tạo Resource Group. <br>                                                                                                    | 14/08/2026   | 14/08/2026      | https://cloudjourney.awsstudygroup.com/ |

### Kết quả đạt được tuần 4:

- Triển khai thành công Amazon FSX for Windows File Server: khởi tạo các hệ thống tệp Multi-AZ trên cả đĩa SSD và HDD, tạo các File Share, theo dõi hiệu năng và bật tính năng Data Deduplication giúp tối ưu dung lượng lưu trữ.

- Tinh chỉnh quản trị S3 & nhân bản đa vùng: cấu hình Static Website Hosting kèm các quy tắc Block Public Access, đồng thời thiết lập thành công cơ chế nhân bản đối tượng S3 đa vùng (CRR) đảm bảo tính sẵn sàng cao và dự phòng sự cố.

- Triển khai đánh giá an ninh với AWS Security Hub: bật thành công Security Hub, phân tích điểm số an toàn theo tiêu chuẩn bảo mật đám mây và thực hiện thu dọn tài nguyên đúng quy chuẩn sau kiểm thử.

- Tự động hóa tối ưu chi phí EC2 bằng AWS Lambda: tạo các Tag tài nguyên, cấu hình IAM Role phân quyền và triển khai các hàm Lambda tự động bật/tắt các Instance EC2 nhằm tiết kiệm chi phí.

- Chuẩn hóa quản trị tài nguyên trên AWS: áp dụng quy chuẩn gắn thẻ đồng nhất và khởi tạo các AWS Resource Group giúp đơn giản hóa việc quản lý và giám sát tập trung tài nguyên hệ thống.
