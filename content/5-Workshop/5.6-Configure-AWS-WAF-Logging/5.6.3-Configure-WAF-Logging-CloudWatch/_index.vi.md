---
title: "Cấu hình WAF Access Logging sang CloudWatch Logs"
date: 2026-08-24
weight: 3
chapter: false
pre: " <b> 5.6.3. </b> "
---

Bài viết này hướng dẫn chi tiết các bước khởi tạo một Amazon CloudWatch Log Group chuẩn tên quy định và kích hoạt tính năng ghi nhật ký (Access Logging) trên AWS WAF để tự động chuyển toàn bộ log truy cập về CloudWatch.

---

## 1. Khởi tạo CloudWatch Log Group

### Bước 1: Truy cập dịch vụ CloudWatch Logs

1. Đăng nhập vào **AWS Management Console**.
2. Trên thanh tìm kiếm, nhập `CloudWatch` và chọn dịch vụ **CloudWatch**.
3. Tại menu điều hướng bên trái, mở mục **Logs** và chọn **Log groups**.
4. Đảm bảo Region đang làm việc là **US East (N. Virginia) us-east-1** (vì WAF CloudFront bắt buộc đẩy log về Region này).

![Truy cập CloudWatch Log Groups](/images/Workshop/5.6-WAF/5.6.3-cloudwatch-log-groups-menu.png)

---

### Bước 2: Tạo Log Group chuẩn tên AWS WAF

1. Nhấn nút **Create log group**.
2. **Log group name:** Nhập chính xác tiền tố bắt buộc của AWS WAF: `aws-waf-logs-cloudfront` (hoặc `aws-waf-logs-website-protection`).
   > **Lưu ý quan trọng:** Tên Log Group của AWS WAF bắt buộc phải bắt đầu bằng `aws-waf-logs-` thì WAF Console mới nhận diện và cho phép liên kết.
3. **Retention setting:** Chọn thời gian lưu trữ log (ví dụ: **1 day** hoặc **7 days** để tiết kiệm chi phí cho bài lab).
4. Nhấn **Create**.

![Khởi tạo CloudWatch Log Group](/images/Workshop/5.6-WAF/5.6.3-create-log-group.png)
![Khởi tạo CloudWatch Log Group](/images/Workshop/5.6-WAF/5.6.3-create-log-group2.png)

---

## 2. Kích hoạt WAF Logging trên Web ACL

### Bước 1: Mở giao diện Cấu hình Logging trên AWS WAF

1. Quay lại dịch vụ **AWS WAF & Shield**.
2. Chọn **Web ACLs** ở menu bên trái -> Chọn **Global (CloudFront)**.
3. Bấm chọn Web ACL `WebsiteProtectionACL` đã khởi tạo ở bài 5.6.2.
4. Chuyển sang tab **Logging and metrics**.
5. Tại mục **Logging**, nhấn nút **Enable**.

![Bật Logging trên Web ACL](/images/Workshop/5.6-WAF/5.6.3-enable-waf-logging.png)

---

### Bước 2: Liên kết với CloudWatch Log Group

1. **Logging destination:** Chọn **CloudWatch Logs log group**.
2. **CloudWatch Logs log group:** Chọn đúng Log Group `aws-waf-logs-cloudfront` vừa tạo ở Phần 1.
3. **Redacted fields (Tùy chọn):** Giữ mặc định (không ẩn trường thông tin nào) hoặc chọn ẩn các trường nhạy cảm nếu cần.
4. **Filter logs (Tùy chọn):** Giữ mặc định để ghi nhận toàn bộ log (All traffic).
5. Nhấn **Save**.

![Liên kết WAF với CloudWatch Log Group](/images/Workshop/5.6-WAF/5.6.3-link-log-destination.png)
![Liên kết WAF với CloudWatch Log Group](/images/Workshop/5.6-WAF/5.6.3-link-log-destination2.png)

---

## 3. Kiểm tra dữ liệu Log ghi nhận

### Bước 1: Phát sinh lưu lượng truy cập

1. Truy cập vào tên miền CloudFront Distribution (đã lấy ở bài 5.5.1) trên trình duyệt hoặc gửi một số request tới trang web để tạo truy cập thực tế.

---

### Bước 2: Kiểm tra Stream Log trong CloudWatch

1. Quay lại dịch vụ **CloudWatch** -> **Log groups** -> chọn `aws-waf-logs-cloudfront`.
2. Trong tab **Log streams**, kiểm tra danh sách các Log Stream mới xuất hiện chứa dữ liệu nhật ký dạng JSON của AWS WAF.

![Kiểm tra WAF Log Streams](/images/Workshop/5.6-WAF/5.6.3-verify-log-streams.png)

---

## 4. Kết quả mong đợi

Sau khi hoàn thành bài thực hành này:

- **CloudWatch Log Group** tên `aws-waf-logs-cloudfront` được khởi tạo thành công tại Region `us-east-1`.
- **WAF Access Logging** được kích hoạt thành công trên Web ACL `WebsiteProtectionACL`.
- Toàn bộ lưu lượng HTTP/HTTPS gửi tới CloudFront được ghi nhận chi tiết dưới dạng JSON Log Streams trong CloudWatch, sẵn sàng làm nguồn dữ liệu kích hoạt cho CloudWatch Alarms và Lambda tự động hóa ở bài 5.7 & 5.8.
