---
title: "Workshop Overview"
date: 2026-08-24
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### Objectives

This workshop guides you through deploying an **Automated Threat Protection** solution for a static website on AWS using a Cloud-Native Serverless architecture, managed security services, and an automated incident response workflow. Upon completing this workshop, you will be able to build a complete web protection system capable of automatically detecting, alerting, and blocking violating IP addresses at the Edge location without requiring manual operator intervention.

---

## 1. Problem Statement & Solution Overview

Today, websites and web applications are primary targets for automated cyberattacks such as HTTP Flood, Brute Force, Web Scraping, and DDoS. When an attack occurs, traditional incident response requires operations engineers to manually inspect logs, extract malicious IP addresses, and add them to a firewall by hand. This process is time-consuming, leading to prolonged service downtime and potential system overload.

Instead of manual processing, this workshop implements an **Automated Threat Protection** solution built on a Serverless Security architecture on AWS. The static website is stored securely on **Amazon S3 (Origin)** and distributed globally via **Amazon CloudFront** CDN.

Traffic passing through CloudFront is filtered and controlled by **AWS WAF**. When anomalous traffic breaches specified thresholds, **Amazon CloudWatch** detects the anomaly, triggers an alarm, and sends a signal to **Amazon SNS**. **Amazon SNS** simultaneously dispatches an instant email notification to system administrators and triggers an **AWS Lambda** function. The Lambda function automatically queries **CloudWatch Logs**, extracts the violating IP address, and directly updates an **AWS WAF IP Set** to permanently block access at the Edge location.

---

## 2. System Architecture

The **Automated Threat Protection** solution is deployed using a Serverless model on AWS (Region `us-east-1`), organized into three main functional layers:

- **Edge Protection Layer:** Includes **Amazon S3** (Origin storage for static website assets), **Amazon CloudFront** (Content Delivery Network - CDN), and **AWS WAF** (Web Application Firewall integrated with WAF IP Set).
- **Monitoring & Detection Layer:** Includes **CloudWatch Logs Stream** (Log Group `aws-waf-logs-cloudfront`) collecting access logs from WAF and **CloudWatch Metric Alarm** monitoring anomalous traffic metrics.
- **Automation & Alerting Layer:** Includes **Amazon SNS Topic** acting as a central orchestrator to send **Email Notifications** to administrators and **Trigger** the **AWS Lambda** function for automated remediation.

**Figure 1 – Automated Threat Protection System Architecture**

![System Architecture](/images/Workshop/Workshop-overview/system_architecture.png)

---

## 3. System Workflow

The end-to-end processing workflow operates through the following steps:

1. Users send HTTP/HTTPS requests to access the website via **Amazon CloudFront Distribution**.

2. CloudFront forwards requests to the **Amazon S3 Bucket (Origin)** to retrieve static content and return it to the user.

3. **AWS WAF** inspects all traffic passing through CloudFront. When request rates from a single IP exceed defined limits, WAF triggers a Rate-based Rule, returns a `403 Forbidden` error, and pushes access logs to **CloudWatch Logs**.

4. **CloudWatch Alarm** continuously monitors the `BlockedRequests` metric. When this metric crosses the threshold within the configured timeframe, the Alarm transitions to the _In Alarm_ state.

5. CloudWatch Alarm immediately sends an alert signal to the **Amazon SNS Topic** (`WAFAlertTopic`).

6. **Amazon SNS** sends an instant incident notification email to the administrator's mailbox (Gmail).

7. Concurrently, **Amazon SNS** acts as a Trigger to execute the **AWS Lambda** function (`WAFAutoBlockFunction`).

8. The Lambda function queries the **CloudWatch Log Group** (`aws-waf-logs-cloudfront`), filtering and extracting the offending IP address (`clientIp`).

9. Lambda formats the IP address to standard CIDR notation (IPv6/128 or IPv4/32) and calls the WAF API (`UpdateIPSet`) to append the offending IP to the **WAF IP Set V6** (`AutoBlockedIPSetV6`).

10. AWS WAF enforces the updated IP Set Rule, automatically blocking all subsequent requests from the offending IP address directly at the CloudFront Edge location.

---

## 4. AWS Services Used

This workshop utilizes the following AWS services:

### Storage & Distribution

- Amazon S3 (Origin Storage)
- Amazon CloudFront (Content Delivery Network - CDN)

### Security & Compliance

- AWS WAF (Web Application Firewall - Rate-based Rule & IP Set Rule)
- AWS IAM (Identity and Access Management - Least Privilege Policy)

### Monitoring & Alerting

- Amazon CloudWatch (Log Group `aws-waf-logs-cloudfront` & Metric Alarms)
- Amazon SNS (Simple Notification Service)

### Serverless Compute & Automation

- AWS Lambda (Python 3.12 Runtime with Boto3 SDK)

### Testing Tools

- PowerShell / Bash CLI (`Invoke-WebRequest` / `curl`)

---

## 5. Expected Outcomes

Upon completing this workshop, you will be able to:

- Successfully host a static website on Amazon S3 and distribute it securely via Amazon CloudFront.
- Configure AWS WAF with a combination of Rate-based Rules and IP Set Rules to filter and handle anomalous traffic at the Edge layer.
- Enable WAF Logging and route centralized logs to an Amazon CloudWatch Log Group.
- Configure CloudWatch Alarms to monitor blocked request metrics and publish alerts to Amazon SNS.
- Create an Amazon SNS Topic for automated administrator email notifications.
- Develop and grant IAM permissions to a Python-based AWS Lambda function to automatically extract violating IPs from CloudWatch Logs and update the WAF IP Set.
- Execute attack simulation scripts (HTTP Flood) to test and verify the automated blocking mechanism end-to-end.
- Perform safe resource cleanup procedures post-workshop to prevent unnecessary cloud costs.
