---
title: "Khởi tạo S3 Bucket"
date: 2026-08-24
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

Bài viết này hướng dẫn chi tiết từng bước tạo Amazon S3 Bucket dùng làm Origin Server lưu trữ website tĩnh và tải mã nguồn ứng dụng lên S3.

---

## 1. Khởi tạo Amazon S3 Bucket

### Bước 1: Truy cập dịch vụ Amazon S3

1. Đăng nhập vào **AWS Management Console**.
2. Trên thanh tìm kiếm, nhập `S3` và chọn dịch vụ **S3**.
3. Tại giao diện điều khiển S3, đảm bảo vùng làm việc hoặc Region chung được chọn đúng (ví dụ: `us-east-1` - US East N. Virginia).
4. Nhấn nút **Create bucket**.

![Truy cập S3 Console](/images/Workshop/5.4-S3/5.4.1-s3-console.png)

---

### Bước 2: Cấu hình General configuration

1. **Bucket name:** Nhập tên duy nhất trên toàn hệ thống AWS (ví dụ: `tung-static-website-2026` hoặc `portfolio-bucket-2026`).
2. **AWS Region:** Chọn **US East (N. Virginia) us-east-1**.
3. **Object Ownership:** Giữ nguyên mặc định **ACLs disabled (recommended)** để tối ưu hóa quản lý quyền truy cập.

![Cấu hình General Configuration](/images/Workshop/5.4-S3/5.4.1-general-config.png)

---

### Bước 3: Thiết lập quyền truy cập (Block Public Access)

1. Tại mục **Block Public Access settings for this bucket**:
   - Tích chọn **Block _all_ public access**.
2. _Lý do:_ Chúng ta sẽ không mở công khai S3 Bucket trực tiếp ra Internet. Mọi truy cập của người dùng sẽ đi qua CloudFront CDN thông qua cơ chế Origin Access Control (OAC) ở các bước tiếp theo.

![Bật Block All Public Access](/images/Workshop/5.4-S3/5.4.1-block-public-access.png)

---

### Bước 4: Hoàn tất khởi tạo Bucket

1. Giữ nguyên các thiết lập mặc định còn lại (Bucket Versioning, Encryption).
2. Cuộn xuống cuối trang và nhấn **Create bucket**.

![Nhấn Create Bucket](/images/Workshop/5.4-S3/5.4.1-create-bucket-finish.png)

---

## 2. Tải mã nguồn website tĩnh lên S3 Bucket

### Bước 1: Mở Bucket vừa tạo

1. Trong danh sách **Buckets**, bấm chọn tên S3 Bucket vừa khởi tạo (ví dụ: `tung-static-website-2026`).

![Chọn S3 Bucket](/images/Workshop/5.4-S3/5.4.1-select-bucket.png)

---

### Bước 2: Tải tệp lên (Upload)

1. Tại tab **Objects**, nhấn nút **Upload**.
2. Bấm **Add files** hoặc **Add folder** để chọn toàn bộ mã nguồn website từ thư mục `portfolio` đã chuẩn bị ở bước 5.3 (bao gồm `index.html`, tệp CSS, JS, hình ảnh...).
3. Kéo cuộn xuống cuối trang và nhấn nút **Upload**.

![Thêm và tải file lên S3](/images/Workshop/5.4-S3/5.4.1-upload-files.png)

---

### Bước 3: Tải lên hoàn tất

1. Đợi quá trình tải lên hiển thị thông báo **Upload succeeded** màu xanh.
2. Nhấn **Close** để quay lại danh sách đối tượng trong Bucket.

![Tải lên thành công](/images/Workshop/5.4-S3/5.4.1-upload-success.png)

---

## 3. Kiểm tra danh mục tài nguyên S3

Kiểm tra danh sách đối tượng trong Bucket để đảm bảo tệp gốc `index.html` nằm ngay tại thư mục gốc của S3 Bucket:

![Kiểm tra danh sách file](/images/Workshop/5.4-S3/5.4.1-bucket-objects-list.png)

---

## 4. Kết quả mong đợi

Sau khi hoàn thành bài thực hành này:

- S3 Bucket đã được khởi tạo thành công tại Region `us-east-1`.
- Quyền truy cập trực tiếp bị chặn hoàn toàn (**Block all public access**).
- Tệp `index.html` và toàn bộ tài nguyên của website `portfolio` đã sẵn sàng trong S3 Bucket để kết nối tới CloudFront OAC.
