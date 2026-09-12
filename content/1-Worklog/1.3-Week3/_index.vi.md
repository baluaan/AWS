---
title: "Worklog - Tuần 3"
date: 2026-08-17
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:

- Làm chủ kiến trúc mạng ảo AWS Networking: Virtual Private Cloud (VPC), Subnets, Route Tables.
- Khởi tạo hạ tầng mạng mở rộng với Internet Gateway (IGW) và NAT Gateway cho Private Subnet.
- Phân biệt và làm chủ cơ chế bảo mật hai lớp: Security Groups (Stateful - Cấp Instance) và NACLs (Stateless - Cấp Subnet).
- Tự tay xây dựng một mô hình VPC hoàn chỉnh (Custom VPC) chứa đầy đủ Public & Private Subnets.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu tổng quan Amazon VPC: Khái niệm IPv4 CIDR Block, Public Subnet, Private Subnet.<br>- Thiết kế sơ đồ địa chỉ IP cho Custom VPC (ví dụ: CIDR 10.0.0.0/16). | 17/08/2026 | 17/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| 3 | - Khởi tạo Custom VPC, chia Subnet (Public Subnet 10.0.1.0/24, Private Subnet 10.0.2.0/24).<br>- Tạo Internet Gateway (IGW) và attach vào Custom VPC.<br>- Cấu hình Route Table cho Public Subnet định tuyến traffic 0.0.0.0/0 đi qua IGW. | 18/08/2026 | 18/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| 4 | - Tìm hiểu NAT Gateway / NAT Instance: Vai trò giúp Private Subnet đi Internet chiều outbound.<br>- Khởi tạo NAT Gateway trên Public Subnet, gắn Elastic IP (EIP) và cập nhật Route Table của Private Subnet đi qua NAT Gateway. | 19/08/2026 | 19/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| 5 | - Chuyên sâu về Security: So sánh Security Groups vs Network Access Control Lists (NACLs).<br>- Cấu hình Inbound/Outbound Rules cho SG và NACLs để kiểm soát lưu lượng truy cập mạng theo nhu cầu. | 20/08/2026 | 20/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| 6 | - Thực hành Lab tổng hợp: Triển khai 1 EC2 Public (Bastion Host) và 1 EC2 Private.<br>- Thực hiện SSH từ máy cá nhân qua Bastion Host để truy cập vào EC2 Private Instance.<br>- Kiểm tra khả năng kết nối Internet của Private EC2 thông qua NAT Gateway và dọn dẹp tài nguyên. | 21/08/2026 | 21/08/2026 | https://cloudjourney.awsstudygroup.com/ |

### Kết quả đạt được tuần 3:

- Nắm vững lý thuyết mạng AWS: Hiểu rõ cách phân chia dải IP CIDR, cơ chế định tuyến mạng và vai trò của từng thành phần VPC.
- Xây dựng thành công Custom VPC hoàn chỉnh gồm Public Subnet (truy cập Internet trực tiếp) và Private Subnet (bảo mật nội bộ).
- Hiểu rõ sự khác biệt giữa Security Group (Stateful) và NACL (Stateless); biết cách dùng NACL để chặn IP xấu ở cấp độ Subnet.
- Hoàn thành xuất sắc bài Lab thực hành: Cấu hình thành công NAT Gateway, triển khai mô hình Bastion Host kết nối tới Private EC2 an toàn.