---
title: "Delete System Resources"
date: 2026-09-13
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

### Objectives

Delete all deployed resources and services on AWS in the correct dependency order to ensure no permission conflicts occur, prevent data coupling errors, and avoid incurring unintended costs after completing the project.

---

## 1. Resource Deletion Order Overview

When cleaning up the system, the most important rule is to **delete in reverse order of creation**. This practice disables automated triggers first, followed by releasing data and network infrastructure, and finally deleting IAM authorization policies.

---

## 2. Detailed Execution Steps

### Step 1: Amazon CloudWatch (Subscription Filter & Metric Filter)
- **Action:** Unlink and delete the **Subscription Filter** (`ssh`) and **Metric Filter** in the Log Group `/aws/ec2/security/auth`.
- **Reason:** Stop automatically pushing log data and triggering the AWS Lambda function when new events occur.

### Step 2: AWS Lambda (Function)
- **Action:** Delete the Lambda function (`ssh-auto-block`).
- **Reason:** Ensure no background code executes to call DynamoDB APIs, insert block rules into Network ACL, or publish messages to SNS.

### Step 3: Amazon DynamoDB (Table)
- **Action:** Delete the counter table (`SSHBruteforceCounter`).
- **Reason:** Release the counter state storage table after Lambda has been completely removed.

### Step 4: Amazon Network ACL (NACL Rules)
- **Action:** Remove automatically inserted `DENY` rules or disassociate Subnets (if a dedicated NACL `SSH-Auto-Block-NACL` was created).
- **Reason:** Restore the initial network configuration state for the Subnet.

### Step 5: Amazon SNS (Topic & Subscription)
- **Action:** Delete the **Email Subscription** and **SNS Topic**.
- **Reason:** Cancel the notification channel for incident alerts via email.

### Step 6: Amazon CloudWatch (Log Group)
- **Action:** Delete the Log Groups `/aws/ec2/security/auth` and `/aws/ec2/system/syslog`.
- **Reason:** Clean up all system log data stored on the cloud.

### Step 7: Amazon EC2 (Instance)
- **Action:** **Terminate** the Ubuntu EC2 server (`AWS-Security-Monitoring`).
- **Reason:** Reclaim the target server and release associated dynamic IP addresses as well as attached EBS storage volumes.

### Step 8: AWS IAM (Roles & Policies)
- **Action:** Delete Inline Policies and IAM Roles (`Lambda-SSH-AutoBlock-Role`, `EC2-CloudWatchAgent-Role`).
- **Reason:** **Delete last.** If IAM Roles are deleted first, running services may encounter errors due to lost access permissions during resource termination.

---
## 3. Expected Results

- All project services (AWS CloudWatch, AWS Lambda, AWS DynamoDB, NACL, AWS SNS, AWS EC2, IAM) are completely deleted.
- Ensure no unexpected charges incur when services are no longer in use.

- > **Note:** Resource deletion is permanent and cannot be undone.