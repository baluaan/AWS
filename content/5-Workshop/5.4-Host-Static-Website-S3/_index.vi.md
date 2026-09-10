---
title: "Lưu trữ website trên Amazon S3"
date: 2026-08-24
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Mục tiêu

Xây dựng hạ tầng lưu trữ gốc (Origin Storage) cho website tĩnh bằng cách sử dụng dịch vụ Amazon S3, đảm bảo lưu trữ mã nguồn an toàn và tuân thủ các nguyên tắc bảo mật cơ bản trước khi đưa qua lớp phân phối nội dung CDN.

---

## 1. Tổng quan

Lớp lưu trữ gốc (Origin Layer) là nơi chứa toàn bộ mã nguồn giao diện, hình ảnh và tài nguyên tĩnh của website. Trong dự án này, **Amazon S3 (Simple Storage Service)** được lựa chọn làm Origin Server nhờ tính sẵn sàng cao, khả năng mở rộng vượt trội và chi phí vận hành tối ưu.

Mô hình triển khai áp dụng tiêu chuẩn bảo mật nâng cao: **Bật tính năng Block All Public Access** trên S3 Bucket. Điều này đảm bảo rằng các đối tượng trong Bucket không bị lộ lọt trực tiếp ra Internet qua đường link S3 chuẩn (`s3.amazonaws.com`), mà buộc mọi truy cập của người dùng phải đi qua lớp CDN **Amazon CloudFront** ở các chương tiếp theo.

---

## 2. Quy trình triển khai

Quy trình khởi tạo lớp lưu trữ gốc trên Amazon S3 được chia thành hai bước chính:

- **Bước 1:** Khởi tạo S3 Bucket riêng biệt tại Region `us-east-1` với chính sách chặn truy cập công khai toàn bộ.
- **Bước 2:** Tải mã nguồn website tĩnh (`index.html` và các tài nguyên đi kèm) lên Bucket.

Chi tiết các thao tác thực hiện trên AWS Management Console kèm hình ảnh minh họa được trình bày chi tiết tại bài học tiếp theo: **5.4.1. Các bước khởi tạo S3 Bucket**.

---

## 3. Nội dung thực hành

Thực hiện lần lượt các bài thực hành sau:

- **[5.4.1. Các bước khởi tạo S3 Bucket](5.4.1-Create-S3-bucket-details)**

---

## 4. Kết quả mong đợi

Sau khi hoàn thành chương này, bạn sẽ đạt được:

- Một **Amazon S3 Bucket** được tạo thành công tại Region `us-east-1 (N. Virginia)`.
- Cấu hình **Block All Public Access** được áp dụng triệt để trên S3 Bucket.
- Mã nguồn website tĩnh (`portfolio`) được tải lên và lưu trữ an toàn trong Bucket.
- Hạ tầng lưu trữ gốc (Origin) sẵn sàng để kết nối với Amazon CloudFront và AWS WAF ở các bước tiếp theo.
