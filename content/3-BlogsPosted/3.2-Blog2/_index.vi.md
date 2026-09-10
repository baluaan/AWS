---
title: "Blog 2"
date: 2026-08-15
weight: 2
chapter: false
pre: "<b>3.2. </b>"
---

---

# Triển khai AWS Verified Access trong kiến trúc bảo mật TIC 3.0 không cần VPN

## Bối cảnh & Vấn đề

Đối với các cơ quan quản lý nhà nước và doanh nghiệp quy mô lớn, việc tuân thủ tiêu chuẩn **TIC 3.0 (Trusted Internet Connections 3.0)** — khung hướng dẫn bảo mật mạng do Cơ quan An ninh Cơ sở hạ tầng và An ninh mạng Hoa Kỳ (CISA) ban hành — là yêu cầu quan trọng để bảo vệ dữ liệu nhạy cảm.

Tuy nhiên, các kiến trúc truy cập từ xa truyền thống thường phụ thuộc hoàn toàn vào kết nối **VPN tập trung (Corporate VPN)**, dẫn đến nhiều bất cập:

- **Tắc nghẽn lưu lượng (Traffic Hairpinning):** Mọi truy cập của nhân viên làm việc từ xa bị buộc phải dẫn đường truyền qua trung tâm dữ liệu (Data Center) hoặc VPC trung tâm để kiểm tra an ninh trước khi kết nối tới ứng dụng Cloud, gây lãng phí băng thông.
- **Trải nghiệm người dùng kém:** Độ trễ mạng (latency) tăng cao do hành trình gói tin kéo dài, gây ảnh hưởng đến hiệu suất làm việc.
- **Rủi ro di chuyển ngang (Lateral Movement):** Cơ chế xác thực VPN truyền thống thường kiểm tra người dùng tại chu vi mạng. Nếu Hacker chiếm được thông tin đăng nhập VPN, họ có thể tìm cách truy cập các tài nguyên nội bộ khác.

**Vậy làm thế nào để đáp ứng các tiêu chí kiểm soát của TIC 3.0 mà không cần duy trì một hệ thống VPN phức tạp?**

---

## Kiến trúc giải pháp: Bảo mật Zero-Trust dựa trên Nhận dạng & Trạng thái thiết bị

![Kiến trúc AWS Verified Access trong TIC 3.0](/images/blog/blog2.png)

Giải pháp sử dụng **AWS Verified Access (AVA)** làm trung tâm, kết hợp với các dịch vụ bảo mật mạng của AWS để triển khai mô hình **Zero-Trust Network Access (ZTNA)** theo định hướng TIC 3.0.

### Các thành phần chính

- **Xác thực theo từng Request (Per-Request Context):**
  AVA đánh giá ngữ cảnh của từng request dựa trên thông tin từ nhà cung cấp danh tính (IdP) như AWS IAM Identity Center, Okta và trạng thái bảo mật của thiết bị (Device Posture) từ các giải pháp như CrowdStrike, Jamf.

- **Loại bỏ VPN Client:**
  Người dùng có thể truy cập trực tiếp ứng dụng nội bộ thông qua kết nối HTTPS an toàn mà không cần cài đặt hoặc duy trì VPN Client.

- **Kiểm soát lưu lượng đi ra (Egress Boundary):**
  Tích hợp **AWS Network Firewall** và **Amazon Route 53 Resolver DNS Firewall** để kiểm tra, kiểm soát và ngăn chặn các kết nối độc hại đi ra Internet theo các **Policy Enforcement Points (PEP)** của TIC 3.0.

---

## 1. Cấu hình Policy AWS Verified Access bằng Cedar

AWS Verified Access sử dụng ngôn ngữ policy **Cedar** để thực thi việc phân quyền truy cập chi tiết.

Policy dưới đây minh họa việc chỉ cho phép những người dùng thuộc nhóm `Finance`, sử dụng email thuộc domain tổ chức và có thiết bị đáp ứng các yêu cầu bảo mật truy cập ứng dụng.

```cedar
permit(principal, action, resource)
when {
    // 1. Kiểm tra thông tin người dùng từ Identity Provider (IdP)
    context.identity.groups.contains("Finance") &&
    context.identity.email.endsWith("@organization.gov") &&

    // 2. Kiểm tra trạng thái an toàn của thiết bị (Device Posture)
    context.device.is_compliant == true &&
    context.device.file_vault_enabled == true
};
```

### Giải thích Policy

Policy thực hiện hai nhóm kiểm tra chính:

1. **Identity:** Kiểm tra người dùng thuộc nhóm `Finance` và sử dụng email thuộc domain `@organization.gov`.
2. **Device Posture:** Kiểm tra thiết bị có đáp ứng yêu cầu bảo mật hay không, ví dụ:
   - Thiết bị đạt trạng thái compliant.
   - Tính năng mã hóa ổ đĩa được bật.

Chỉ khi tất cả điều kiện đều thỏa mãn, request mới được phép truy cập tài nguyên.

---

## 2. Logic tự động hóa ứng phó sự cố với AWS Lambda

Khi AWS Verified Access ghi nhận các request bị từ chối hoặc vi phạm policy, log có thể được thu thập và xử lý để phát hiện các hành vi đáng ngờ.

Function **AWS Lambda (Python 3.12)** dưới đây minh họa việc phân tích log và tự động thêm địa chỉ IP đáng ngờ vào **AWS WAF IP Set**.

```python
import json
import logging
import os
import boto3

logger = logging.getLogger()
logger.setLevel(logging.INFO)

wafv2_client = boto3.client("wafv2")

IP_SET_ID = os.environ.get("WAF_IP_SET_ID")
IP_SET_NAME = os.environ.get("WAF_IP_SET_NAME")
SCOPE = "REGIONAL"


def lambda_handler(event, context):
    logger.info("Processing Verified Access security logs...")

    # Parse CloudWatch Log Event Payload
    for record in event.get("Records", []):
        payload = json.loads(record["body"])
        detail = payload.get("detail", {})

        # Identify unauthorized/denied access attempt
        status = detail.get("http_response_code")
        user_ip = detail.get("remote_ip")
        decision = detail.get("policy_evaluation_decision")

        if status == 403 and decision == "DENY":
            logger.warning(
                f"UNAUTHORIZED ACCESS ATTEMPT DETECTED from IP: {user_ip}"
            )
            block_suspicious_ip(user_ip)

    return {
        "statusCode": 200,
        "body": "Security log evaluation completed."
    }


def block_suspicious_ip(ip_address):
    try:
        # Retrieve current WAF IP Set details
        response = wafv2_client.get_ip_set(
            Name=IP_SET_NAME,
            Scope=SCOPE,
            Id=IP_SET_ID
        )

        ip_set = response["IPSet"]
        lock_token = response["LockToken"]

        addresses = ip_set["Addresses"]
        cidr_ip = f"{ip_address}/32"

        if cidr_ip not in addresses:
            addresses.append(cidr_ip)

            # Update WAF IP Set to automatically block
            # the offending IP
            wafv2_client.update_ip_set(
                Name=IP_SET_NAME,
                Scope=SCOPE,
                Id=IP_SET_ID,
                Addresses=addresses,
                LockToken=lock_token
            )

            logger.info(
                f"Successfully added {cidr_ip} to WAF Blocklist."
            )

    except Exception as e:
        logger.error(
            f"Error updating WAF IP Set: {str(e)}"
        )
```

### Luồng xử lý

Quy trình tự động hóa có thể được mô tả như sau:

```text
AWS Verified Access
        │
        │ Access Log
        ▼
Amazon CloudWatch Logs
        │
        │ Event
        ▼
Amazon EventBridge
        │
        │ DENY Event
        ▼
AWS Lambda
        │
        │ Analyze IP
        ▼
AWS WAF IP Set
        │
        ▼
Block Suspicious IP
```

---

## 3. Lập lịch & Kích hoạt qua Amazon EventBridge

Cấu hình **Amazon EventBridge Rule** để lọc các sự kiện truy cập bị từ chối (`DENY`) từ AWS Verified Access và kích hoạt Lambda Function xử lý tự động.

### Event Pattern Configuration

**Event Source:**

```text
aws.verified-access
```

**Detail Type:**

```text
Verified Access Access Log
```

**Event Pattern:**

```json
{
  "source": ["aws.verified-access"],
  "detail-type": ["Verified Access Access Log"],
  "detail": {
    "policy_evaluation_decision": ["DENY"]
  }
}
```

**Target:**

```text
Lambda Function: AVAAutomatedBlockFunction
```

### Luồng hoạt động

1. AWS Verified Access đánh giá request.
2. Nếu request không đáp ứng policy, hệ thống trả về quyết định `DENY`.
3. Sự kiện được ghi nhận trong hệ thống log.
4. Amazon EventBridge lọc các sự kiện `DENY`.
5. EventBridge kích hoạt Lambda Function.
6. Lambda phân tích địa chỉ IP nguồn.
7. Nếu phát hiện IP đáng ngờ, Lambda thêm IP vào AWS WAF IP Set.
8. AWS WAF sử dụng IP Set để chặn các request tiếp theo.

---

## 4. Kết quả & Phân tích tác động

Bằng cách chuyển đổi từ giải pháp VPN tập trung sang kiến trúc Zero-Trust sử dụng AWS Verified Access, tổ chức có thể cải thiện đáng kể khả năng kiểm soát truy cập và giảm sự phụ thuộc vào VPN truyền thống.

| Chỉ số                         | Kiến trúc VPN tập trung truyền thống     | Kiến trúc Zero-Trust với AWS Verified Access        |
| ------------------------------ | ---------------------------------------- | --------------------------------------------------- |
| **Độ trễ mạng**                | Cao do kết nối vòng về Data Center       | Có thể thấp hơn nhờ truy cập trực tiếp đến ứng dụng |
| **Yêu cầu kết nối người dùng** | Bắt buộc cài đặt và duy trì VPN Client   | Không cần VPN Client                                |
| **Cơ chế kiểm tra an ninh**    | Xác thực chủ yếu tại chu vi mạng         | Đánh giá policy dựa trên ngữ cảnh truy cập          |
| **Rủi ro di chuyển ngang**     | Cao nếu VPN cấp quyền truy cập mạng rộng | Giảm đáng kể nhờ kiểm soát theo ứng dụng            |
| **Khả năng quản lý truy cập**  | Quản lý ở cấp mạng/subnet                | Quản lý chi tiết ở cấp ứng dụng                     |
| **Trải nghiệm người dùng**     | Phụ thuộc vào VPN Gateway                | Truy cập ứng dụng thông qua HTTPS                   |
| **Vận hành**                   | Cần duy trì VPN infrastructure           | Giảm sự phụ thuộc vào VPN infrastructure            |

> **Lưu ý:** Các chỉ số như latency và mức độ giảm rủi ro phụ thuộc vào kiến trúc triển khai thực tế, vị trí người dùng, ứng dụng và chính sách bảo mật. Không nên coi các giá trị này là kết quả đo tuyệt đối.

---

## 5. Điểm quan trọng & Best Practice

### 5.1. Kiểm soát đa ngữ cảnh (Contextual Trust)

Kết hợp đồng thời:

- Thông tin người dùng từ Identity Provider (IdP).
- Thông tin về nhóm/quyền của người dùng.
- Trạng thái bảo mật của thiết bị (Device Posture).
- Các thuộc tính bổ sung của request.

Cách tiếp cận này giúp hạn chế việc cấp quyền cho người dùng hoặc thiết bị không đáp ứng yêu cầu bảo mật.

### 5.2. Tối ưu hóa điểm kiểm soát lưu lượng ra Internet (Egress PEP)

Triển khai **AWS Network Firewall** kết hợp với **Amazon Route 53 Resolver DNS Firewall** tại VPC phù hợp để:

- Kiểm soát lưu lượng outbound.
- Chặn các domain độc hại.
- Giảm nguy cơ kết nối tới Command-and-Control (C&C) Server.
- Tăng khả năng kiểm soát egress traffic.

### 5.3. Độc lập tầng mạng (Network Decoupling)

AWS Verified Access hoạt động ở **tầng ứng dụng (Application Layer)**, cho phép triển khai mô hình Zero-Trust mà không nhất thiết phải mở rộng quyền truy cập mạng nội bộ cho người dùng từ xa.

Điều này giúp giảm sự phụ thuộc vào:

- Route Tables.
- CIDR.
- VPN Gateway.
- Network-level access.

### 5.4. Lưu trữ Log phục vụ kiểm toán

Toàn bộ log liên quan đến truy cập nên được thu thập và lưu trữ tập trung để phục vụ:

- Điều tra sự cố.
- Phân tích bảo mật.
- Giám sát truy cập.
- Kiểm toán định kỳ.
- Đáp ứng yêu cầu lưu trữ log của tổ chức.

Có thể sử dụng **Amazon S3** hoặc **Amazon OpenSearch Service** tùy theo yêu cầu phân tích và thời gian lưu trữ.

---

## 6. Tổng kết

Kiến trúc **AWS Verified Access + Zero Trust** cung cấp một hướng tiếp cận hiện đại cho bài toán truy cập ứng dụng nội bộ từ xa.

Thay vì cấp quyền truy cập vào toàn bộ mạng thông qua VPN, người dùng được xác thực và đánh giá dựa trên **identity, device posture và context của request** trước khi được phép truy cập ứng dụng cụ thể.

Khi kết hợp với **AWS Network Firewall, Route 53 Resolver DNS Firewall, Amazon EventBridge, AWS Lambda và AWS WAF**, kiến trúc có thể tạo thành một hệ thống bảo mật nhiều lớp, hỗ trợ kiểm soát truy cập, giám sát và tự động hóa ứng phó sự cố.

---

## Tài liệu tham khảo

- [AWS Verified Access in a TIC 3.0 Architecture](https://aws.amazon.com/blogs/publicsector/aws-verified-access-in-a-tic-3-0-architecture/)
- [AWS Verified Access User Guide](https://docs.aws.amazon.com/verified-access/latest/ug/what-is-verified-access.html)
