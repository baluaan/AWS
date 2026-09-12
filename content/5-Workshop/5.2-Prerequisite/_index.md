---
title: "Prerequisites"
date: 2026-09-11
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

### Objective

Ensure that the practitioner has full administrative access to the AWS Management Console, prepares the target test environment (Amazon EC2 Ubuntu), configures SSH Brute Force attack simulation tools, and prepares the required AWS Lambda source code files before deploying the automated defense system.

---

## 1. Required Tools and Resources

This automated incident response workshop primarily operates on the **AWS Management Console (Web UI)** in combination with a local workstation and an EC2 instance for practical testing. You need to prepare the following components:

- **AWS Account:** Must have permissions to create and manage Amazon EC2, CloudWatch Logs, AWS Lambda, Amazon DynamoDB, Amazon SNS, VPC (Network ACL), and IAM services (the "AdministratorAccess" policy is recommended).
- **Target Server (Amazon EC2):** 01 Instance running **Ubuntu Server 22.04 LTS / 24.04 LTS** acting as the target SSH server under attack.
- **Web Browser:** The latest version of Google Chrome, Microsoft Edge, or Mozilla Firefox.
- **SSH Attack Simulation Tools (Terminal / PowerShell / Hydra):** Used to perform repeated invalid SSH login attempts.
  - For Windows: **PowerShell** / **PuTTY** / **MobaXterm** or **Hydra** (for automated testing).
  - For macOS/Linux: **Terminal** (using the "ssh" command or "hydra" tool).
- **Code Editor:** Visual Studio Code or Notepad++ to review/edit the Lambda script ("lambda_function.py") for processing SSH logs and updating DynamoDB/NACL.
- **Email Address (Gmail):** Used to subscribe to and receive incident notification emails from the Amazon SNS Topic.

---

## 2. Detailed Preparation Steps

### Step 1: Log in to AWS Console and Select Region

1. Log in to the [AWS Management Console](https://aws.amazon.com/console/).
2. Select the deployment region (e.g., **Asia Pacific (Singapore) — ap-southeast-1** or **US East (N. Virginia) — us-east-1**) from the top-right corner of the Console header.

> ⚠️ **IMPORTANT NOTE:** All resources including EC2 Instance, CloudWatch Logs Group, Lambda Function, DynamoDB Table, SNS Topic, and Network ACL must be deployed in the **same Region and within the same VPC/Subnet** to ensure seamless integration and accurate reaction.

**Checkpoint:** The Region name in the navigation bar correctly displays the target region (e.g., **Asia Pacific (Singapore) ap-southeast-1**).

---

### Step 2: Prepare Ubuntu EC2 Server and Configure CloudWatch Agent

1. Launch an Ubuntu EC2 Instance (e.g., "t2.micro" or "t3.micro").
2. Attach an **IAM Role** with the "CloudWatchAgentServerPolicy" managed policy to allow pushing system logs to CloudWatch.
3. Install and configure the **CloudWatch Agent** on the EC2 instance to automatically monitor and push the system log file "/var/log/auth.log" to CloudWatch Logs Group.

Verify SSH log recording on the EC2 instance using the command: "sudo tail -f /var/log/auth.log"

**Checkpoint:** The log file "/var/log/auth.log" actively records all successful and failed SSH authentication events ("Failed password").

---

### Step 3: Verify SSH Simulation Tools on Local Workstation

Open Terminal (macOS/Linux) or PowerShell (Windows) to verify connectivity and test SSH authentication requests to the EC2 instance:

Test basic SSH command execution using: "ssh invalid_user@<EC2_PUBLIC_IP>"

Or run automated Brute Force testing via Hydra using: "hydra -l admin -P passwords.txt <EC2_PUBLIC_IP> ssh -t 4"

**Checkpoint:** The SSH connection is rejected due to invalid credentials, and the EC2 server logs a "Failed password for ..." entry into "/var/log/auth.log".

---

### Step 4: Prepare the Incident Response Script (AWS Lambda)

Prepare the Python source code file ("lambda_function.py") on your local machine with the following core functionalities:
- Decompress and decode incoming log payloads sent by **CloudWatch Subscription Filter**.
- Extract the source IP address ("clientIp") using **Regex**.
- Record and increment the failure counter within a 1-minute window in **Amazon DynamoDB**.
- Evaluate the threshold (≥ 5 failures/1 min): trigger alert emails via **Amazon SNS** and automatically attach a "DENY" CIDR "/32" rule to the target **Network ACL**.

**Checkpoint:** The Python Lambda script is ready for deployment and linking with CloudWatch, DynamoDB, and Network ACL.

---

## 3. Expected Outcomes

Upon completing this chapter, you will have achieved the following prerequisites:

- Successfully logged into the AWS Management Console in the selected Region.
- Deployed and verified an Ubuntu EC2 instance integrated with CloudWatch Agent to record "/var/log/auth.log".
- Set up a local workstation environment ready to execute SSH Brute Force attack simulations.
- Prepared an active Email address subscribed to Amazon SNS alerts.
- Finalized the Python Lambda source code, ready to be deployed and configured with DynamoDB and Network ACL.