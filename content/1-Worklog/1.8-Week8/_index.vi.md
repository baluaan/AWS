---
title: "Worklog - Tuần 8"
date: 2026-09-05
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8:

- Kiểm thử tải & Giả lập tấn công: Sử dụng các công cụ stress test (Artillery, Apache JMeter) để giả lập kịch bản tấn công HTTP Flood / DDoS truy cập dồn dập vào hệ thống.

- Kiểm thử tự động hóa End-to-End: Đánh giá toàn bộ luồng vận hành từ lúc phát sinh lượng truy cập bất thường -> WAF ghi log -> CloudWatch Log kích hoạt Alarm -> Lambda trích xuất IP vi phạm -> Cập nhật WAF Blocked IP Set -> Gửi cảnh báo qua Amazon SNS.

- Đo lường & Tối ưu hiệu năng: Đánh giá độ trễ (latency) của quá trình ứng phó tự động, tinh chỉnh tham số Metric Filter, ngưỡng Alarm và thời gian gỡ chặn IP (TTL) để giảm thiểu tối đa tỷ lệ báo động giả (False Positives).

- Tổng kết & Báo cáo nghiệm thu dự án: Thu thập dữ liệu thực nghiệm, hoàn thiện tài liệu kĩ thuật, lưu trữ các chỉ số đo lường và bàn giao hệ thống.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                                                       | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                          |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | --------------------------------------- |
| 2   | - Chuẩn bị môi trường kiểm thử tải & giả lập tấn công.<br>- Cấu hình kịch bản mô phỏng HTTP Flood với Artillery/JMeter.<br>- Thực hiện phát lưu lượng truy cập dồn dập vượt ngưỡng từ IP thử nghiệm.                            | 07/09/2026   | 07/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 3   | - Kiểm thử toàn diện luồng ứng phó tự động (End-to-End).<br>- Giám sát thời gian CloudWatch kích hoạt Alarm.<br>- Kiểm tra tính chính xác khi Lambda trích xuất IP và thêm vào WAF IP Set.<br>- Xác nhận email cảnh báo từ SNS. | 08/09/2026   | 08/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 4   | - Đánh giá độ trễ & Tinh chỉnh cấu hình.<br>- Đo lường thời gian đáp ứng tổng thể của toàn bộ luồng tự động hóa.<br>- Điều chỉnh khoảng thời gian đánh giá (Evaluation Period) và ngưỡng Alarm để tối ưu độ nhạy.               | 09/09/2026   | 09/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 5   | - Kiểm thử cơ chế tự động gỡ chặn IP (TTL).<br>- Xác minh khả năng tự động xóa IP khỏi danh sách chặn sau khoảng thời gian quy định.<br>- Kiểm thử khả năng phục hồi truy cập bình thường cho người dùng hợp lệ.                | 10/09/2026   | 10/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 6   | - Tổng kết, hoàn thiện tài liệu & Báo cáo dự án.<br>- Tổng hợp báo cáo kết quả kiểm thử và chỉ số hiệu năng.<br>- Hoàn thiện sơ đồ kiến trúc hoàn chỉnh và tài liệu hướng dẫn vận hành hệ thống.                                | 11/09/2026   | 11/09/2026      | https://cloudjourney.awsstudygroup.com/ |

### Kết quả đạt được tuần 8:

- Giả lập thành công kịch bản tấn công thực tế: Đã thực hiện kiểm thử tải thành công với kịch bản HTTP Flood, tạo ra lượng lưu lượng truy cập đủ lớn để kích hoạt các cơ chế bảo vệ của hệ thống.

- Xác minh hoàn hảo luồng tự động hóa End-to-End: Hệ thống tự động phản ứng chính xác 100% trong thực tế: phát hiện truy cập bất thường, tự động đưa IP vào danh sách chặn WAF và gửi thông báo qua SNS trong thời gian ngắn.

- Tối ưu hóa hiệu năng & Giảm thiểu báo động giả: Đo lường chính xác thời gian ứng phó toàn luồng, tinh chỉnh các tham số ngưỡng giúp hệ thống hoạt động ổn định, chính xác và giảm tối đa tác động tới người dùng hợp lệ.

- Nghiệm thu & Bàn giao dự án: Hoàn thành toàn bộ báo cáo kĩ thuật, sơ đồ kiến trúc và bộ tài liệu hướng dẫn vận hành hệ thống tự động hóa ứng phó sự cố bảo mật trên AWS.
