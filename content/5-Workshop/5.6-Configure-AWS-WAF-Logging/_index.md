---
title: "Configure AWS WAF & Logging"
date: 2026-08-24
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

### Objectives

Build a Web Application Firewall (AWS WAF) layer at the Edge to protect the Amazon CloudFront Distribution, configure traffic control rules (IP Set & Rate-based Rule), and set up centralized logging to Amazon CloudWatch Logs.

---

## 1. Overview

The Web Application Firewall and Logging layer (WAF & Logging Layer) serves as the core security component safeguarding the application infrastructure against Internet threats. In this architecture, **AWS WAF (Web Application Firewall)** is attached directly to the Amazon CloudFront Distribution to inspect all incoming HTTP/HTTPS requests before they reach the S3 Origin.

To defend against Denial of Service attacks (HTTP Flood/DDoS) and prepare event data for automated response workflows, three primary components are configured:

- **IP Set (`AutoBlockedIPSetV6`):** Initializes an empty IP list that acts as a repository for malicious IP addresses automatically updated by AWS Lambda.
- **Web ACL (`WebsiteProtectionACL`):** Configures the primary firewall entity, links it to the CloudFront Distribution, and defines rule conditions (a Rate-based Rule to limit request frequency and an IP Set Rule to block offending IPs).
- **WAF Access Logging (`aws-waf-logs-cloudfront`):** Enables streaming of all WAF access logs directly to Amazon CloudWatch Logs to serve as the input source for analytical and automated processes.

---

## 2. Deployment Workflow

The configuration process for AWS WAF and Logging is divided into three distinct modules:

- **Section 5.6.1:** Create a WAF IP Set (`AutoBlockedIPSetV6`) under the CloudFront Scope (`us-east-1`).
- **Section 5.6.2:** Create a Web ACL (`WebsiteProtectionACL`), configure Rate-based Rules, attach the IP Set, and associate it with the CloudFront Distribution.
- **Section 5.6.3:** Create the CloudWatch Log Group `aws-waf-logs-cloudfront` and enable WAF Access Logging.

---

## 3. Practical Exercises

Complete the following practical exercises in sequence:

- **[5.6.1. Create WAF IP Set](5.6.1-Create-WAF-IP-Set)**
- **[5.6.2. Create and Configure Web ACL](5.6.2-Create-and-configure-Web-ACL)**
- **[5.6.3. Configure WAF Access Logging to CloudWatch Logs](5.6.3-Configure-WAF-Logging-CloudWatch)**

---

## 4. Expected Outcomes

Upon completing this chapter, you will achieve the following:

- The **WAF IP Set** (`AutoBlockedIPSetV6`) successfully created under the `CLOUDFRONT` scope.
- The **Web ACL** (`WebsiteProtectionACL`) fully configured with security rules and attached to the CloudFront Distribution.
- **WAF Access Logs** automatically captured and routed to the **CloudWatch Log Group** named `aws-waf-logs-cloudfront`.
- A functional firewall and logging infrastructure prepared for CloudWatch Alarm integration and Lambda automation in subsequent chapters.
