---
title: "Kiểm thử hệ thống"
date: 2026-09-11
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

## Mục tiêu

Sau khi hoàn tất cấu hình toàn bộ hạ tầng (EC2, CloudWatch Logs, Metric Filter, Subcriprion filter, CloudWatch Alarm, AWS Lambda, DynamoDB, NACL, SNS Topic), bước kiểm thử này nhằm xác minh khả năng tự động phản ứng của hệ thống đối với đợt tấn công SSH.

---
### Kịch bản kiểm thử
Sử dụng kali linux thực hiện tấn công hydra vào địa chỉ ip máy EC2 Unbutu để kich hoạt phản ứng tự động

---
### Các bước thực hiện kiểm thử

#### Bước 1: Thực hiện tấn công bằng kali linux

Mở Command Prompt (CMD) trên máy Kali linux để sử dụng công cụ hydra tấn công đăng nhập vào user: `ssh` với danh sách mật khẩu có sẵn.

```cmd
hydra -l ssh -P /usr/share/wordlists/rockyou.txt ssh://44.202.74.162 -t 4 
```

#### Bước 2: Kiểm tra Log và CloudWatch Alarm

**CloudWatch Logs:**

- Truy cập **CloudWatch > Log management > /aws/ec2/security/auth**
- Kiểm tra các Log Stream mới nhất để xem lịch sử bị tấn công cùng các log hiện thị.
![Các log mới nhất khi bị tấn công](/images/5/8/1.png)

**CloudWatch Alarm:**

- Truy cập **CloudWatch > Alarms > All alarms**.
- Quan sát báo động `SSH-BruteForce-Detected`. Sau 1–3 phút, Metric Filter phát hiện vượt ngưỡng 5 lần / 1 phút và Alarm chuyển sang trạng thái **In alarm**.

![Cảnh báo từ Alarm ](/images/5/8/2.png)


#### Bước 3: Kiểm tra dữ liệu tại DynamoDB

- Truy cập **DynamoDB > Tables > SSHBruteForceCounter > Explore table items**
- Quan sát danh sách bảng `SSHBruteForceCounter` để xem số lượt đăng nhập thất bại.
![Bảng DynamoDb đếm số lần đăng nhập thất bại](/images/5/8/3.png)


#### Bước 4: Kiểm tra nhật ký thực thi AWS Lambda

- Truy cập **AWS Lambda > chọn hàm `WAFAutoBlockFunction` > chọn tab Monitor > chọn View CloudWatch logs**.
- Mở Log Stream mới nhất và kiểm tra nội dung nhật ký:
![Nhật ký thực thi của hàm Lambda ](/images/5/8/4.png)

#### Bước 5: Kiểm tra danh sách ip đã chặn trong NACL

- Truy cập **VPC > Network ACLs > acl-050ea4da0cd7e08aa / Name: SSH-Auto-Block-NACL**
- Tìm Inbound rule và quan sát danh sách ip bị chặn.
- Địa chỉ ip `58.187.56.50/32` đã được thêm vào danh sách chặn.

![Inbound rule chặn ip](/images/5/8/5.png)

#### Bước 6: Kiểm tra Mail thông báo từ SNS

Kiểm tra hộp thư đến của Gmail đã đăng ký với SNS Topic, bạn sẽ nhận được email cảnh báo `SH-BruteForce-Detected`
![Cảnh báo gửi về mail](/images/5/8/6.png)


---
### Đánh giá kết quả

| Hạng mục kiểm thử   | Trạng thái kỳ vọng                              | Trạng thái thực tế                                      | Kết luận   |
| ------------------- | ----------------------------------------------- | ------------------------------------------------------- | ---------- |
| Ghi nhận Log WAF    | Đẩy log truy cập về CloudWatch Logs             | Log xuất hiện trong Log Group  | Đạt (PASS) |
| Kích hoạt Alarm     | Chuyển sang `IN ALARM` khi > 5 lần đăng nhập sai /1 min | Alarm kích hoạt chính xác khi đạt ngưỡng                | Đạt (PASS) |
| Tự động hóa Lambda  | Trích xuất chính xác IP vi phạm (`ATTACKER IP`)    | Trích xuất thành công IPv6/IPv4 vi phạm                 | Đạt (PASS) |
| Cập nhật NACL | Chặn IP của kẻ tấn công            | Đã từ chối `deny` các thông tin đi vào từ địa chỉ ip attacker             | Đạt (PASS) |
| Dữ liệu DynamoDB    | Đếm được số lần đăng nhập sai của 1 ip | Đếm được số lần đăng nhập sai của 1 ip         | Đạt (PASS) |
| Gửi thông báo Email | Gửi email thông báo về sự cố         | Email SNS gửi về Gmail để cảnh báo sự cố            | Đạt (PASS) |

**Đánh giá chung:** Hệ thống phản ứng hoàn toàn tự động, phát hiện và ngăn chặn thành công địa chỉ ip tấn công theo đúng thiết kế kiến trúc đề ra.