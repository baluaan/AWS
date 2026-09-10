---
title: "Sự kiện 1 - AWS VIETNAM COMMUNITY MEETUP"
date: 2026-07-25
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# AWS VIETNAM COMMUNITY MEETUP

## Thông tin sự kiện

| Mục             | Chi tiết                                                             |
| --------------- | -------------------------------------------------------------------- |
| **Tên sự kiện** | AWS VIETNAM COMMUNITY MEETUP                                         |
| **Ngày**        | Thứ Bảy, 25/07/2026                                                  |
| **Thời gian**   | 08:30 – 12:00 (GMT+7)                                                |
| **Địa điểm**    | AWS Hà Nội – Tầng 7, Grand Terra Tower, 36 Cát Linh, Đống Đa, Hà Nội |
| **Vai trò**     | Người tham dự                                                        |

---

# Báo cáo tổng kết

## Mục tiêu của sự kiện

AI đang thay đổi cách chúng ta xây dựng sản phẩm, phát triển doanh nghiệp và giải quyết các bài toán thực tế. Bạn đã sẵn sàng khám phá những xu hướng AI mới nhất và cách áp dụng chúng trên AWS chưa?

Tham gia AWS Vietnam Community Meetup để gặp gỡ các AWS Community Heroes, Community Builders và các chuyên gia đang trực tiếp triển khai AI trong doanh nghiệp.

---

## Diễn giả

- Hồ Việt Anh
- Tuấn Vũ
- Nguyễn Thu
- Nam La
- Đức Bùi

---

# Những nội dung nổi bật

## OpenClaw – The Rise and Practice of Open-Source AI Agents

- OpenClaw thực chất là gì - một agent runtime mã nguồn mở
- Kiến trúc phân lớp của OpenClaw (L1–L4)
- Các use case thực tế và khả năng multi-agent nâng cao
- Những điểm bắt đầu "vỡ vụn" (rủi ro hệ thống)
- Ý nghĩa của điều này đối với các developer

## AI Trends Through the Lens of an Infrastructure Engineer

- Bối cảnh chung: AI đang định hình lại hạ tầng (infrastructure) như thế nào
- AI đã thay đổi công việc của Infrastructure Engineer ra sao
- AI trong toàn bộ infrastructure stack: IaC, AIOps, FinOps, Security, GenAI coding
- Bước tiếp theo: hạ tầng agentic và AI-native
- Kỹ năng và thách thức phía trước

## Community Update

- Những cột mốc đáng nhớ đã định hình sự phát triển của cộng đồng qua các năm.
- Các hoạt động chia sẻ kiến thức trong những buổi gặp mặt.
- Hợp tác và làm việc cùng AWS tại Mỹ.

## Ship Fast with AI, Not by AI

- Encode knowledge as infrastructure
- Xây dựng ngôn ngữ chung với agent
- Lưu docs ngay trong repo
- Code càng critical, hãy viết càng nhiều code để verify
- Xây dựng một superpowered engineer

## From AI Trends to Business Value

- AI đã giúp Sales tạo ra giá trị kinh doanh như thế nào?
- Sales dành thời gian của mình cho việc gì?
- AI hỗ trợ ở mọi giai đoạn
- AI không chốt deal. Con người mới chốt deal.

---

# Kiến thức tiếp thu

## 1. Ship Fast with AI, Not by AI

- **Inner Loop** — Vòng viết → chạy → debug. AI đã giúp vòng này nhanh hơn khoảng 10 lần.
- **Outer Loop** — Vòng review → tích hợp → deploy → vận hành. Vẫn hoàn toàn phụ thuộc vào con người; không có gì tự động làm nó nhanh hơn.
- **Encode Knowledge as Infrastructure** — Biến các lỗi lặp đi lặp lại thành lint rule hoặc CI check để chặn vĩnh viễn, thay vì sửa tay từng lần.
- **Living Wiki** — Một "ngôn ngữ chung" bằng markdown giữa người và agent — agent đọc trước khi đặt tên và tự ghi lại khi có khái niệm mới hình thành.
- **Stay the Author** — Mục tiêu không phải là một agent siêu mạnh, mà là một kỹ sư siêu mạnh — con người vẫn là người chịu trách nhiệm cuối cùng.

## 2. AI Trends – Infrastructure Engineer

- **AIOps** — AI tự động phát hiện bất thường và dự đoán sự cố hạ tầng trước khi ảnh hưởng đến người dùng.
- **AI-driven IaC** — AI sinh code hạ tầng (Terraform/CDK) từ ngôn ngữ tự nhiên, tự review và phát hiện configuration drift.
- **FinOps** — AI liên tục phân tích usage để tối ưu chi phí theo thời gian thực (right-sizing, gợi ý Savings Plan).
- **Agentic Infrastructure** — Nấc thang trưởng thành mà hạ tầng đang đi qua: Assisted → Automated → Agentic → Self-managing.
- **Explainability** — Hiểu được _vì sao_ AI ra quyết định — điều kiện cần để tin tưởng và chịu trách nhiệm về nó.

## 3. AI Trends to Business – Sales

- **AI / GenAI / Agent** — Ba tầng công nghệ AI mà Sales đang áp dụng, từ trợ lý đơn giản đến agent tự hành động.
- **Time Allocation Gap** — Sales chỉ dành 5–10% thời gian để gặp khách hàng thực sự; phần còn lại là công việc chuẩn bị lặp đi lặp lại.
- **AI-Augmented Sales Journey** — AI hỗ trợ ở mọi giai đoạn: prospecting → research → discovery → proposal → follow-up.
- **Time-to-Value Compression** — AI rút ngắn đáng kể các công việc như research và viết proposal (ví dụ: research từ 2 giờ xuống còn 20 phút).
- **Human Trust** — Trust, quan hệ, giao tiếp và đàm phán là những điều AI không thể thay thế khi chốt deal.

## 4. OpenClaw – Open-Source AI Agents

- **Agent Runtime** — OpenClaw không phải là một app, mà là một "hệ điều hành" để chạy và nuôi dưỡng các AI agent.
- **Layered Architecture (L1–L4)** — Từ tầng gọi model (L1), đến agent execution loop (L2), coding-agent layer (L3), và gateway với memory/cron/session (L4).
- **Agent Loop (Steering + Follow-ups)** — Cơ chế giúp agent tự suy luận, gọi tool, và tự quyết định hành động tiếp theo.
- **Skill Injection Risk** — Rủi ro an ninh khi skill/plugin của agent có thể chứa mã độc hoặc bị khai thác qua prompt injection.
- **Memory as Prompt Augmentation** — Thứ hiện tại được gọi là "memory" của agent thực chất chỉ là văn bản được chèn thêm vào prompt, chưa phải là một cấu trúc memory thực sự.

---

# Cảm nhận sau sự kiện

Tham gia AWS Vietnam Community Meetup là một trải nghiệm quý giá trong hành trình học hỏi về AI và cloud của tôi. Sự kiện đã tập hợp nhiều bài chia sẻ kỹ thuật, bao trùm cả khía cạnh hạ tầng (infrastructure) và ứng dụng AI trong kinh doanh.

Tôi đặc biệt thích bài trình bày "OpenClaw" vì nó cho thấy một dự án mã nguồn mở có thể phát triển từ một bot chuyển tiếp WhatsApp đơn giản thành một agent runtime hoàn chỉnh, vượt qua React về số lượng GitHub stars chỉ trong vài tháng. Bài nói "Ship Fast with AI, Not by AI" cũng truyền cảm hứng cho tôi khi cho thấy rằng viết code nhanh hơn với AI là chưa đủ — tốc độ thực sự đến từ việc đầu tư vào review, testing và chia sẻ kiến thức trong cả team.

Bên cạnh các bài kỹ thuật, phần trình bày về xu hướng AI từ góc nhìn của một Infrastructure Engineer giúp tôi hiểu rõ hơn cách AIOps, FinOps và AI-driven IaC đang định hình lại công việc vận hành cloud hàng ngày. Tôi cũng thấy bài nói về ứng dụng AI trong Sales rất thú vị, khi nó cho thấy AI có thể giải phóng các chuyên gia khỏi công việc chuẩn bị lặp đi lặp lại để tập trung xây dựng quan hệ khách hàng thực sự.

Nhìn chung, sự kiện đã cho tôi cơ hội kết nối với các chuyên gia cloud và AI, trao đổi ý tưởng với những người tham dự khác, và hiểu rõ hơn về hướng đi của ngành — từ AI-assisted coding đến hạ tầng agentic hoàn toàn.

---

# Bài học rút ra

- Sự kiện này cho tôi thấy rằng tác động lớn nhất của AI không chỉ là làm cho từng công việc riêng lẻ nhanh hơn — mà là việc phải suy nghĩ lại cách tổ chức công việc xung quanh AI. Từ bài "Ship Fast with AI, Not by AI", tôi học được rằng viết code nhanh hơn sẽ vô nghĩa nếu review, testing và deployment không được mở rộng tương ứng; năng suất thực sự đến từ việc đầu tư vào hạ tầng chung như lint rule và tài liệu sống (living documentation), không chỉ dựa vào kết quả AI tạo ra.

- Bài trình bày về hạ tầng cho tôi thấy việc áp dụng AI trong cloud engineering không chỉ là tự động hóa — đó là sự thay đổi về vai trò, từ việc vận hành hệ thống bằng tay sang thiết kế và giám sát những hệ thống ngày càng tự quản lý. Điều này cũng đi kèm trách nhiệm mới, đặc biệt về explainability và security, vì việc tin tưởng một quyết định của AI đòi hỏi phải hiểu được vì sao nó được đưa ra.

- Từ phần trình bày về OpenClaw, tôi học được rằng khi agent có nhiều quyền tự chủ và khả năng truy cập tool hơn, năng lực của chúng đang phát triển nhanh hơn các "hàng rào" an toàn xung quanh — một lời nhắc rằng "memory" và "permissions" trong AI agent vẫn cần được thiết kế chặt chẽ hơn rất nhiều so với hiện tại.

- Cuối cùng, bài nói về Sales củng cố thêm một sự cân bằng quan trọng: AI rất giỏi trong việc loại bỏ công việc lặp đi lặp lại, nhưng trust, quan hệ và đàm phán vẫn hoàn toàn phụ thuộc vào con người. Nhìn chung, sự kiện này giúp tôi nhìn nhận AI không phải là thứ thay thế kỹ năng hay khả năng đánh giá của con người, mà là một công cụ chỉ tạo ra giá trị thực sự khi đi kèm với sự giám sát của con người và những hệ thống được thiết kế tốt xung quanh nó.

---

# Hình ảnh sự kiện

## Check-in

![Check-in at AWS VIETNAM COMMUNITY MEETUP](/images/event/event1.jpg)
