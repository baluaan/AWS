---
title: "Proposal"
date: 2026-09-11
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# AWS SSH Automated Threat Protection

## Automated SSH Attack Monitoring, Detection, and Mitigation System on AWS

---

# 1. Executive Summary

**AWS SSH Automated Threat Protection** is a cloud-native cybersecurity solution designed to automate the process of detecting, analyzing, and proactively mitigating SSH Brute-Force and Password Guessing attacks targeting Amazon EC2 (Ubuntu Linux) instances.

The system fully leverages AWS serverless and managed services, including **Amazon EC2**, **Amazon CloudWatch Logs**, **Subscription Filters**, **AWS Lambda**, **Amazon DynamoDB**, **Amazon SNS**, and **Network ACLs (NACLs)**. By collecting real-time SSH login logs (`/var/log/auth.log`), the system automatically parses failed authentication events, tracks source IP access frequencies, and maintains a rolling 1-minute time window. When the number of failed attempts reaches or exceeds the threshold (**5 attempts / 1 minute**), the system automatically dispatches email alerts via **Amazon SNS** and triggers an automated remediation workflow: injecting a `DENY` rule (`/32`) into the Network ACL to completely drop traffic from the attacking IP at the Subnet boundary.

This solution guarantees near-instantaneous automated response, drastically reduces manual administrator intervention, optimizes operational costs, and ensures high availability for cloud infrastructure.

---

# 2. Problem Statement

## Current Challenges

The SSH (Secure Shell) protocol is the primary remote administration method for Linux operating systems. Due to the requirement of keeping administrative ports open (default Port 22), EC2 instances are frequently targeted by automated scanners and credential-stuffing attacks.

Traditional defense mechanisms face several critical drawbacks:

- **Passive Logging:** Simply collecting failed login logs without an automated response mechanism leaves systems exposed to exploitation.
- **Delayed Manual Response:** Administrators must manually inspect log files (`auth.log`), extract malicious IPs, and configure firewall rules by hand. This process is slow, risking system compromise before action is taken.
- **Lack of Immediate Alerting:** Administrators lack real-time visibility into active brute-force attempts as they occur.
- **Resource Exhaustion:** High-frequency brute-force attacks consume host CPU, RAM, and network bandwidth if not blocked at the perimeter (Subnet network layer).
- **Absence of State Tracking:** Lacking a centralized state store makes it difficult to accurately measure attack density from a single IP over short time intervals.

## Proposed Solution

This project proposes building the **AWS SSH Automated Threat Protection** system to fully automate detection, alerting, and mitigation in accordance with AWS security best practices:

- **Amazon EC2 (Ubuntu Linux):** Serves as the target SSH host.
- **Amazon CloudWatch Logs:** Centralizes and stores SSH access logs in real time.
- **Subscription Filter:** Scans log streams for the `Failed password` pattern and triggers downstream processing.
- **AWS Lambda:** Serverless orchestrator that decodes payloads, extracts source IPs via Regex, interacts with DynamoDB, publishes SNS notifications, and updates Network ACL rules.
- **Amazon DynamoDB:** NoSQL store maintaining failed login counts per IP within a 1-minute rolling window.
- **Amazon SNS:** Pushes automated email alerts to security administrators upon threat detection.
- **Network ACL (NACL):** Stateless Subnet firewall enforcing `/32` `DENY` rules to drop malicious traffic at the network edge.

## Key Benefits

- **Near-Instantaneous Automated Mitigation:** Blocks malicious IPs within seconds upon reaching the threshold without human intervention.
- **Proactive Email Alerts:** Keeps administrators informed with detailed attack reports delivered via Amazon SNS.
- **Perimeter Network Blocking:** Dropping traffic at the Network ACL layer prevents packets from reaching the EC2 host, saving operating system compute resources.
- **Fully Serverless Operations:** Eliminates dedicated security monitoring infrastructure costs through pay-as-you-go pricing.
- **High Scalability:** Scales automatically with incoming log volume and attack intensity without bottlenecking.

---

# 3. Architecture Overview

The solution follows a Cloud-Native Serverless Security Architecture on AWS.

## Solution Architecture

System components and execution workflow:

- **Attacker → EC2 Ubuntu → CloudWatch Logs → Subscription Filter (`Failed password`) → AWS Lambda ↔ DynamoDB (Attack Counter) →  Network ACL (DENY /32 Rule)**
- **Attacker → EC2 Ubuntu → CloudWatch Logs → Metric Filter (`Failed password`)→ Amazon SNS (Email Alert)**

![System Architecture](/images/2/1.jpg)

## AWS Services Utilized

- **Amazon EC2:** Ubuntu Linux server running the SSH daemon.
- **Amazon CloudWatch Logs:** Log ingestion and management service.
- **CloudWatch Subscription Filter:** Pattern-matching event filter (`Failed password`).
- **CloudWatch Metric Filter:** Pattern-matching event filter (`Failed password`).
- **AWS Lambda:** Automated logic execution engine.
- **Amazon DynamoDB:** NoSQL database tracking IP failure metrics.
- **Amazon SNS:** Notification service delivering email security alerts.
- **Network ACL (NACL):** Stateless Subnet firewall.
- **AWS IAM:** Access control and role management across AWS services.

## Component Design

### Data Collection & Filtering Layer

- **EC2 Ubuntu:** Configured with CloudWatch Agent to stream `/var/log/auth.log` to CloudWatch Logs.
- **CloudWatch Logs Group:** Receives and stores real-time authentication logs.
- **Subscription Filter:** Evaluates log lines against `Failed password` pattern, immediately streaming matching events to AWS Lambda.

### Processing & State Management Layer

- **AWS Lambda:** Decodes gzipped event payloads and extracts source IP addresses (`clientIp`) using Regular Expressions.
- **Amazon DynamoDB Table (`SSHAttackCounter`):**
  - **Partition Key:** `AttackerIP` (String).
  - **Attributes:** `FailedCount` (Number), `WindowTimestamp` (Number).
  - **Logic:** Increments and evaluates failure thresholds within 1-minute windows.

### Alerting & Remediation Layer

- **Amazon SNS:** Dispatches email alerts when failure counts reach >= 5 within 1 minute.
- **Network ACL Enforcement:** Lambda queries current NACL rules attached to the EC2 Subnet.
- **Rule Enforcement:** If the IP is unblocked, Lambda programmatically inserts a top-priority `DENY` rule (`/32`) at the Subnet boundary.

---

# 4. System Workflow

## Detailed Execution Sequence


The automated detection, alerting, and mitigation lifecycle consists of 10 steps:

1. An attacker executes invalid SSH login attempts into the **Amazon EC2 Ubuntu** server.
2. The SSH Server on the EC2 instance records failed login events into the system log file `/var/log/auth.log`.
3. The **CloudWatch Agent** installed on the EC2 instance automatically pushes new log data to the **CloudWatch Logs Group**.
4. The **Subscription Filter** scans the logs, detects event strings matching the pattern configuration `Failed password`, and forwards the event payload to **AWS Lambda**.
5. **AWS Lambda** decompresses the data and uses regular expressions (Regex) to accurately extract the source IP address (`clientIp`).
6. Lambda queries and increments the failed login counter for that IP address in an **Amazon DynamoDB** table.
7. Lambda checks the total number of failures within the most recent 1-minute window.
8. If the failure count is less than 5, Lambda terminates the process, and the system continues monitoring.
9. If the failure count reaches **5 times/1 minute** or more, Lambda triggers an automated response workflow via **Network ACL** to block the IP.
10. The **Metric filter** is checked; if it exceeds the threshold of **5 times/1 minute**, it triggers the **SNS topic** to send an email alert.

---

# 5. Technical Implementation

## Deployment Stages

The project is executed across 6 core engineering steps:

1. **EC2 Infrastructure Initialization & SSH Configuration:** Create an Ubuntu EC2 server, configure a Security Group to open port 22.
2. **CloudWatch Agent & Log Group Configuration:** Install the CloudWatch Agent on EC2 to collect `/var/log/auth.log` logs into a CloudWatch Log Group.
3. **Amazon SNS Topic Initialization:** Create an SNS Topic along with an email subscription to receive alert notifications.
4. **Automation Process Initialization:** Use DynamoDB to track failed login counts. Develop a Lambda function in Python to extract IPs, count failures, and create NACL rules; link a Subscription Filter from the CloudWatch Log Group to Lambda.
6. **Scenario Testing & Evaluation:** Use a tool/script to simulate an SSH Brute-Force attack with 5 failed attempts/minute to verify the SNS email alert workflow and automatic blocking via Network ACL.

## Technical Requirements

### Languages & SDKs

- Python 3.12
- AWS SDK for Python (`boto3`)

### Cloud Infrastructure & APIs

- AWS Management Console
- Amazon EC2 API (`DescribeInstances`, `DescribeNetworkAcls`, `CreateNetworkAclEntry`)
- Amazon DynamoDB API (`GetItem`, `UpdateItem`)
- Amazon SNS API (`Publish`)
- CloudWatch Logs Subscription Filters

### Testing Tools

- OpenSSH CLI / Bash Scripting

---

# 6. Implementation Roadmap

| Step | Technical Task | Relevant Components |
| :--- | :--- | :--- |
| **Step 1** | Launch Ubuntu EC2 instance, attach IAM Role, and open SSH access port. | Amazon EC2, IAM |
| **Step 2** | Install CloudWatch Agent on Ubuntu and stream `/var/log/auth.log`. | CloudWatch Logs, EC2 |
| **Step 3** | Create DynamoDB counter table and establish Amazon SNS Topic for email alerts. | Amazon DynamoDB, Amazon SNS |
| **Step 4** | Configure IAM execution role for Lambda with required CloudWatch, DynamoDB, SNS, and EC2 permissions. | AWS IAM |
| **Step 5** | Deploy Python Lambda source code and configure CloudWatch Subscription Filter (`Failed password`). | AWS Lambda, CloudWatch |
| **Step 6** | Execute SSH attack simulation (>= 5 attempts/min), verify SNS email alerts, validate Network ACL `DENY` `/32` entries, and finalize documentation. | Bash, Amazon SNS, Network ACL |

---

# 7. Estimated Cost Analysis

Operational costs are minimal due to serverless pay-as-you-go pricing.

| AWS Service | Cost Description | Estimated Monthly Cost |
| :--- | :--- | :--- |
| **Amazon EC2** | Ubuntu t3.micro instance (Free Tier eligible) | ~$0.00 - $8.50 / month |
| **Amazon CloudWatch** | Ingestion & Storage for Log Group | ~$0.50 / month |
| **AWS Lambda** | Request volume & Execution duration | ~$0.00 / month (Free Tier) |
| **Amazon DynamoDB** | On-Demand Read/Write Requests & Storage | ~$0.00 / month (Free Tier) |
| **Amazon SNS** | First 1,000 email notifications | **Free** |
| **Network ACL** | Built-in VPC network control feature | **Free** |
| **Total Estimated Cost** | **Monthly Operational Expenses** | **~$0.50 - $9.00 USD / month** |

---

# 8. Risk Assessment & Mitigation Strategies

## Risks & Solutions

- **Risk 1 - Network ACL Rule Limit Capacity:** Network ACLs have default entry limits (typically 20–40 rules). Exceeding this quota causes rule creation API calls to fail.
  - *Mitigation:* Program Lambda to implement a cleanup mechanism (TTL) that removes stale `DENY` rules after a designated period (e.g., 24 hours) or publish SNS alerts when rule capacity approaches limits.
- **Risk 2 - IAM Permission Misconfigurations:** Lambda fails to insert NACL entries, write to DynamoDB, or send SNS alerts due to insufficient privileges.
  - *Mitigation:* Ensure strict IAM policy definitions explicitly authorizing `ec2:CreateNetworkAclEntry`, `dynamodb:UpdateItem`, and `sns:Publish`.
- **Risk 3 - Administrator False Positives:** Administrators entering incorrect credentials 5 times in 1 minute risk getting automatically locked out.
  - *Mitigation:* Maintain an IP Whitelist array within Lambda code to bypass threshold evaluation for trusted administrative static IPs.

---

# 9. Expected Outcomes

## Technical Deliverables

- Fully deployed automated monitoring, detection, alerting, and mitigation system for EC2 SSH brute-force attacks.
- Reliable DynamoDB-backed rate counter tracking failures under a 1-minute window.
- Instant email notifications delivered via Amazon SNS upon threat confirmation.
- Dynamic creation of `/32` `DENY` rules on Subnet Network ACLs.
- Complete elimination of manual security intervention during incident response.

## Practical Business Value

- Enhanced cloud host security posture against automated Internet brute-force scanners.
- Improved incident awareness for administrative teams through real-time SNS notifications.
- Reduced EC2 CPU/RAM overhead by terminating malicious connections at the Subnet perimeter.
- Reusable serverless baseline architecture for cloud security automation on AWS.