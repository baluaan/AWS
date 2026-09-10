---
title: "Blog 2"
date: 2026-08-15
weight: 2
chapter: false
pre: "<b>3.2. </b>"
---

---

# Deploying AWS Verified Access in a VPN-Less TIC 3.0 Security Architecture

## Context & Problem

For government agencies and large-scale enterprises, compliance with the **TIC 3.0 (Trusted Internet Connections 3.0)** standards—a network security framework issued by the Cybersecurity and Infrastructure Security Agency (CISA)—is a vital requirement to safeguard sensitive data.

However, traditional remote access architectures rely heavily on **centralized corporate VPNs**, introducing significant operational and security challenges:

- **Traffic Hairpinning:** All remote employee traffic is forced through a centralized Data Center or primary hub VPC for security inspection before reaching cloud applications, causing severe bandwidth degradation and inefficient routing.
- **Suboptimal User Experience:** Network latency increases significantly due to extended packet journey paths, negatively impacting user productivity.
- **Risk of Lateral Movement:** Traditional VPN authentication verifies users primarily at the network perimeter. If an attacker compromises VPN credentials, they can freely traverse and attempt lateral movement to access other internal assets.

**How can organizations satisfy TIC 3.0 security capabilities without maintaining a complex, bottlenecked VPN infrastructure?**

---

## Solution Architecture: Zero-Trust Security Driven by Identity & Device Posture

![AWS Verified Access in TIC 3.0 Architecture](/images/blog/blog2.png)

The solution leverages **AWS Verified Access (AVA)** as the core foundation, integrated with AWS network security services to implement a **Zero-Trust Network Access (ZTNA)** model aligned with TIC 3.0 guidance.

### Key Components

- **Per-Request Context Evaluation:**
  AVA evaluates the context of every single request based on identity attributes from Identity Providers (IdP)—such as AWS IAM Identity Center or Okta—and Device Posture metrics provided by solutions like CrowdStrike or Jamf.

- **VPN Client Elimination:**
  Users directly access internal applications via secure HTTPS connections without requiring the installation, configuration, or maintenance of corporate VPN clients.

- **Egress Boundary Enforcement:**
  Integrates **AWS Network Firewall** and **Amazon Route 53 Resolver DNS Firewall** to inspect, filter, and prevent unauthorized or malicious outbound connections to the Internet, acting as TIC 3.0 **Policy Enforcement Points (PEP)**.

---

## 1. Configuring AWS Verified Access Policies with Cedar

AWS Verified Access utilizes the **Cedar** policy language to enforce granular, fine-grained access control.

The policy below demonstrates how to restrict application access exclusively to users within the `Finance` department, using an organization-owned email domain, and operating a compliant, secure device.

```cedar
permit(principal, action, resource)
when {
    // 1. Verify user identity attributes from the Identity Provider (IdP)
    context.identity.groups.contains("Finance") &&
    context.identity.email.endsWith("@organization.gov") &&

    // 2. Verify device security posture
    context.device.is_compliant == true &&
    context.device.file_vault_enabled == true
};
```

### Policy Breakdown

The policy enforces two primary evaluation checks:

1. **Identity Context:** Confirms the requesting user belongs to the `Finance` group and holds a valid `@organization.gov` email address.
2. **Device Posture:** Validates that the endpoint complies with organizational security baselines, specifically verifying:
   - Overall device compliance status is `true`.
   - Full-disk encryption (e.g., FileVault) is actively enabled.

Only when **all conditions evaluate to true** does AVA grant access to the application resource.

---

## 2. Automated Incident Response Logic via AWS Lambda

When AWS Verified Access detects denied requests or policy violations, access logs can be ingested and analyzed in real-time to mitigate potential threats automatically.

The following **AWS Lambda (Python 3.12)** function demonstrates parsing security logs to automatically append suspicious source IP addresses to an **AWS WAF IP Set**.

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

### Response Workflow

The automated incident response workflow is structured as follows:

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
        │ Analyze Source IP
        ▼
AWS WAF IP Set
        │
        ▼
Block Suspicious IP
```

---

## 3. Event-Driven Automation via Amazon EventBridge

Configure an **Amazon EventBridge Rule** to isolate denied access events (`DENY`) generated by AWS Verified Access and automatically invoke the response Lambda function.

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

### Detailed Operational Execution

1. AWS Verified Access evaluates incoming application requests against configured Cedar policies.
2. If request parameters fail policy evaluation, AVA denies access and returns an HTTP 403.
3. Access log entry is generated and forwarded to CloudWatch Logs.
4. Amazon EventBridge evaluates the event stream against the defined `DENY` event pattern.
5. EventBridge triggers the `AVAAutomatedBlockFunction` Lambda execution.
6. The Lambda function extracts the originating IP address from the security context.
7. If the IP address is deemed malicious/unauthorized, Lambda appends it to the AWS WAF IP Set.
8. AWS WAF immediately drops subsequent incoming requests originating from the blocked IP address at the edge/alb level.

---

## 4. Impact Analysis & Comparison

Transitioning from a legacy centralized VPN topology to an AWS Verified Access Zero-Trust architecture delivers measurable improvements across security, performance, and operational overhead.

| Performance Metric / Dimension | Traditional Centralized VPN Architecture              | Zero-Trust Architecture with AWS Verified Access           |
| ------------------------------ | ----------------------------------------------------- | ---------------------------------------------------------- |
| **Network Latency**            | High due to traffic hairpinning through central DCs   | Significantly lower via direct application connectivity    |
| **Client Requirement**         | Mandatory installation & upkeep of VPN clients        | Zero client required (Seamless HTTPS access)               |
| **Security Mechanism**         | Static perimeter-level authentication                 | Per-request continuous evaluation using context & identity |
| **Lateral Movement Risk**      | High (Broad subnet-level network visibility)          | Severely minimized (App-level granular micro-segmentation) |
| **Access Control Granularity** | Coarse network/subnet level routing                   | Fine-grained application level policy execution            |
| **User Experience**            | Dependent on VPN gateway capacity & connection status | Native web/HTTPS experience via standard browsers          |
| **Operational Complexity**     | High (Infrastructure, patching, license maintenance)  | Low (Fully managed cloud-native ZTNA service)              |

> **Note:** Performance benefits such as latency reductions and security posture improvements depend on actual implementation topology, user geographic distribution, application design, and organizational security policies. These metrics serve as a architectural comparative baseline.

---

## 5. Key Design Principles & Best Practices

### 5.1. Contextual Trust Control

Combine multiple trust vectors into a single authorization decision:

- User identity context from enterprise Identity Providers (IdP).
- Fine-grained role and group memberships.
- Endpoint health metrics (Device Posture).
- Real-time HTTP request attributes.

This multi-layered contextual evaluation prevents unauthorized access even in scenarios involving valid user credentials on unmanaged or compromised devices.

### 5.2. Optimizing Egress Policy Enforcement Points (PEP)

Deploy **AWS Network Firewall** in conjunction with **Amazon Route 53 Resolver DNS Firewall** within designated inspection VPCs to:

- Inspect and control all outbound (egress) internet traffic.
- Block DNS queries to malicious or unapproved external domains.
- Mitigate Command-and-Control (C&C) beaconing risks.
- Enforce strict TIC 3.0 perimeter compliance for outbound data flows.

### 5.3. Application & Network Decoupling

AWS Verified Access operates at the **Application Layer (Layer 7)**, enabling Zero-Trust deployment without granting users broad network-level layer-3 access to internal subnets.

This decoupling decouples security management from:

- Network Route Tables.
- CIDR range definitions.
- Direct VPN Gateways.
- Subnet-level network visibility.

### 5.4. Centralized Audit & Compliance Logging

All access evaluation logs should be centralized and persisted long-term to facilitate:

- Forensic incident investigations.
- Automated Threat Intelligence analysis.
- Real-time security operations monitoring.
- Periodic regulatory compliance auditing.
- Extended log retention compliance.

Storage solutions such as **Amazon S3** or **Amazon OpenSearch Service** can be selected depending on required analytics capabilities and retention mandates.

---

## 6. Conclusion

The combination of **AWS Verified Access and Zero-Trust principles** provides a modern, resilient alternative to traditional VPN-based remote access architectures.

Rather than granting blanket network access through a centralized VPN tunnel, user requests are continuously authenticated and evaluated against **identity, device posture, and request context** prior to granting application access.

When integrated with **AWS Network Firewall, Route 53 Resolver DNS Firewall, Amazon EventBridge, AWS Lambda, and AWS WAF**, organizations can establish a robust, automated multi-layered defense architecture that enforces TIC 3.0 guidelines effectively.

---

## References

- [AWS Verified Access in a TIC 3.0 Architecture](https://aws.amazon.com/blogs/publicsector/aws-verified-access-in-a-tic-3-0-architecture/)
- [AWS Verified Access User Guide](https://docs.aws.amazon.com/verified-access/latest/ug/what-is-verified-access.html)
