---
title: "Đề xuất"
date: 2026-09-11
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# AWS SSH Automated Threat Protection

## Hệ thống giám sát, phát hiện và tự động ngăn chặn tấn công SSH trên AWS

---

# 1. Tóm Tắt Dự Án

**AWS SSH Automated Threat Protection** là một giải pháp an toàn thông tin dựa trên hạ tầng đám mây (Cloud-Native Security Solution), được thiết kế nhằm tự động hóa quy trình phát hiện, phân tích và chủ động ngăn chặn các hành vi tấn công SSH Brute-Force và Password Guessing hướng tới máy chủ Amazon EC2 (Ubuntu Linux).

Hệ thống tận dụng tối đa các dịch vụ không máy chủ (Serverless) và quản lý của AWS bao gồm **Amazon EC2**, **Amazon CloudWatch Logs**, **Subscription Filter**, **AWS Lambda**, **Amazon DynamoDB**, **Amazon SNS** và **Network ACL (NACL)**. Bằng cách thu thập log đăng nhập theo thời gian thực từ SSH Server (`/var/log/auth.log`), hệ thống tự động bóc tách các sự kiện đăng nhập thất bại, duy trì bộ đếm tần suất truy cập theo địa chỉ IP nguồn và cửa sổ thời gian (1 phút). Khi số lần đăng nhập thất bại vượt ngưỡng cho phép (**5 lần/1 phút**), hệ thống tự động gửi tin nhắn cảnh báo qua email bằng **Amazon SNS** và kích hoạt quy trình phản ứng tự động: tạo quy tắc từ chối (`DENY`) trên Network ACL để chặn hoàn toàn địa chỉ IP tấn công dạng `/32` ở tầng mạng Subnet.

Giải pháp đảm bảo khả năng phản ứng tự động gần như tức thì, giảm thiểu tối đa sự can thiệp thủ công của quản trị viên, tối ưu chi phí vận hành và nâng cao tính sẵn sàng cho hạ tầng máy chủ.

---

# 2. Đặt Vấn Đề

## Vấn đề hiện tại

Giao thức SSH (Secure Shell) là phương thức quản trị từ xa phổ biến nhất trên các hệ điều hành Linux. Do tính chất bắt buộc phải mở cổng quản trị (mặc định Port 22), các máy chủ EC2 thường xuyên trở thành mục tiêu hàng đầu của các đợt rà quét và tấn công dò mật khẩu tự động.

Các phương pháp phòng vệ và quản trị truyền thống đang đối mặt với nhiều thách thức:

- **Ghi nhận Log thụ động:** Việc chỉ thu thập và ghi nhận log đăng nhập thất bại chưa đủ để bảo vệ hệ thống nếu không có cơ chế phản ứng tự động kích hoạt kèm theo.
- **Phản ứng thủ công chậm trễ:** Quản trị viên phải kiểm tra file log (`auth.log`) thủ công, lọc tìm IP độc hại và thêm quy tắc chặn bằng tay. Quy trình này tốn thời gian, dẫn đến nguy cơ hệ thống bị chiếm quyền điều khiển trước khi sự cố được xử lý.
- **Thiếu cơ chế cảnh báo tức thời:** Khi sự cố tấn công diễn ra, quản trị viên không nhận được thông báo ngay lập tức để nắm bắt tình hình hệ thống.
- **Tiêu tốn tài nguyên máy chủ:** Các đợt tấn công Brute-Force tần suất cao liên tục tiêu tốn tài nguyên CPU, RAM và băng thông mạng của máy chủ EC2 nếu không được chặn ngay từ vòng ngoài (tầng mạng Subnet).
- **Thiếu cơ chế duy trì trạng thái đếm linh hoạt:** Không có bộ đếm tập trung để theo dõi chính xác mật độ tấn công từ một IP trong các khoảng thời gian ngắn.

## Giải pháp

Dự án đề xuất xây dựng hệ thống **AWS SSH Automated Threat Protection** tự động hóa hoàn toàn quy trình phát hiện, cảnh báo và phản ứng trước hành vi tấn công SSH theo chuẩn kiến trúc an toàn trên AWS:

- **Amazon EC2 (Ubuntu Linux):** Đóng vai trò làm máy chủ dịch vụ SSH Server.
- **Amazon CloudWatch Logs:** Thu thập và lưu trữ tập trung log đăng nhập SSH từ EC2 theo thời gian thực.
- **Subscription Filter:** Màng lọc log thông minh, phát hiện chính xác các chuỗi sự kiện `Failed password` và đẩy dữ liệu tới bộ xử lý.
- **AWS Lambda:** Hàm xử lý Serverless đóng vai trò làm trung tâm điều phối: giải mã payload log, bóc tách IP nguồn bằng Regex, tương tác với cơ sở dữ liệu, gửi thông báo SNS và gọi API chặn IP.
- **Amazon DynamoDB:** Cơ sở dữ liệu NoSQL lưu trữ và duy trì bộ đếm số lần đăng nhập thất bại theo từng IP và cửa sổ thời gian 1 phút.
- **Amazon SNS:** Dịch vụ gửi thông báo tự động phát tin nhắn cảnh báo qua email đến quản trị viên ngay khi phát hiện mối đe dọa.
- **Network ACL (NACL):** Tường lửa tầng Subnet thực thi quy tắc `DENY` loại bỏ hoàn toàn traffic từ IP tấn công theo định dạng `/32`.

## Lợi ích

- **Phản ứng tự động gần như tức thì:** Ngăn chặn IP độc hại trong vài giây ngay khi đạt ngưỡng vi phạm mà không cần con người can thiệp.
- **Cảnh báo chủ động qua Email:** Quản trị viên lập tức nhận được email thông báo chi tiết về IP vi phạm và hành động can thiệp của hệ thống thông qua Amazon SNS.
- **Chặn triệt để ở tầng mạng (Subnet Level):** Việc chặn bằng Network ACL giúp loại bỏ gói tin độc hại trước khi nó tiếp cận máy chủ EC2, bảo vệ tài nguyên hệ điều hành.
- **Vận hành Serverless hoàn toàn:** Tối ưu chi phí, không cần duy trì hạ tầng riêng cho hệ thống giám sát an toàn thông tin.
- **Khả năng mở rộng vượt trội:** Tự động mở rộng theo lưu lượng log và quy mô của các đợt tấn công mà không gây nghẽn hệ thống.

---

# 3. Kiến Trúc Giải Pháp

Hệ thống tuân theo kiến trúc Cloud-Native Serverless Security trên hạ tầng AWS.

## Kiến trúc giải pháp

Sơ đồ tổng quan luồng xử lý và các thành phần trong hệ thống:

**Attacker → EC2 Ubuntu → CloudWatch Logs → Subscription Filter (`Failed password`) → AWS Lambda ↔ DynamoDB (Đếm thất bại) → Amazon SNS (Gửi Mail Cảnh Báo) & Network ACL (Rule DENY /32)**

![Kiến trúc hệ thống](/images/proposal/system_architecture1.png)

## Các dịch vụ AWS sử dụng

- **Amazon EC2:** Máy chủ Linux Ubuntu chạy dịch vụ SSH.
- **Amazon CloudWatch Logs:** Dịch vụ thu thập và quản lý nhật ký hệ thống.
- **CloudWatch Subscription Filter:** Lọc sự kiện log theo biểu thức mẫu (`Failed password`).
- **AWS Lambda:** Hàm thực thi logic xử lý tự động (Python 3.12 Runtime).
- **Amazon DynamoDB:** Cơ sở dữ liệu NoSQL lưu trữ bộ đếm theo IP.
- **Amazon SNS:** Dịch vụ gửi thông báo cảnh báo qua Email.
- **Network ACL (NACL):** Tường lửa stateless bảo vệ tầng Subnet.
- **AWS IAM:** Quản lý quyền và định danh truy cập giữa các dịch vụ.

## Thiết kế thành phần

### Data Collection & Filtering Layer

- **EC2 Ubuntu:** Cấu hình CloudWatch Agent đẩy toàn bộ log đăng nhập hệ thống (`/var/log/auth.log`) về CloudWatch Logs.
- **CloudWatch Logs Group:** Nhận và lưu trữ log truy cập SSH theo thời gian thực.
- **Subscription Filter:** Thiết lập pattern bóc tách chứa chuỗi `Failed password`. Ngay khi có log khớp pattern, sự kiện sẽ được trigger trực tiếp sang AWS Lambda.

### Processing & State Management Layer

- **AWS Lambda:** Hàm Serverless nhận dữ liệu sự kiện từ CloudWatch, giải mã (base64/gzip), bóc tách địa chỉ IP nguồn (`clientIp`) bằng biểu thức chính quy (Regex).
- **Amazon DynamoDB Table (`SSHAttackCounter`):**
  - **Partition Key:** `AttackerIP` (String).
  - **Attributes:** `FailedCount` (Number), `WindowTimestamp` (Number).
  - **Logic:** Lambda thực hiện cập nhật và kiểm tra bộ đếm trong khoảng thời gian 1 phút.

### Alerting & Remediation Layer

- **Amazon SNS:** Khi số lần thất bại đạt từ **5 lần/1 phút** trở lên, hệ thống phát tin nhắn cảnh báo qua email đến quản trị viên.
- **Network ACL Enforcement:** Lambda kiểm tra danh sách quy tắc hiện tại trên Network ACL gắn liền với Subnet chứa EC2.
- **Rule Enforcement:** Nếu IP chưa bị chặn trong danh sách, Lambda tự động chèn một quy tắc `DENY` cho IP vi phạm đó dưới dạng CIDR `/32` ở thứ tự ưu tiên cao tại ranh giới Subnet.

---

# 4. Luồng Xử Lý Của Hệ Thống

## Luồng hoạt động chi tiết

![Luồng hoạt động của hệ thống](/images/proposal/system_workflow.png)

Quy trình phát hiện, cảnh báo và chặn tự động gồm 12 bước:

1. Kẻ tấn công (Attacker) thực hiện các lượt đăng nhập SSH không hợp lệ vào máy chủ Amazon EC2 Ubuntu.
2. SSH Server trên EC2 ghi nhận sự kiện đăng nhập thất bại vào file nhật ký hệ thống `/var/log/auth.log`.
3. CloudWatch Agent cài trên EC2 tự động đẩy dữ liệu log mới về CloudWatch Logs Group.
4. Subscription Filter quét log, phát hiện các chuỗi sự kiện khớp với cấu hình pattern `Failed password` và gửi payload sự kiện đến AWS Lambda.
5. AWS Lambda giải mã dữ liệu nén, sử dụng biểu thức chính quy (Regex) để bóc tách chính xác địa chỉ IP nguồn (`clientIp`).
6. Lambda truy vấn và cập nhật tăng bộ đếm số lần đăng nhập thất bại cho địa chỉ IP đó trong bảng Amazon DynamoDB.
7. Lambda kiểm tra tổng số lần thất bại trong khoảng thời gian 1 phút gần nhất.
8. Nếu số lần thất bại nhỏ hơn 5, Lambda kết thúc lượt xử lý và hệ thống tiếp tục duy trì giám sát.
9. Nếu số lần thất bại đạt từ **5 lần/1 phút** trở lên, hệ thống gửi tin nhắn cảnh báo qua mail bằng dịch vụ Amazon SNS và Lambda kích hoạt quy trình phản ứng tự động.
10. Lambda kiểm tra danh sách quy tắc hiện tại trên Network ACL gắn với Subnet chứa EC2.
11. Nếu IP chưa có trong danh sách chặn, Lambda tự động chèn một quy tắc `DENY` cho IP vi phạm dưới dạng CIDR `/32`.
12. Network ACL lập tức từ chối mọi gói tin kết nối SSH từ địa chỉ IP vi phạm ngay tại tầng mạng ranh giới Subnet.

---

# 5. Triển Khai Kỹ Thuật

## Các giai đoạn triển khai

Dự án được triển khai qua 6 bước kỹ thuật chi tiết:

1. **Khởi tạo hạ tầng EC2 & Cấu hình SSH:** Tạo máy chủ EC2 Ubuntu 22.04 LTS, cấu hình Security Group mở cổng 22.
2. **Cấu hình CloudWatch Agent & Log Group:** Cài đặt CloudWatch Agent trên EC2 để thu thập log `/var/log/auth.log` về CloudWatch Log Group.
3. **Khởi tạo Amazon DynamoDB Table & Amazon SNS Topic:** Tạo bảng `SSHAttackCounter` với Partition Key `AttackerIP` và khởi tạo SNS Topic cùng đăng ký email nhận cảnh báo.
4. **Phân quyền IAM Role:** Khởi tạo IAM Role cấp quyền cho Lambda thao tác với CloudWatch Logs, DynamoDB (`GetItem`, `PutItem`, `UpdateItem`), SNS (`Publish`) và EC2 Network ACL (`DescribeNetworkAcls`, `CreateNetworkAclEntry`).
5. **Phát triển mã nguồn AWS Lambda & Subscription Filter:** Lập trình hàm Lambda bằng Python 3.12 để bóc tách IP, đếm thất bại, kích hoạt SNS notification và tạo rule NACL; liên kết Subscription Filter từ CloudWatch Log Group tới Lambda.
6. **Kiểm thử kịch bản & Đánh giá:** Sử dụng công cụ/script giả lập tấn công Brute-Force SSH sai 5 lần/phút để kiểm tra luồng gửi mail cảnh báo SNS và tự động chặn trên Network ACL.

## Yêu cầu kỹ thuật

### Ngôn ngữ lập trình & SDKs

- Python 3.12
- AWS SDK for Python (`boto3`)

### Cloud Infrastructure & Tools

- AWS Management Console
- Amazon EC2 API (`DescribeInstances`, `DescribeNetworkAcls`, `CreateNetworkAclEntry`)
- Amazon DynamoDB API (`GetItem`, `UpdateItem`)
- Amazon SNS API (`Publish`)
- CloudWatch Logs Subscription Filter

### Testing & Simulation Tools

- OpenSSH CLI / Bash Script (Giả lập SSH Brute-Force)

---

# 6. Lộ Trình Triển Khai

| Bước | Nội Dung Công Việc Kỹ Thuật | Thành Phần Liên Quan |
| :--- | :--- | :--- |
| **Bước 1** | Khởi tạo máy chủ EC2 Ubuntu, gán IAM Role và cấu hình cổng quản trị SSH. | Amazon EC2, IAM |
| **Bước 2** | Cài đặt CloudWatch Agent trên Ubuntu, đẩy log `/var/log/auth.log` về Log Group. | CloudWatch Logs, EC2 |
| **Bước 3** | Tạo bảng DynamoDB lưu trữ bộ đếm số lần đăng nhập thất bại theo IP và cấu hình Amazon SNS Topic gửi mail. | Amazon DynamoDB, Amazon SNS |
| **Bước 4** | Thiết lập IAM Role cho Lambda cấp đủ quyền tương tác với Logs, DynamoDB, SNS và EC2. | AWS IAM |
| **Bước 5** | Triển khai mã nguồn Python Lambda, cấu hình Subscription Filter với pattern `Failed password`. | AWS Lambda, CloudWatch |
| **Bước 6** | Thực hiện kịch bản giả lập tấn công SSH 5 lần/phút, kiểm tra email cảnh báo từ SNS, kiểm tra rule DENY `/32` trên Network ACL và hoàn thiện báo cáo. | Bash / Amazon SNS / Network ACL |

---

# 7. Ước Tính Chi Phí

Chi phí vận hành giải pháp tự động cực kỳ tối ưu do áp dụng mô hình Serverless và Pay-as-you-go.

| Dịch vụ AWS | Mô tả chi phí | Chi phí dự kiến |
| :--- | :--- | :--- |
| **Amazon EC2** | Máy chủ Ubuntu t3.micro (Free Tier) | ~$0.00 - $8.50/tháng |
| **Amazon CloudWatch** | Ingestion & Storage cho Log Group | ~$0.50/tháng |
| **AWS Lambda** | Số lượng lệnh gọi API & Thời gian thực thi | ~$0.00/tháng (Free Tier) |
| **Amazon DynamoDB** | Dung lượng lưu trữ & Thao tác Read/Write (On-Demand) | ~$0.00/tháng (Free Tier) |
| **Amazon SNS** | 1,000 tin nhắn thông báo Email đầu tiên | **Miễn phí** |
| **Network ACL** | Tính năng quản lý mạng tích hợp sẵn của VPC | **Miễn phí** |
| **Tổng chi phí dự kiến** | **Chi phí vận hành hàng tháng** | **~$0.50 - $9.00 USD/tháng** |

---

# 8. Đánh Giá Rủi Ro

## Rủi ro & Giải pháp

- **Rủi ro 1 - Giới hạn số lượng Rule trên Network ACL:** Mặc định Network ACL giới hạn số lượng quy tắc (ví dụ: 20-40 rules). Nếu số lượng IP tấn công vượt quá giới hạn, việc tạo mới rule sẽ bị lỗi.
  - *Giải pháp:* Cấu hình hàm Lambda có cơ chế dọn dẹp (TTL) tự động xóa các rule DENY cũ sau một khoảng thời gian nhất định (ví dụ: sau 24 giờ) hoặc gửi cảnh báo qua SNS khi NACL đạt ngưỡng giới hạn.
- **Rủi ro 2 - Lỗi phân quyền IAM Role:** Lambda không thể tạo rule trên NACL, đọc/ghi DynamoDB hoặc gửi mail qua SNS do thiếu permission.
  - *Giải pháp:* Kiểm tra và phân quyền chính xác các hành vi `ec2:CreateNetworkAclEntry`, `dynamodb:UpdateItem`, `sns:Publish` trong IAM Policy.
- **Rủi ro 3 - Chặn nhầm IP của Quản trị viên (False Positive):** Quản trị viên gõ sai mật khẩu 5 lần liên tiếp trong 1 phút dẫn đến việc bị hệ thống tự động chặn.
  - *Giải pháp:* Bổ sung cơ chế danh sách trắng (Whitelist IP) trong code Lambda để bỏ qua không xử lý đối với các IP tĩnh của quản trị viên.

---

# 9. Kết Quả Kỳ Vọng

## Kết quả kỹ thuật

- Xây dựng thành công hệ thống tự động phát hiện, cảnh báo và chặn tấn công SSH Brute-Force trên máy chủ EC2.
- Hoàn thiện bộ đếm tần suất thông minh dựa trên DynamoDB với ngưỡng chính xác **5 lần/1 phút**.
- Tự động gửi mail thông báo cảnh báo sự cố đến quản trị viên thông qua dịch vụ Amazon SNS.
- Thực thi kịch bản chặn tự động bằng quy tắc `DENY` `/32` trên Network ACL ngay khi vượt ngưỡng.
- Loại bỏ hoàn toàn sự can thiệp thủ công của con người trong quá trình ứng phó sự cố.

## Giá trị thực tiễn

- Bảo vệ an toàn tuyệt đối cho tài nguyên máy chủ trước các đợt tấn công dò quét tự động từ Internet.
- Nâng cao tính chủ động nhận biết sự cố an ninh cho đội ngũ quản trị nhờ hệ thống cảnh báo tức thời qua Amazon SNS.
- Giảm tải cho hệ điều hành EC2 bằng cách ngắt kết nối độc hại từ tầng mạng Subnet.
- Cung cấp mô hình tham chiếu thực tế (Baseline Security Architecture) ứng dụng Serverless trong lĩnh vực an toàn thông tin trên đám mây AWS.