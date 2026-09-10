---
title: "Điều kiện chuẩn bị"
date: 2026-08-24
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

### Mục tiêu

Đảm bảo người thực hiện có đầy đủ quyền truy cập vào AWS Management Console, chuẩn bị môi trường kiểm thử cục bộ và sẵn sàng các tài nguyên mã nguồn cần thiết trước khi triển khai hệ thống bảo vệ tự động.

---

## 1. Công cụ và tài nguyên cần chuẩn bị

Workshop này thao tác chính trên **AWS Management Console (Web UI)** kết hợp với môi trường máy trạm để kiểm thử thực tế. Bạn cần chuẩn bị các yếu tố sau:

- **Tài khoản AWS (AWS Account):** Có quyền khởi tạo và quản lý các dịch vụ AWS WAF, Lambda, S3, CloudFront, CloudWatch, SNS và IAM (khuyên dùng quyền `AdministratorAccess`).
- **Trình duyệt Web (Web Browser):** Google Chrome, Microsoft Edge hoặc Mozilla Firefox bản mới nhất.
- **Công cụ kiểm thử CLI (Terminal / PowerShell):** Dùng để chạy lệnh giả lập lưu lượng truy cập/tấn công (HTTP Flood).
  - Đối với Windows: **PowerShell** (có sẵn `Invoke-WebRequest`).
  - Đối với macOS/Linux: **Terminal** (có sẵn `curl`).
- **Trình chỉnh sửa mã nguồn (Code Editor):** Visual Studio Code (hoặc Notepad++) để xem/chỉnh sửa mã nguồn Lambda (Python 3.12) và giao diện website tĩnh (`index.html`).
- **Địa chỉ Email (Gmail):** Dùng để đăng ký nhận email cảnh báo sự cố từ Amazon SNS Topic.

---

## 2. Các bước chuẩn bị chi tiết

### Bước 1: Đăng nhập AWS Console và kiểm tra Region

1. Đăng nhập vào [AWS Management Console](https://aws.amazon.com/console/).
2. Đảm bảo khu vực (Region) làm việc được chọn là **US East (N. Virginia) — us-east-1** ở góc trên bên phải màn hình Console.

> ⚠️ **LƯU Ý QUAN TRỌNG:** AWS WAF dành cho Amazon CloudFront (Global) và các tài nguyên phụ thuộc bắt buộc phải được khởi tạo và quản lý tại Region **us-east-1 (N. Virginia)**.

**Checkpoint:** Tên Region trên thanh công cụ hiển thị chính xác là **US East (N. Virginia) us-east-1**.

---

### Bước 2: Kiểm tra công cụ dòng lệnh trên máy cục bộ

Mở Terminal (macOS/Linux) hoặc PowerShell (Windows) và kiểm tra sự khả dụng của lệnh kiểm thử HTTP request:

**Trên Windows (PowerShell):**

```powershell
Get-Command Invoke-WebRequest
```

**Trên macOS/Linux (Terminal):**

```bash
curl --version
```

**Checkpoint:** Các lệnh trả về thông tin công cụ hợp lệ và sẵn sàng gửi request HTTP/HTTPS.

---

### Bước 3: Chuẩn bị tệp mã nguồn ứng dụng & Lambda

Tải hoặc tạo sẵn thư mục dự án trên máy cục bộ chứa các tệp sau:

- Tệp giao diện website tĩnh (`index.html`) dùng để host trên Amazon S3.
- Tệp mã nguồn Python (`lambda_function.py`) xử lý tự động hóa bóc tách IP vi phạm và gọi API AWS WAF.

**Checkpoint:** Mã nguồn website và kịch bản Lambda đã sẵn sàng trên máy tính để tải lên AWS ở các bước tiếp theo.

---

## 3. Kết quả mong đợi

Sau khi hoàn thành chương này, bạn sẽ đạt được các điều kiện:

- Đăng nhập thành công vào AWS Management Console tại Region **us-east-1 (N. Virginia)**.
- Chuẩn bị sẵn môi trường máy trạm với PowerShell/Terminal phục vụ kiểm thử tấn công.
- Đã có sẵn địa chỉ Email nhận thông báo cảnh báo từ SNS.
- Mã nguồn trang web tĩnh và hàm Lambda Python đã sẵn sàng để triển khai.
