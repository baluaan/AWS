---
title: "Tạo và cấu hình Web ACL"
date: 2026-08-24
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

Bài viết này hướng dẫn chi tiết các bước tạo mới một Web Access Control List (Web ACL) trên AWS WAF, cấu hình các quy tắc bảo vệ (Rate-based rule và IP Set rule) và liên kết trực tiếp với Amazon CloudFront Distribution để bảo vệ hệ thống.

---

## 1. Khởi tạo Web ACL

### Bước 1: Truy cập tạo Web ACL

1. Đăng nhập vào **AWS Management Console**.
2. Truy cập dịch vụ **AWS WAF & Shield**.
3. Tại menu điều hướng bên trái, chọn **Web ACLs**.
4. Tại mục **Region**, chọn **Global (CloudFront)**.
5. Nhấn nút **Create web ACL**.

![Truy cập Web ACLs](/images/Workshop/5.6-WAF/5.6.2-web-acl-menu.png)

---

### Bước 2: Cấu hình thông tin chung (Describe web ACL)

1. **Name:** `WebsiteProtectionACL`
2. **Description:** `Web ACL bao gom Rate-based rule va IP Block rule bao ve CloudFront`
3. **Resource type:** Giữ mặc định **CloudFront distributions**.
4. **Associated AWS resources:**
   - Nhấn nút **Add AWS resources**.
   - Chọn **Amazon CloudFront distributions**.
   - Tích chọn tên CloudFront Distribution đã khởi tạo ở bài 5.5.
   - Nhấn **Add**.
5. Nhấn **Next**.

![Cấu hình thông tin Web ACL](/images/Workshop/5.6-WAF/5.6.2-describe-web-acl.png)
![Cấu hình thông tin Web ACL](/images/Workshop/5.6-WAF/5.6.2-describe-web-acl2.png)

---

## 2. Cấu hình Rules và Actions

### Bước 1: Thêm IP Set Rule (Chặn các IP vi phạm)

1. Tại bước **Add rules and rule groups**, nhấn **Add rules** -> chọn **Add my own rules and rule groups**.
2. Cấu hình quy tắc chặn IP:
   - **Rule type:** Chọn **IP set**.
   - **Name:** `BlockAutoIPSetRule`
   - **IP set:** Chọn IP Set `AutoBlockedIPSetV6` đã tạo ở bài 5.6.1.
   - **Source IP location:** Chọn **Source IP address**.
   - **Action:** Chọn **Block**.
3. Nhấn **Add rule**.

![Cấu hình IP Set Rule](/images/Workshop/5.6-WAF/5.6.2-add-ip-set-rule.png)

---

### Bước 2: Thêm Rate-based Rule (Giới hạn lưu lượng truy cập)

1. Tiếp tục nhấn **Add rules** -> chọn **Add my own rules and rule groups**.
2. Cấu hình quy tắc Rate limit:
   - **Rule type:** Chọn **Rate-based rule**.
   - **Name:** `HTTPRateLimitRule`
   - **Rate limit:** Nhập ngưỡng giới hạn, ví dụ: `100` (hoặc `100` - `2000` tùy theo yêu cầu bài lab).
   - **Evaluation window:** Chọn **5 minutes** (hoặc thời gian mặc định).
   - **Criteria to aggregate requests:** Chọn **IP address** -> **Source IP address**.
   - **Action:** Chọn **Block**.
3. Nhấn **Add rule**.

![Cấu hình Rate-based Rule](/images/Workshop/5.6-WAF/5.6.2-add-rate-rule.png)

---

### Bước 3: Cấu hình Default Web ACL Action

1. Tại mục **Default action**, chọn **Allow** (Cho phép tất cả các request thông thường không vi phạm quy tắc).
2. Nhấn **Next**.

---

## 3. Hoàn tất cài đặt Web ACL

### Bước 1: Thiết lập thứ tự ưu tiên (Set rule priority)

1. Giữ nguyên thứ tự quy tắc (đảm bảo `BlockAutoIPSetRule` đứng trên hoặc được đánh giá hợp lý so với `HTTPRateLimitRule`).
2. Nhấn **Next**.

---

### Bước 2: Review và tạo Web ACL

1. Xem lại toàn bộ thông số đã thiết lập.
2. Nhấn **Create web ACL** ở cuối trang.

![Hoàn tất tạo Web ACL](/images/Workshop/5.6-WAF/5.6.2-finish-web-acl.png)

---

## 4. Kết quả mong đợi

Sau khi hoàn thành bài thực hành này:

- **Web ACL** có tên `WebsiteProtectionACL` được khởi tạo thành công tại Scope `Global (CloudFront)`.
- Tích hợp thành công **IP Set Rule** (`BlockAutoIPSetRule`) kết nối với `AutoBlockedIPSetV6`.
- Tích hợp thành công **Rate-based Rule** (`HTTPRateLimitRule`) để theo dõi và giới hạn lượng request từ một IP.
- Web ACL đã gắn thành công vào **CloudFront Distribution**, sẵn sàng lọc và xử lý lưu lượng mạng ở lớp Edge.
