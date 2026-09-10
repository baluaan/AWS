---
title: "Phân phối dữ liệu qua Amazon CloudFront"
date: 2026-08-24
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Mục tiêu

Xây dựng mạng lưới phân phối nội dung CDN (Content Delivery Network) bằng dịch vụ Amazon CloudFront, giúp tăng tốc độ truyền tải website cho người dùng toàn cầu và cấu hình bảo mật Origin Access Control (OAC) để bảo vệ S3 Bucket gốc.

---

## 1. Tổng quan

Lớp phân phối nội dung (CDN Layer) đóng vai trò là điểm tiếp nhận truy cập đầu tiên từ phía người dùng Internet. Trong dự án này, **Amazon CloudFront** được triển khai để tăng tốc tải trang nhờ hệ thống máy chủ biên (Edge Locations) toàn cầu và làm màng lọc an ninh ban đầu.

Đặc biệt, việc tích hợp cơ chế **Origin Access Control (OAC)** cho phép CloudFront ký duyệt các truy vấn gửi tới Amazon S3. Nhờ đó, S3 Bucket có thể từ chối mọi truy cập trực tiếp từ Internet mà chỉ chấp nhận các yêu cầu hợp lệ được ủy quyền từ CloudFront.

---

## 2. Quy trình triển khai

Quy trình cấu hình mạng lưới phân phối CloudFront được chia thành hai bước chính:

- **Bước 1:** Khởi tạo CloudFront Distribution kết nối với S3 Origin, bật tính năng OAC và thiết lập tệp gốc mặc định (`index.html`).
- **Bước 2:** Cập nhật S3 Bucket Policy để cấp quyền truy cập duy nhất cho CloudFront Distribution vừa tạo.

Chi tiết các thao tác thực hiện trên AWS Management Console kèm hình ảnh minh họa được trình bày chi tiết tại bài học tiếp theo: **5.5.1. Các bước tạo CloudFront Distribution**.

---

## 3. Nội dung thực hành

Thực hiện lần lượt các bài thực hành sau:

- **[5.5.1. Các bước tạo CloudFront Distribution](5.5.1-Create-CloudFront-distribution-details)**

---

## 4. Kết quả mong đợi

Sau khi hoàn thành chương này, bạn sẽ đạt được:

- Một **Amazon CloudFront Distribution** được khởi tạo thành công và liên kết với S3 Bucket.
- Cơ chế **Origin Access Control (OAC)** được thiết lập và phân quyền chính xác trên S3 Bucket Policy.
- Website tĩnh có thể truy cập an toàn thông qua đường dẫn domain của CloudFront (`dxxxxxxxxxxxx.cloudfront.net`).
- S3 Bucket gốc được bảo vệ an toàn, chặn hoàn toàn các truy cập trực tiếp không qua CloudFront.
