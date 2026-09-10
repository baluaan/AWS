---
title: "Các bài blogs đã đăng"
date: 2026-08-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Trong 8 tuần thực tập, tôi đã hoàn thiện và xuất bản **3 bài blog kỹ thuật chuyên sâu** trên cộng đồng [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj). Các bài viết tập trung giải quyết các bài toán thực tế của doanh nghiệp: tự động hóa tối ưu chi phí FinOps, kiến trúc bảo mật Zero-Trust không VPN và ứng dụng Generative AI Serverless vào tra cứu tri thức nội bộ.

| #      | Chủ đề                                                                                             | Danh mục (Theo yêu cầu FCAJ)                          | Ngày đăng  |
| ------ | -------------------------------------------------------------------------------------------------- | ----------------------------------------------------- | ---------- |
| Blog 1 | **Tối ưu chi phí EC2 & RDS tự động bằng AWS Lambda và EventBridge**                                | Tối ưu chi phí & Tự động hóa FinOps với Serverless    | 01/08/2026 |
| Blog 2 | **Triển khai AWS Verified Access trong kiến trúc bảo mật TIC 3.0 không cần VPN**                   | Mạng nâng cao & Bảo mật Zero-Trust Access             | 15/08/2026 |
| Blog 3 | **Xây dựng hệ thống RAG tra cứu tài liệu doanh nghiệp với Amazon Bedrock & OpenSearch Serverless** | Ứng dụng Generative AI & Tìm kiếm tri thức Serverless | 01/09/2026 |

---

### [3.1. Blog 1](3.1-Blog1/)

Vận hành môi trường không phải sản xuất (Dev, Test, Staging) 24/7 tạo ra lãng phí chi phí đám mây vô cùng lớn cho doanh nghiệp. Bài viết này giới thiệu một giải pháp FinOps tự động kết hợp **AWS Lambda**, **Amazon EventBridge** và **Resource Tagging** để tự động tắt các máy chủ EC2 và cơ sở dữ liệu RDS nhàn rỗi ngoài giờ làm việc. Bằng cách lên lịch tự động khởi động/dừng và xác thực thẻ tài nguyên, giải pháp này giúp các đội ngũ DevOps giảm tới **60-70%** chi phí điện toán không cần thiết mà không làm ảnh hưởng đến tiến độ công việc của lập trình viên.

---

### [3.2. Blog 2](3.2-Blog2/)

Triển khai bảo mật Zero-Trust cho khối cơ quan chính phủ và doanh nghiệp theo tiêu chuẩn TIC 3.0 (Trusted Internet Connections) thường gặp trở ngại do sự phụ thuộc vào VPN truyền thống. Bài viết này phân tích phương pháp ứng dụng **AWS Verified Access (AVA)** kết hợp với **AWS Network Firewall** và **Amazon Route 53 Resolver DNS Firewall** để xây dựng kiến trúc truy cập an toàn không cần VPN (VPN-less). Giải pháp giúp xác thực liên tục dựa trên nhận dạng người dùng (Identity) và trạng thái thiết bị (Device Posture), đáp ứng toàn bộ các yêu cầu khắt khe của khung bảo mật TIC 3.0 mà vẫn tối ưu hóa trải nghiệm làm việc từ xa.

---

### [3.3. Blog 3](3.3-Blog3/)

Hệ thống tra cứu tri thức nội bộ truyền thống dựa trên từ khóa thường gặp hạn chế lớn về ngữ cảnh và khả năng tổng hợp câu trả lời cho nhân viên. Bài viết này chi tiết hóa cách xây dựng giải pháp **Generative AI Chatbot (RAG)** chuẩn doanh nghiệp không dùng máy chủ (Serverless). Bằng cách kết hợp **Amazon Bedrock (Titan Embeddings & Claude 3)**, **Amazon OpenSearch Serverless (Vector Engine)** và **Knowledge Bases for Amazon Bedrock**, giải pháp cho phép tra cứu tài liệu nội bộ chính xác cao, tự động hóa quy trình phân mảnh (chunking), lập chỉ mục (indexing) và bảo mật dữ liệu tuyệt đối theo tiêu chuẩn AWS.
