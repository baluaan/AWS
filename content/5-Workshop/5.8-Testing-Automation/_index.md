---
title: "Workshop"
date: 2026-08-24
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Deploy Static Website Protection & Automated IP Blocking with AWS WAF & Lambda

#### Overview

In this workshop, we will build and deploy an **Automated Threat Protection** solution for a static website using a Cloud-Native Serverless architecture on AWS.

The solution leverages core AWS services including **Amazon S3**, **Amazon CloudFront**, **AWS WAF**, **Amazon CloudWatch**, **Amazon SNS**, **AWS Lambda**, and **AWS IAM** to establish real-time traffic monitoring, detect anomalous access behavior (such as HTTP Flood / Rate-limit breach), and automatically update the blacklist (WAF IP Set V6) at the Edge location without requiring manual intervention from system administrators.

Throughout this workshop, you will practice the complete end-to-end deployment workflow: from preparing project foundations, setting up S3 storage infrastructure & CloudFront CDN, configuring WAF Web ACL security rule sets, setting up SNS notification channels, writing automated execution logic with AWS Lambda (Python 3.12), to configuring CloudWatch Alarms, executing real-world attack simulation testing, and safely cleaning up resources post-testing.

#### Contents

1. [Workshop Overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequisite/)
3. [Project Preparation](5.3-Project-foundation/)
4. [Host Static Website on Amazon S3](5.4-Host-Static-Website-S3/)
5. [Distribute Content via Amazon CloudFront](5.5-Distribute-via-CloudFront/)
6. [Configure AWS WAF & Logging](5.6-Configure-AWS-WAF-Logging/)
7. [Initialize Amazon SNS Topic](5.7-Create-SNS-Topic/)
8. [Create IAM Role & AWS Lambda Function](5.8-Create-IAM-Role-Lambda/)
9. [Configure CloudWatch Alarm](5.9-Configure-CloudWatch-Alarm/)
10. [System Testing](5.10-Testing/)
11. [Resource Cleanup](5.11-Cleanup/)
