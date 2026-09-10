---
title: "Tạo WAF IP Set"
date: 2026-08-24
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

Bài viết này hướng dẫn chi tiết các bước khởi tạo một WAF IP Set trên AWS Management Console. IP Set này đóng vai trò làm vùng chứa danh sách các địa chỉ IP vi phạm, sẵn sàng kết nối với Web ACL và cho phép AWS Lambda tự động cập nhật danh sách chặn.

---

## 1. Khởi tạo WAF IP Set

### Bước 1: Truy cập dịch vụ AWS WAF

1. Đăng nhập vào **AWS Management Console**.
2. Trên thanh tìm kiếm, nhập `WAF` và chọn dịch vụ **AWS WAF & Shield**.
3. Tại menu điều hướng bên trái, chọn **IP sets**.

![Truy cập WAF IP Sets](/images/Workshop/5.6-WAF/5.6.1-waf-ip-sets-menu.png)

---

### Bước 2: Thiết lập thông số IP Set

1. Tại mục **Region**, chọn **Global (CloudFront)**.
2. Nhấn nút **Create IP set**.
3. Nhập các thông tin chi tiết:
   - **IP set name:** `AutoBlockedIPSetV6`
   - **Description:** `IP Set chứa danh sách IP bị tự động chặn bởi Lambda`
   - **Region:** Giữ mặc định `Global (CloudFront)`
   - **IP version:** Chọn **IPv6**.
   - **IP addresses:** Giữ trống (không nhập IP nào) vì danh sách này sẽ được cập nhật tự động bởi AWS Lambda khi phát hiện vi phạm.

![Cấu hình IP Set Name và Region](/images/Workshop/5.6-WAF/5.6.1-configure-ip-set.png)

---

### Bước 3: Hoàn tất tạo IP Set

1. Kéo xuống cuối trang và nhấn nút **Create IP set**.

![Nhấn Create IP Set](/images/Workshop/5.6-WAF/5.6.1-finish-create-ip-set.png)

---

## 2. Kiểm tra danh sách WAF IP Set

Sau khi khởi tạo thành công, danh sách IP sets sẽ hiển thị `AutoBlockedIPSetV6` với các thông số:

- **Region:** Global (CloudFront)
- **IP version:** IPv4
- **Capacity:** 1

![Kiểm tra danh sách IP Set](/images/Workshop/5.6-WAF/5.6.1-verify-ip-set.png)

---

## 3. Kết quả mong đợi

Sau khi hoàn thành bài thực hành này:

- **WAF IP Set** có tên `AutoBlockedIPSetV6` được khởi tạo thành công ở Scope `Global (CloudFront)`.
- Danh sách IP ban đầu được giữ trống thành công, sẵn sàng để gán vào Web ACL ở bài 5.6.2 và để hàm Lambda ghi đè/thêm các IP vi phạm ở bài 5.8.
