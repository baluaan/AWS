---
title: "Proposal"
date: 2026-08-24
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Automated Threat Protection

## Automated Threat Protection with AWS WAF & Lambda

---

# 1. Project Summary

**Automated Threat Protection** is a Cloud-Native Security Solution designed to automate the detection, response, and mitigation of malicious web attacks (such as DDoS, Brute Force, Web Scraping, and HTTP Flood) targeted at static websites hosted on Amazon S3 and distributed via Amazon CloudFront.

The solution leverages fully managed AWS services including **Amazon S3**, **Amazon CloudFront**, **AWS WAF**, **Amazon CloudWatch**, **Amazon SNS**, and **AWS Lambda**. By continuously analyzing real-time WAF logs and metrics, the system proactively identifies suspicious IP addresses, triggers an automated pipeline to parse logs, extracts offending IPs, updates them into a blocking list (**WAF IP Set V6**), and sends immediate alert notifications to system administrators.

This solution ensures exceptional scalability, near real-time attack mitigation, optimized operational costs, and minimizes manual intervention during security incident response.

---

# 2. Problem Statement

## Existing Challenges

Modern web applications are primary targets for automated malicious traffic. Traditional defense and incident response mechanisms face critical limitations:

- **Delayed Manual Response:** During Brute Force or HTTP Flood attacks, operations engineers must manually inspect logs, identify malicious IPs, and add them to firewall rules. This process is time-consuming and leads to extended service downtime.
- **Lack of Real-Time Automation:** Static firewall configurations cannot dynamically adjust rules based on fluctuating traffic volume.
- **Backend Resource Exhaustion:** Malicious traffic that is not blocked at the edge reaches backend servers, causing heavy resource strain on application servers and databases.
- **Fragmented Incident Visibility:** Security teams lack real-time visibility into the scale, frequency, and origins of incoming attacks.

## Proposed Solution

The proposed solution implements an end-to-end Automated Threat Protection workflow on AWS following industry architecture standards:

- **Amazon S3 (Origin):** Securely stores static website assets with Block Public Access enabled.
- **Amazon CloudFront:** Delivers content globally over CDN and protects the application at the edge via AWS WAF integration.
- **AWS WAF:** Filters HTTP/HTTPS traffic using rate-limiting rules (`Rate-based Rule`) and IP blocking rules (`IP Set Rule`), while streaming access logs to a CloudWatch Log Group (`aws-waf-logs-cloudfront`).
- **Amazon CloudWatch Alarm:** Monitors WAF `BlockedRequests` metrics. When thresholds are breached (In Alarm state), CloudWatch sends alarm signals to an **Amazon SNS Topic**.
- **Amazon SNS:** Acts as the central notification and fan-out service, sending **Email Notifications** directly to administrators while serving as a **Trigger** for **AWS Lambda**.
- **AWS Lambda:** Parses incoming logs from CloudWatch Logs, extracts offending IP addresses (`clientIp`), formats CIDR subnets, and updates the WAF IP Set to block offenders permanently.

## Key Benefits

- **Near Real-Time Attack Blocking:** Reduces incident response time from hours to seconds.
- **Edge Layer Protection:** Blocks malicious traffic at CloudFront edge locations before it hits the origin.
- **Cost & Resource Optimization:** Reduces system load and infrastructure expenditure during attack surges.
- **100% Serverless Operations:** Eliminates the need to provision or manage servers for security monitoring.
- **High Scalability:** Easily adaptable to new attack scenarios and scalable with traffic growth.

---

# 3. Solution Architecture

The system strictly follows Cloud-Native Serverless Security architecture on AWS.

## Architecture Diagram

System workflow sequence:

**S3 (Origin) → CloudFront (Distribution) → AWS WAF (Rate-based + IP Set V6) → CloudWatch Logs / Alarm → Amazon SNS → AWS Lambda → WAF IP Set V6 & Email Notification**

![System Architecture](/images/proposal/system_architecture1.png)

## AWS Services Used

- Amazon S3 (Origin storage)
- Amazon CloudFront (CDN Edge protection)
- AWS WAF (Web Application Firewall)
- Amazon CloudWatch (Log Group `aws-waf-logs-cloudfront` & Alarms)
- Amazon SNS (Simple Notification Service)
- AWS Lambda (Python runtime)
- AWS IAM (Identity and Access Management)

## Component Design

### Edge & Traffic Filtering Layer

- **Amazon S3:** Bucket configured with Block All Public Access, acting as the web origin.
- **Amazon CloudFront:** Connects to S3 via Origin Access Control (OAC), forcing all traffic through CloudFront and HTTPS.
- **AWS WAF:** Attached directly to the CloudFront Distribution, including:
  - _Rate-based Rule (`BlockSpamRateLimit`):_ Automatically detects and mitigates request bursts.
  - _IP Set Rule (`BlockAutoIPSetRuleV6`):_ Applies the blacklisted IP set (`AutoBlockedIPSetV6`) for persistent blocking.

### Detection & Monitoring Layer

- **CloudWatch Logs:** Collects WAF access logs into the Log Group `aws-waf-logs-cloudfront`.
- **CloudWatch Alarms:** Tracks the `BlockedRequests` metric from the Web ACL. When threshold conditions are met (e.g., Sum >= 1 in 1 minute), the alarm changes state and notifies SNS.

### Automation & Alerting Layer

- **Amazon SNS Topic (`WAFAlertTopic`):** Receives alarm states from CloudWatch Alarms.
  - Sends immediate email alerts to system admins via Email Subscription.
  - Triggers execution of the **AWS Lambda Function**.
- **AWS Lambda (`WAFAutoBlockFunction`):** Python-based serverless function executing logic to:
  1. Query the CloudWatch Log Group `aws-waf-logs-cloudfront` for recent log events.
  2. Parse the JSON log payload to extract offending IP addresses (`clientIp`).
  3. Classify IP subnet formatting (IPv6 `/128` or IPv4 `/32`).
  4. Fetch the current WAF IP Set state and push newly detected IPs via the AWS WAF API (`GetIPSet`, `UpdateIPSet`).
  5. Publish confirmation messages to SNS.

---

# 4. Technical Implementation

## Implementation Phases

The deployment comprises 7 technical steps:

1. **Host Static Website on Amazon S3:** Create an S3 Bucket in region `us-east-1`, enable Block Public Access, and upload website assets.
2. **Distribute via Amazon CloudFront:** Create a distribution attached to S3 via OAC, configure HTTP-to-HTTPS redirect, and set the S3 Bucket Policy.
3. **Configure AWS WAF & Logging:** Create an IPv6 IP Set (`AutoBlockedIPSetV6`), set up a Web ACL associated with CloudFront, configure Rate-based & IP Set rules, and enable CloudWatch Logging to `aws-waf-logs-cloudfront`.
4. **Create Amazon SNS Topic:** Create SNS Topic `WAFAlertTopic` and configure a Gmail subscription.
5. **Set up IAM Role & AWS Lambda Function:** Create IAM Role `LambdaWAFAutoBlockRole` with `logs`, `wafv2`, and `sns` permissions; deploy Lambda `WAFAutoBlockFunction` (Python 3.12), set 4 environment variables (`IP_SET_NAME`, `IP_SET_ID`, `LOG_GROUP_NAME`, `SNS_TOPIC_ARN`), and attach an SNS Trigger.
6. **Configure CloudWatch Alarm:** Create a Metric Alarm monitoring `BlockedRequests` with a notification action directed to `WAFAlertTopic` on In Alarm state.
7. **Testing & Resource Cleanup:** Run PowerShell scripts simulating attack traffic to verify automated IP blocking, followed by proper resource teardown.

## Technical Requirements

### Programming Languages & SDKs

- Python 3.12
- AWS SDK for Python (`boto3`)

### Cloud Infrastructure & Tools

- AWS Management Console (Region: `us-east-1` / Global)
- AWS WAFv2 API (`GetIPSet`, `UpdateIPSet`)
- CloudWatch Logs Filter API (`filter_log_events`)

### Testing Tools

- PowerShell / Bash CLI (`Invoke-WebRequest` / `curl`)

---

# 5. Deployment Roadmap

Project implementation roadmap following 7 standardized technical steps:

| Step       | Technical Task Description                                                                                                   | Associated Components    |
| :--------- | :--------------------------------------------------------------------------------------------------------------------------- | :----------------------- |
| **Step 1** | Create S3 Bucket, block public access, and upload static site files.                                                         | Amazon S3                |
| **Step 2** | Deploy CloudFront distribution connected via OAC, update S3 Bucket Policy.                                                   | CloudFront, S3           |
| **Step 3** | Initialize WAF IP Set V6, create Web ACL with Rate-based + IP Set rules, enable Logging to `aws-waf-logs-cloudfront`.        | AWS WAF, CloudWatch Logs |
| **Step 4** | Create SNS Topic `WAFAlertTopic` and confirm Email Subscription.                                                             | Amazon SNS               |
| **Step 5** | Provision IAM Role `LambdaWAFAutoBlockRole`, deploy Python Lambda code, set 4 environment variables, and attach SNS trigger. | AWS Lambda, IAM, SNS     |
| **Step 6** | Configure CloudWatch Alarm tracking `BlockedRequests` linked to SNS Topic.                                                   | CloudWatch Alarm, SNS    |
| **Step 7** | Execute attack simulation script, check logs, verify IP blocking in WAF IP Set, confirm email alert, and perform cleanup.    | PowerShell / AWS Console |

---

# 6. Cost Estimation

## Infrastructure Cost Breakdown

Operating costs are minimal due to the Serverless pay-as-you-go billing model.

| AWS Service              | Cost Description                                   | Estimated Monthly Cost |
| :----------------------- | :------------------------------------------------- | :--------------------- |
| **AWS WAF**              | Web ACL & Rule Groups                              | ~$5.00/month           |
| **Amazon CloudFront**    | Data Transfer Out (Free Tier covered)              | ~$0.50/month           |
| **Amazon CloudWatch**    | Metrics, Logs (`aws-waf-logs-cloudfront`) & Alarms | ~$0.30/month           |
| **AWS Lambda**           | Request Count & Execution Duration                 | ~$0.05/month           |
| **Amazon SNS**           | Notification Email Deliveries                      | ~$0.01/month           |
| **Total Estimated Cost** | **Monthly Operational Expense**                    | **~$5.86 USD/month**   |

### Post-Demo Resource Cleanup Sequence

To prevent recurring charges after testing, resources **must** be deleted in reverse dependency order:

1. **Delete CloudWatch Alarm:** Delete `WAF-BlockedRequests-Alarm`.
2. **Delete Lambda & IAM:** Remove Lambda Function `WAFAutoBlockFunction`, IAM Role `LambdaWAFAutoBlockRole`, and Policy `LambdaWAFAutoBlockPolicy`.
3. **Delete SNS Topic:** Delete Topic `WAFAlertTopic`.
4. **Remove AWS WAF & IP Set:** Disassociate WAF from CloudFront, disable logging, delete `WebsiteProtectionACL`, and remove `AutoBlockedIPSetV6`.
5. **Delete Log Groups:** Delete Log Groups `/aws/lambda/WAFAutoBlockFunction` and `aws-waf-logs-cloudfront`.
6. **Disable & Delete CloudFront Distribution:** Disable the distribution and delete once status updates to Disabled.
7. **Delete S3 Bucket:** Empty bucket contents completely and delete the S3 Bucket.

---

# 7. Risk Assessment

## Risks & Mitigation Strategies

- **Risk 1 — IAM Permission or Environment Variable Errors:** Lambda execution fails due to insufficient API permissions or incorrect parameter configurations.
  - _Mitigation:_ Ensure IAM Policies grant the necessary `logs`, `wafv2`, and `sns` permissions, and verify all 4 environment variables are accurately set in the Lambda Configuration.
- **Risk 2 — Incorrect Teardown Order:** Attempting deletion out of order results in "Resource in use" dependency errors.
  - _Mitigation:_ Strictly adhere to the 7-step reverse cleanup sequence.
- **Risk 3 — WAF Log Streaming Latency:** Minor delays in WAF log delivery to CloudWatch Logs may cause Lambda to miss recently blocked IPs during short time windows.
  - _Mitigation:_ Program Lambda to query a 15-minute log window (`fifteen_min_ago = now - (15 * 60 * 1000)`) to compensate for logging latency.

---

# 8. Expected Outcomes

## Technical Outcomes

Upon completion, the project delivers:

- A fully automated web threat protection solution for S3 + CloudFront static websites.
- An end-to-end automated pipeline: CloudWatch Alarm detects threshold breach → SNS triggers → SNS notifies Admin & invokes Lambda → Lambda reads CloudWatch Logs → Lambda updates WAF IP Set for permanent blocking.
- Instant security alert notifications delivered directly via email.

## Practical Value

- Drastically reduces manual effort required by Security and Cloud Operations teams.
- Increases service availability and resilience against automated web threats.
- Serves as a baseline architecture reference for enterprise Serverless Security implementations on AWS.
