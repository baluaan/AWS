---
title: "Workshop Overview"
date: 2026-09-11
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### Objectives

This workshop guides you through deploying the **AWS SSH Automated Threat Protection** solution for EC2 Linux servers on AWS using Cloud-Native Serverless architecture, managed security services, and automated incident response workflows. Upon completing this workshop, you will be able to build a complete SSH server defense system capable of automatically collecting logs, tracking failure frequencies, sending alerts, and creating block rules for violating IP addresses at the Subnet network layer (Network ACL) without manual administrator intervention.

---

## 1. Problem Statement and Solution Overview

The SSH (Secure Shell) protocol is a common method for remotely managing Linux servers. However, opening the SSH port (Port 22) to the Internet makes EC2 instances prime targets for automated password scanning attacks (SSH Brute-force and Password Guessing). During an attack, traditional response procedures require operations engineers to manually inspect log files (`/var/log/auth.log`), extract malicious IPs, and manually add firewall block rules. This process is time-consuming, leading to service downtime and the risk of server compromise.

Instead of manual handling, this workshop applies the **AWS SSH Automated Threat Protection** solution based on Serverless Security architecture on AWS. An **Amazon EC2 (Ubuntu Linux)** instance acts as the protected service infrastructure.

All system login logs are pushed centrally to **Amazon CloudWatch Logs** by the CloudWatch Agent. A **CloudWatch Subscription Filter** filters `Failed password` event patterns and streams the data directly to **AWS Lambda**. The Lambda function decompresses the data, extracts the source IP address, and updates a counter in **Amazon DynamoDB** within a 1-minute time window. When failed login attempts exceed the defined threshold (**5 attempts / 1 minute**), the system sends an email alert via **Amazon SNS**, and **AWS Lambda** automatically calls APIs to create a `DENY` rule on the **Network ACL (NACL)** to permanently block the `/32` source IP address directly at the Subnet network layer, completely dropping attack traffic before it reaches the EC2 instance.

---

## 2. System Architecture

The **AWS SSH Automated Threat Protection** architecture is deployed following a Serverless model on AWS, divided into 3 main functional layers:

- **Data Collection & Ingestion Layer:** Consists of **Amazon EC2 Ubuntu** (SSH Server), **CloudWatch Agent** (Collects `/var/log/auth.log`), and **CloudWatch Logs Group** (Centralized authentication log storage).
- **Processing & State Management Layer:** Consists of **Subscription Filter** (Filters `Failed password` pattern), **AWS Lambda** (Serverless function for decoding, IP extraction, and API calls), and **Amazon DynamoDB** (Stores failure counts per IP within a 1-minute window).
- **Remediation & Perimeter Enforcement Layer:** Consists of **Amazon SNS** (Dispatches incident email alerts), **Network ACL (NACL)** (Enforces `DENY` `/32` rules to block attacking IP connections at the Subnet layer), and **AWS IAM Role** (Grants secure execution permissions to Lambda).

**Figure 1 – AWS SSH Automated Threat Protection System Architecture**

![System Architecture](/images/proposal/system_architecture1.png)

---

## 3. System Execution Workflow

The core processing flow of the system occurs across 12 steps:

1. An Attacker performs invalid SSH login attempts against the **Amazon EC2 Ubuntu** server.

2. The SSH Server on EC2 logs the failed login event into the system log file `/var/log/auth.log`.

3. The **CloudWatch Agent** installed on EC2 automatically streams new log entries to **CloudWatch Logs Group**.

4. The **Subscription Filter** scans logs, detects event strings matching the `Failed password` pattern, and sends the event payload to **AWS Lambda**.

5. **AWS Lambda** decompresses the compressed payload and uses Regular Expressions (Regex) to parse the exact source IP address (`clientIp`).

6. Lambda queries and increments the failed attempt count for that IP in the **Amazon DynamoDB** table.

7. Lambda checks the total number of failures within the last 1-minute rolling window.

8. If the failure count is less than 5, Lambda completes execution, and the system continues monitoring.

9. If the failure count reaches **5 attempts / 1 minute** or more, the system sends an email alert via **Amazon SNS**, and Lambda triggers the automated response workflow.

10. Lambda inspects the current rule list on the **Network ACL** attached to the EC2 Subnet.

11. If the IP is not already in the block list, Lambda automatically inserts a `DENY` rule for the offending IP in CIDR `/32` format.

12. The Network ACL immediately denies all incoming SSH connection packets from the offending IP address right at the Subnet perimeter.

---

## 4. AWS Services Utilized

This workshop utilizes the following AWS services:

### Infrastructure and Log Management

- **Amazon EC2:** Ubuntu Linux 22.04 LTS server running the SSH Server daemon.
- **Amazon CloudWatch Logs:** Centralized collection, storage, and management of SSH login logs.
- **CloudWatch Subscription Filter:** Scans and filters event log streams using pattern matching.

### State Management, Alerting, and Automation

- **AWS Lambda:** Executes Python 3.12 code (using AWS Boto3 SDK) to process IP extraction logic, trigger alerts, and make API calls.
- **Amazon DynamoDB:** NoSQL database storing failure counts per IP and time window.
- **Amazon SNS:** Automated messaging service delivering email security alerts to administrators.

### Security and Access Control

- **Network Access Control List (NACL):** Stateless Subnet-level firewall enforcing `DENY` rules.
- **AWS IAM:** Manages secure execution permissions between services based on Least Privilege principles.

### Testing Tools

- OpenSSH CLI / Bash Scripting (Simulates SSH brute-force attack sequences).

---

## 5. Expected Outcomes

Upon completing this workshop, you will be able to:

- Launch an EC2 Ubuntu server, configure SSH, and install CloudWatch Agent to stream `/var/log/auth.log` centrally.
- Configure CloudWatch Subscription Filter with the `Failed password` pattern to filter failed authentication events.
- Create an Amazon DynamoDB table to maintain IP tracking counters under a 1-minute window, alongside an Amazon SNS topic for email alerts.
- Develop an AWS Lambda function using Python 3.12 to extract offending IPs, interact with DynamoDB, publish SNS messages, and execute Network ACL API calls.
- Assign precise IAM Role permissions for Lambda to securely interact with CloudWatch Logs, DynamoDB, Amazon SNS, and EC2 Network ACLs.
- Execute SSH Brute-force attack simulations to verify the automated workflow of dispatching SNS email alerts and appending `DENY` `/32` rules on Network ACLs.
- Perform a safe resource cleanup procedure post-workshop to avoid incurring ongoing hosting charges.