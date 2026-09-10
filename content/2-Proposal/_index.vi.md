---
title: "Đề xuất"
date: 2026-08-24
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Automated Threat Protection

## Automated Threat Protection với AWS WAF & Lambda

---

# 1. Tóm Tắt Dự Án

**Automated Threat Protection** là một giải pháp an ninh mạng dựa trên hạ tầng đám mây (Cloud-Native Security Solution), được thiết kế nhằm tự động hóa việc phát hiện, phản ứng và ngăn chặn các hành vi tấn công mạng (như DDoS, Brute Force, Web Scraping, HTTP Flood) hướng tới hệ thống website tĩnh được lưu trữ trên Amazon S3 và phân phối qua Amazon CloudFront.

Hệ thống tận dụng tối đa các dịch vụ quản lý của AWS bao gồm **Amazon S3**, **Amazon CloudFront**, **AWS WAF**, **Amazon CloudWatch**, **Amazon SNS** và **AWS Lambda**. Bằng cách liên tục phân tích log/metrics theo thời gian thực từ WAF, hệ thống chủ động nhận diện các địa chỉ IP có dấu hiệu bất thường, kích hoạt chuỗi tự động hóa để đọc log, trích xuất IP vi phạm, tự động thêm các IP này vào danh sách chặn (**WAF IP Set V6**) và gửi email thông báo cảnh báo tức thì tới đội ngũ quản trị viên.

Giải pháp đảm bảo khả năng mở rộng vượt trội, độ trễ cực thấp trong việc ngăn chặn tấn công, tối ưu chi phí vận hành và loại bỏ tối đa sự can thiệp thủ công của con người trong quy trình ứng phó sự cố an ninh thông tin (Incident Response).

---

# 2. Đặt Vấn Đề

## Vấn đề hiện tại

Hiện nay, các ứng dụng web ngày càng trở thành mục tiêu hàng đầu của các cuộc tấn công mạng tự động. Các phương pháp phòng vệ và ứng phó sự cố truyền thống đang gặp nhiều hạn chế:

- **Phản ứng thủ công chậm trễ:** Khi xảy ra các cuộc tấn công Brute Force hoặc HTTP Flood, kỹ sư vận hành phải kiểm tra log thủ công, xác định IP độc hại và thêm IP vào firewall bằng tay. Quy trình này mất nhiều thời gian, dẫn đến thời gian gián đoạn dịch vụ (downtime) kéo dài.
- **Thiếu khả năng tự động hóa theo thời gian thực:** Các giải pháp tường lửa tĩnh không thể linh hoạt điều chỉnh quy tắc dựa trên lượng lưu lượng (traffic) biến động liên tục.
- **Tốn kém tài nguyên hệ thống:** Cuộc tấn công trôi qua mà không được chặn ngay tại cửa ngõ (Edge Location) sẽ đi sâu vào backend, làm quá tải máy chủ ứng dụng và cơ sở dữ liệu.
- **Thiếu cơ chế cảnh báo tập trung:** Đội ngũ an ninh thiếu thông tin tức thời về quy mô, tần suất và nguồn gốc của các đợt tấn công.

## Giải pháp

Giải pháp được đề xuất là xây dựng một hệ thống **Automated Threat Protection** tự động hóa hoàn toàn quy trình phát hiện và chặn IP tấn công trên AWS chuẩn kiến trúc:

- **Amazon S3 (Origin):** Lưu trữ mã nguồn website tĩnh và thực hiện chế độ Block Public Access an toàn.
- **Amazon CloudFront:** Phân phối nội dung qua mạng lưới CDN và bảo vệ ứng dụng ở vòng ngoài cùng (Edge Location) thông qua tích hợp với AWS WAF.
- **AWS WAF:** Lọc lưu lượng HTTP/HTTPS qua các luật kiểm soát tốc độ (`Rate-based Rule`) và danh sách IP bị chặn (`IP Set Rule`). Đồng thời đẩy log truy cập tới CloudWatch Logs Group (`aws-waf-logs-cloudfront`).
- **Amazon CloudWatch Alarm:** Giám sát chỉ số `BlockedRequests` từ WAF. Khi lưu lượng vượt ngưỡng cấu hình (chuyển sang trạng thái _In Alarm_), CloudWatch gửi tín hiệu cảnh báo tới **Amazon SNS Topic**.
- **Amazon SNS:** Đóng vai trò làm điểm điều phối cảnh báo trung tâm, vừa trực tiếp gửi **Email Notification** tới người quản trị, vừa đóng vai trò làm **Trigger** kích hoạt **AWS Lambda Function**.
- **AWS Lambda:** Hàm tự động chạy quét dữ liệu từ **CloudWatch Logs**, bóc tách địa chỉ IP vi phạm (`clientIp`), thực hiện phân loại định dạng subnet IP và cập nhật danh sách vào **WAF IP Set** để chặn truy cập vĩnh viễn.

## Lợi ích

- **Tự động chặn tấn công gần như tức thì (Near Real-Time Blocking):** Giảm thời gian phản ứng từ vài giờ xuống còn vài giây.
- **Chặn ngay tại cửa ngõ (Edge Protection):** Ngăn chặn traffic độc hại từ cửa ngõ CloudFront trước khi nó tiếp cận tài nguyên gốc.
- **Tiết kiệm chi phí & tài nguyên:** Giảm tải cho hệ thống và tối ưu chi phí hạ tầng trong thời gian bị tấn công.
- **Vận hành Serverless hoàn toàn:** Không cần quản lý hay bảo trì máy chủ phục vụ cho hệ thống giám sát an toàn thông tin.
- **Khả năng mở rộng cao:** Dễ dàng bổ sung các kịch bản phát hiện tấn công mới hoặc mở rộng theo quy mô lưu lượng.

---

# 3. Kiến Trúc Giải Pháp

Hệ thống tuân theo kiến trúc Cloud-Native Serverless Security trên hạ tầng AWS.

## Kiến trúc giải pháp

Sơ đồ tổng quan luồng xử lý và các thành phần trong hệ thống:

**S3 (Origin) → CloudFront (Distribution) → AWS WAF (Rate-based + IP Set V6) → CloudWatch Logs / Alarm → Amazon SNS → AWS Lambda → WAF IP Set V6 & Email Notification**

![Kiến trúc hệ thống](/images/proposal/system_architecture1.png)

## Các dịch vụ AWS sử dụng

- Amazon S3 (Origin storage)
- Amazon CloudFront (CDN Edge protection)
- AWS WAF (Web Application Firewall)
- Amazon CloudWatch (Logs group `aws-waf-logs-cloudfront` & Alarms)
- Amazon SNS (Simple Notification Service)
- AWS Lambda (Python runtime)
- AWS IAM (Identity and Access Management)

## Thiết kế thành phần

### Edge & Traffic Filtering Layer

- **Amazon S3:** Bucket cấu hình chặn hoàn toàn truy cập công khai (Block All Public Access), đóng vai trò làm Origin.
- **Amazon CloudFront:** Tích hợp với S3 qua Origin Access Control (OAC), buộc mọi traffic phải đi qua CloudFront và HTTPS.
- **AWS WAF:** Được gắn trực tiếp vào CloudFront Distribution. Bao gồm:
  - _Rate-based Rule (`BlockSpamRateLimit`):_ Tự động phát hiện và ngăn chặn nếu request vượt ngưỡng.
  - _IP Set Rule (`BlockAutoIPSetRuleV6`):_ Áp dụng danh sách IP vi phạm (`AutoBlockedIPSetV6`) để chặn vĩnh viễn.

### Detection & Monitoring Layer

- **CloudWatch Logs:** Nhận và lưu trữ log trực tiếp từ WAF qua Log Group `aws-waf-logs-cloudfront`.
- **CloudWatch Alarms:** Theo dõi chỉ số `BlockedRequests` của Web ACL. Khi đạt điều kiện ngưỡng (ví dụ: Sum >= 1 trong 1 phút), Alarm sẽ đổi trạng thái và bắn tín hiệu sang Amazon SNS.

### Automation & Alerting Layer

- **Amazon SNS Topic (`WAFAlertTopic`):** Tiếp nhận tín hiệu từ CloudWatch Alarm.
  - Gửi email cảnh báo trực tiếp tới hộp thư Email của Quản trị viên (Email Subscription).
  - Kích hoạt (Trigger) hàm **AWS Lambda Function**.
- **AWS Lambda (`WAFAutoBlockFunction`):** Hàm Serverless viết bằng Python, thực thi logic:
  1. Truy vấn CloudWatch Log Group `aws-waf-logs-cloudfront` trong khoảng thời gian gần nhất.
  2. Bóc tách địa chỉ IP vi phạm (`clientIp`) từ dữ liệu JSON log.
  3. Phân loại định dạng CIDR IPv6 (`/128`) hoặc IPv4 (`/32`).
  4. Lấy dữ liệu WAF IP Set hiện tại và cập nhật danh sách IP mới thông qua AWS WAF API (`GetIPSet`, `UpdateIPSet`).
  5. Đẩy tin nhắn cảnh báo thành công qua SNS.

---

# 4. Triển Khai Kỹ Thuật

## Các giai đoạn triển khai

Dự án được triển khai qua 7 bước kỹ thuật chi tiết:

1. **Host Website tĩnh trên Amazon S3:** Khởi tạo S3 Bucket tại Region `us-east-1`, bật cấu hình Block Public Access và tải mã nguồn ứng dụng.
2. **Phân phối dữ liệu qua CloudFront:** Tạo Distribution kết nối S3 Bucket qua OAC, cấu hình điều hướng Redirect HTTP to HTTPS và dán Bucket Policy.
3. **Cấu hình AWS WAF & Logging:** Khởi tạo IP Set IPv6 (`AutoBlockedIPSetV6`), tạo Web ACL gắn kèm CloudFront, thiết lập Rate-based Rule & IP Set Rule, đồng thời bật CloudWatch Logging dẫn về Log Group `aws-waf-logs-cloudfront`.
4. **Khởi tạo Amazon SNS Topic:** Tạo SNS Topic `WAFAlertTopic` và tạo Email Subscription xác thực qua hộp thư Gmail.
5. **Tạo IAM Role và Hàm AWS Lambda:** Khởi tạo IAM Role `LambdaWAFAutoBlockRole` với các quyền `logs`, `wafv2`, `sns`; tạo Lambda Function `WAFAutoBlockFunction` (Python 3.12), gán 4 biến môi trường (`IP_SET_NAME`, `IP_SET_ID`, `LOG_GROUP_NAME`, `SNS_TOPIC_ARN`) và thiết lập SNS Trigger.
6. **Cấu hình CloudWatch Alarm đi qua SNS:** Tạo Metric Alarm giám sát chỉ số `BlockedRequests` và thiết lập Action gửi notification tới `WAFAlertTopic` khi chuyển sang trạng thái _In Alarm_.
7. **Kịch bản kiểm thử & Dọn dẹp tài nguyên:** Chạy PowerShell Script giả lập tấn công gửi liên tục requests để xác minh luồng chặn tự động và thực hiện dọn dẹp tài nguyên theo quy trình an toàn.

## Yêu cầu kỹ thuật

### Ngôn ngữ lập trình & SDKs

- Python 3.12
- AWS SDK for Python (`boto3`)

### Cloud Infrastructure & Tools

- AWS Management Console (Region: `us-east-1` / Global)
- AWS WAFv2 API (`GetIPSet`, `UpdateIPSet`)
- CloudWatch Logs Filter API (`filter_log_events`)

### Testing & Simulation Tools

- PowerShell / Bash CLI (`Invoke-WebRequest` / `curl`)

---

# 5. Lộ Trình Triển Khai

Dự án triển khai theo 7 bước quy chuẩn kỹ thuật sau:

| Bước       | Nội Dung Công Việc Kỹ Thuật                                                                                                                    | Thành Phần Liên Quan     |
| :--------- | :--------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------- |
| **Bước 1** | Tạo S3 Bucket, chặn public access hoàn toàn và upload mã nguồn website.                                                                        | Amazon S3                |
| **Bước 2** | Triển khai CloudFront distribution kết nối S3 qua OAC, cập nhật S3 Bucket Policy.                                                              | CloudFront, S3           |
| **Bước 3** | Khởi tạo WAF IP Set V6, tạo Web ACL gán Rate-based + IP Set rules, bật WAF Logging về Log Group `aws-waf-logs-cloudfront`.                     | AWS WAF, CloudWatch Logs |
| **Bước 4** | Khởi tạo SNS Topic `WAFAlertTopic` và cấu hình Email Subscription tới Gmail.                                                                   | Amazon SNS               |
| **Bước 5** | Phân quyền IAM Role `LambdaWAFAutoBlockRole`, triển khai mã nguồn Python Lambda, cài đặt 4 biến môi trường và liên kết SNS Trigger.            | AWS Lambda, IAM, SNS     |
| **Bước 6** | Tạo CloudWatch Alarm theo dõi `BlockedRequests` gắn Action phát thông báo tới SNS Topic.                                                       | CloudWatch Alarm, SNS    |
| **Bước 7** | Chạy script giả lập HTTP request từ máy trạm, kiểm tra log, xác minh IP bị tự động thêm vào IP Set, nhận mail và thực hiện dọn dẹp tài nguyên. | PowerShell / AWS Console |

---

# 6. Ước Tính Chi Phí

## Ước tính chi phí hạ tầng

Chi phí vận hành giải pháp tự động này cực kỳ tối ưu do áp dụng mô hình Serverless và Pay-as-you-go.

| Dịch vụ AWS              | Mô tả chi phí                                      | Chi phí dự kiến      |
| :----------------------- | :------------------------------------------------- | :------------------- |
| **AWS WAF**              | Web ACL & Rule Groups                              | ~$5.00/tháng         |
| **Amazon CloudFront**    | Lưu lượng phân phối (Gói Free Tier)                | ~$0.50/tháng         |
| **Amazon CloudWatch**    | Metrics, Logs (`aws-waf-logs-cloudfront`) & Alarms | ~$0.30/tháng         |
| **AWS Lambda**           | Số lượng yêu cầu & Thời gian thực thi              | ~$0.05/tháng         |
| **Amazon SNS**           | Số lượng tin nhắn cảnh báo gửi đi                  | ~$0.01/tháng         |
| **Tổng chi phí dự kiến** | **Chi phí vận hành hàng tháng**                    | **~$5.86 USD/tháng** |

### Hướng dẫn tối ưu & Dọn dẹp tài nguyên (Post-demo Cleanup)

Để tránh phát sinh chi phí duy trì sau khi kiểm thử, tài nguyên **bắt buộc** phải được dọn dẹp theo thứ tự phụ thuộc ngược từ cuối lên đầu:

1. **Xóa CloudWatch Alarm:** Xóa `WAF-BlockedRequests-Alarm`.
2. **Xóa Lambda & IAM:** Xóa Lambda Function `WAFAutoBlockFunction`, IAM Role `LambdaWAFAutoBlockRole` và Policy `LambdaWAFAutoBlockPolicy`.
3. **Xóa SNS Topic:** Xóa Topic `WAFAlertTopic`.
4. **Dỡ bỏ AWS WAF & IP Set:** Gỡ WAF khỏi CloudFront, disable logging, xóa `WebsiteProtectionACL` và xóa IP Set `AutoBlockedIPSetV6`.
5. **Xóa Log Groups:** Xóa các Log Groups `/aws/lambda/WAFAutoBlockFunction` và `aws-waf-logs-cloudfront`.
6. **Vô hiệu hóa & Xóa CloudFront:** Chuyển CloudFront sang `Disabled` rồi tiến hành Delete.
7. **Xóa S3 Bucket:** Bấm `Empty` toàn bộ object rồi thực hiện `Delete` Bucket.

---

# 7. Đánh Giá Rủi Ro

## Rủi ro & Giải pháp

- **Rủi ro 1 - Lỗi phân quyền IAM hoặc thiếu biến môi trường:** Hàm Lambda thực thi thất bại do không có đủ quyền gọi API WAF/Logs/SNS hoặc nhập sai giá trị cấu hình.
  - _Giải pháp:_ Thiết lập chính xác IAM Policy cấp quyền `logs`, `wafv2`, `sns` và khai báo đầy đủ 4 biến môi trường chuẩn trong Lambda Configuration.
- **Rủi ro 2 - Cấu hình sai thứ tự dọn dẹp tài nguyên:** Bị lỗi "Resource in use" hoặc không xóa được tài nguyên do còn liên kết phụ thuộc.
  - _Giải pháp:_ Thực hiện nghiêm ngặt quy trình dọn dẹp theo thứ tự 7 bước ngược từ cuối lên đầu.
- **Rủi ro 3 - Độ trễ ghi log WAF:** Độ trễ từ WAF đẩy về CloudWatch Logs có thể khiến Lambda không lọc ngay được IP vi phạm nếu chỉ quét thời gian ngắn.
  - _Giải pháp:_ Thiết lập mã nguồn Lambda thực hiện quét Log trong cửa sổ thời gian 15 phút gần nhất (`fifteen_min_ago = now - (15 * 60 * 1000)`) để bù đắp độ trễ ghi log.

---

# 8. Kết Quả Kỳ Vọng

## Kết quả kỹ thuật

Sau khi hoàn thành, dự án sẽ đem lại:

- Giải pháp bảo mật website tĩnh trên S3 + CloudFront tự động hóa 100% việc chặn địa chỉ IP bất thường.
- Luồng hoạt động khép kín chuẩn kiến trúc: CloudWatch Alarm phát hiện sự cố -> kích hoạt SNS -> SNS gửi mail cảnh báo cho Admin & kích hoạt Lambda -> Lambda đọc CloudWatch Logs trích xuất IP -> Lambda cập nhật WAF IP Set để chặn vĩnh viễn.
- Cảnh báo tức thời được gửi tự động qua Gmail của nhà quản trị.

## Giá trị thực tiễn

- Giảm thiểu đáng kể thao tác can thiệp thủ công từ đội ngũ vận hành hệ thống.
- Nâng cao tính sẵn sàng và độ tin cậy cho ứng dụng web trước các đợt tấn công tự động từ Internet.
- Cung cấp mô hình tham chiếu chuẩn (Baseline Architecture) cho việc triển khai Serverless Security trên AWS.
