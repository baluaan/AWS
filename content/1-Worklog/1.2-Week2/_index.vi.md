---
title: "Worklog - Tuần 2"
date: 2026-07-25
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:

- Nắm vững kiến thức cốt lõi về AWS Networking: Hiểu sâu về cấu trúc VPC, Subnets, Route Tables, Internet Gateways, NAT Gateways và các cơ chế bảo mật (Security Groups, Network ACLs, VPC Flow Logs).

- Triển khai hạ tầng mạng bảo mật: Cấu hình truy cập an toàn bằng EC2 Instance Connect Endpoint, thiết lập kết nối Site-to-Site VPN và thiết lập VPC Peering giữa các môi trường.

- Tích hợp dịch vụ Hybrid DNS và Identity: Cấu hình Hybrid DNS với Route 53 Resolver, triển khai Microsoft Active Directory trên AWS và kết nối qua RDGW.

- Theo dõi và tự động hóa: Thiết lập CloudWatch Monitoring, khởi tạo tài nguyên bằng CloudFormation template và cấu hình bảo mật nâng cao.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                                                                                                          | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                          |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | --------------------------------------- |
| 2   | - Học về VPC: khái niệm, kiến trúc và phạm vi, các thành phần cơ bản: Subnets, Route Table, Internet Gateway, NAT Gateway.<br>- Học về tường lửa trong VPC: Security Group, Network ACLs, VPC Resource Map.<br>- Tạo VPC.<br>- Tạo Subnet.<br>- Tạo Internet Gateway.              | 27/07/2026   | 27/07/2026      | https://cloudjourney.awsstudygroup.com/ |
| 3   | - Tạo Route Table.<br>- Tạo Security Group.<br>- Kích hoạt VPC Flow Logs.<br>                                                                                                                                                                                                      | 28/07/2026   | 28/07/2026      | https://cloudjourney.awsstudygroup.com/ |
| 4   | - Tạo NAT Gateway.<br>- Tạo EC2 Instance Connect Endpoint.<br>- Triển khai CloudWatch Monitoring.<br> - Cấu hình Site to Site VPN: <br> + Tạo môi trường VPN: tạo VPC cho VPN, tạo EC2 Instance. <br> + Cấu hình kết nối VPN: tạo Virtual Private Gateway, tạo Customer Gateway... | 29/07/2026   | 29/07/2026      | https://cloudjourney.awsstudygroup.com/ |
| 5   | - Thiết lập Hybrid DNS với Route 53 Resolver.<br>- Tạo key pair.<br>- Khởi tạo cloudformation template. <br> - Cấu hình security group. <br> - Kết nối đến RDGW. <br> - Triển khai Microsoft AD. <br> - Thiết lập DNS.                                                             | 30/07/2026   | 30/07/2026      | https://cloudjourney.awsstudygroup.com/ |
| 6   | - Thiết lập VPC Peering.<br>- Cập nhật Network ACL.<br>- Tạo kết nối Peering. <br> - Kích hoạt Cross-Peer DNS.                                                                                                                                                                     | 31/07/2026   | 31/07/2026      | https://cloudjourney.awsstudygroup.com/ |

### Kết quả đạt được tuần 2:

- Khởi tạo và cấu hình thành công hệ thống VPC: Tạo hoàn chỉnh hệ thống mạng riêng bao gồm Public/Private Subnets, Route Tables, Internet Gateway, NAT Gateway và bật VPC Flow Logs để phục vụ việc giám sát truy cập.

- Tăng cường bảo mật và kết nối an toàn: Thiết lập thành công EC2 Instance Connect Endpoint, triển khai kết nối Site-to-Site VPN (Virtual Private Gateway & Customer Gateway) và liên kết các VPC thông qua VPC Peering (bao gồm cả Cross-Peer DNS và cập nhật NACL).

- Triển khai hạ tầng DNS và Identity nâng cao: Thiết lập thành công Route 53 Resolver cho mô hình Hybrid DNS, triển khai hệ thống Microsoft Active Directory kết hợp RDGW bằng CloudFormation template.

- Hoàn thành triển khai CloudWatch Monitoring: Cấu hình giám sát tập trung cho hệ thống mạng và hạ tầng máy chủ.
