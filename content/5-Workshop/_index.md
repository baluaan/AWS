---
title: "Workshop"
date: 2026-09-11
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Deploying Automated SSH Attack Monitoring, Detection, and Mitigation on AWS

#### Overview

In this workshop, we will build and deploy an **AWS SSH Automated Threat Protection** solution for EC2 Linux servers based on a Cloud-Native Serverless architecture on AWS infrastructure.

The solution utilizes core AWS services including **Amazon EC2 (Ubuntu)**, **Amazon CloudWatch Logs**, **Subscription Filters**, **AWS Lambda**, **Amazon DynamoDB**, **Amazon SNS**, **Network ACL (NACL)**, and **AWS IAM** to establish a real-time monitoring mechanism for SSH login traffic. It automatically detects abnormal access patterns (such as SSH Brute-Force attacks), sends email notifications via SNS, and programmatically applies `DENY` rules against malicious source IP addresses (`/32`) at the Subnet network boundary layer without requiring manual administrator intervention.

Throughout this workshop, you will complete an end-to-end deployment workflow: from setting up project prerequisites, configuring the EC2 Ubuntu server and streaming authentication log files (`/var/log/auth.log`) to CloudWatch Logs, provisioning a DynamoDB counter table with a 1-minute tracking window, developing Lambda automation functions (Python 3.12) to update Subnet NACLs and trigger SNS email alerts, to executing real-world attack simulation tests and safely cleaning up AWS resources post-testing.

#### Table of Contents

1. [Workshop Overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequisite/)
3. [Project Foundation](5.3-Project-foundation/)
4. [Launch EC2 Ubuntu Instance & Configure SSH](5.4-Launch-EC2-Ubuntu/)
5. [Configure CloudWatch Agent & Collect Log `/var/log/auth.log`](5.5-Configure-CloudWatch-Agent/)
6. [Provision Amazon DynamoDB Table & Amazon SNS Topic](5.6-Create-DynamoDB-SNS/)
7. [Create IAM Roles & Develop AWS Lambda Function](5.7-Create-Lambda/)
8. [Configure CloudWatch Subscription Filter](5.8-Configure-Subscription-Filter/)
9. [System Testing & Automated Subnet NACL IP Blocking](5.9-Testing-Automation/)
10. [Resource Cleanup](5.10-Cleanup/)