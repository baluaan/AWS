---
title: "Tạo CloudFront Distribution"
date: 2026-08-24
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

Bài viết này hướng dẫn chi tiết từng bước tạo Amazon CloudFront Distribution để phân phối website tĩnh từ S3 Bucket, thiết lập cơ chế bảo mật Origin Access Control (OAC) và cập nhật S3 Bucket Policy.

---

## 1. Khởi tạo Amazon CloudFront Distribution

### Bước 1: Truy cập dịch vụ Amazon CloudFront

1. Đăng nhập vào **AWS Management Console**.
2. Trên thanh tìm kiếm, nhập `CloudFront` và chọn dịch vụ **CloudFront**.
3. Tại giao diện CloudFront Dashboard, nhấn **Create distribution**.

![Truy cập CloudFront Console](/images/Workshop/5.5-CloudFront/5.5.1-cloudfront-console.png)

---

### Bước 2: Cấu hình Origin Settings & Origin Access Control (OAC)

1. **Origin domain:** Bấm chọn S3 Bucket đã tạo ở bước 5.4 (ví dụ: `tung-static-website-2026.s3.us-east-1.amazonaws.com`).
2. **Name:** Giữ nguyên tên gợi ý mặc định.
3. **Origin access:** Tích chọn **Origin access control settings (recommended)**.
4. Bấm chọn **Create new OAC** (nếu chưa có sẵn OAC) -> Giữ nguyên tên mặc định -> Nhấn **Create**.

![Cấu hình Origin và OAC](/images/Workshop/5.5-CloudFront/5.5.1-origin-oac-config.png)

---

### Bước 3: Cấu hình Default Cache Behavior

1. **Viewer protocol policy:** Chọn **Redirect HTTP to HTTPS** để bắt buộc mã hóa toàn bộ lưu lượng truy cập.
2. **Allowed HTTP methods:** Giữ mặc định **GET, HEAD**.
3. **Cache key and origin requests:** Giữ mặc định **CachingOptimized**.

![Cấu hình Default Cache Behavior](/images/Workshop/5.5-CloudFront/5.5.1-cache-behavior.png)

---

### Bước 4: Cấu hình Web Application Firewall (WAF) & Settings

1. **Web Application Firewall (WAF):** Tạm thời chọn **Do not enable security protections** (Chúng ta sẽ tích hợp AWS WAF chi tiết ở bước 5.6).
2. **Default root object:** Nhập `index.html`.
3. Kéo xuống cuối trang và nhấn **Create distribution**.

![Cấu hình Root Object và nhấn Create](/images/Workshop/5.5-CloudFront/5.5.1-create-distribution.png)
![Cấu hình Root Object và nhấn Create](/images/Workshop/5.5-CloudFront/5.5.1-create-distribution2.png)
![Cấu hình Root Object và nhấn Create](/images/Workshop/5.5-CloudFront/5.5.1-create-distribution3.png)

---

## 2. Cập nhật S3 Bucket Policy với OAC

Sau khi khởi tạo CloudFront Distribution thành công, một thông báo màu vàng sẽ xuất hiện yêu cầu cập nhật S3 Bucket Policy để phân quyền cho OAC.

### Bước 1: Sao chép Bucket Policy từ CloudFront

1. Tại trang chi tiết của CloudFront Distribution vừa tạo, nhấn nút **Copy policy** trong hộp thông báo màu xanh dương.

![Copy S3 Bucket Policy](/images/Workshop/5.5-CloudFront/5.5.1-copy-bucket-policy.png)

---

### Bước 2: Dán Policy vào Amazon S3 Bucket

1. Quay lại dịch vụ **Amazon S3** và chọn S3 Bucket của bạn.
2. Chuyển sang tab **Permissions**.
3. Tại mục **Bucket policy**, nhấn **Edit**.
4. Dán đoạn Policy vừa copy từ CloudFront vào khung chỉnh sửa JSON.
5. Nhấn **Save changes**.

![Cập nhật S3 Bucket Policy](/images/Workshop/5.5-CloudFront/5.5.1-update-s3-policy.png)

![Cập nhật S3 Bucket Policy](/images/Workshop/5.5-CloudFront/5.5.1-update-s3-policy2.png)

---

## 3. Kiểm tra kết quả phân phối (Distribution Domain Name)

### Bước 1: Lấy Domain Name của CloudFront

1. Quay lại giao diện **CloudFront Distributions**.
2. Tìm cột **Domain name** hoặc sao chép chuỗi **Distribution domain name** trong trang chi tiết (ví dụ: `dxd2hh9ocuujg.cloudfront.net`).

![Lấy CloudFront Domain Name](/images/Workshop/5.5-CloudFront/5.5.1-get-domain-name.png)

---

### Bước 2: Truy cập website kiểm thử

1. Đợi trạng thái **Last modified** của Distribution chuyển từ `Deploying` sang mốc thời gian hoàn tất.
2. Mở thẻ trình duyệt mới và truy cập đường dẫn: `https://dxd2hh9ocuujg.cloudfront.net/`

![Kiểm tra truy cập CloudFront Domain](/images/Workshop/5.5-CloudFront/5.5.1-test-website-access.png)

---

## 4. Kết quả mong đợi

Sau khi hoàn thành bài thực hành này:

- **CloudFront Distribution** được khởi tạo thành công và liên kết chính xác với S3 Origin.
- **S3 Bucket Policy** được cập nhật phân quyền OAC thành công, chặn hoàn toàn truy cập trực tiếp URL S3 và chỉ chấp nhận request từ CloudFront.
- Website giao diện `portfolio` truy cập mượt mà, an toàn qua giao thức HTTPS bằng tên miền CloudFront Domain Name.
