---
title: "Deploying SNS"
date: 2026-09-11
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

### Objectives

Deploy AWS SNS to send timely alerts immediately when an AWS Lambda function detects a source IP exceeding the attack threshold (≥ 5 failed attempts per minute) and blocks the IP. SNS will instantly send an incident notification email to the administrator.

---
## 1. Overview
Amazon SNS is a fully managed, highly scalable Publish/Subscribe (Pub/Sub) messaging service provided by AWS. This service allows decoupling of Publishers from Subscribers.
In this project, AWS SNS will issue real-time email alerts whenever the number of "Failed password" login attempts exceeds the threshold of 5 times per minute.

---
## 2. Deployment Process

1. Create a **CloudWatch Alarm**
Go to **CloudWatch** > **Alarms** > **Create alarm**
Enter the details in **Step 1**:
  - Namespace: SecurityMonitoring
  - Metric name: SSHBruteForce
  - Statistic: Sum
  - Period: 1 minute
  - Set threshold: Greater/Equal to 5 times per minute
  - Click: Next
![Step1](/images/5/6/1.png)

Next, proceed to **Step 2**:
  - Keep all default settings
  - Send a notification: SSH-Attack-Alerts
  - Email: caphonglon2004@gmail.com
  - Click: Next
![Step2](/images/5/6/2.png)

Finally, proceed to **Step 3**:
  - Name: SSH-BruteForce-Detected
  - Click: Next
  - Click: Create alarm
![Step3](/images/5/6/3.png)

2. Create **AWS SNS Topic**
Go to **Amazon SNS** > **Topics** > **Create topic**
Enter the following information:
  - Type: Standard
  - Name: SSH-Attack-Alerts
  - Display name: canh-bao
  - Click: Create topic
![Create Topic](/images/5/6/4.png)

3. Create **Subscription**
Go to **Amazon SNS** > **Subscriptions** > **Create subscription**
Fill in the required information:
  - Topic ARN: Select the topic created above
  - Protocol: Email
  - Endpoint: caphonglon2004@gmail.com
  - Click: Create subscription
![Create subscription](/images/5/6/5.png)


## 3. Expected Results

After completing this chapter, you will achieve:
- Real-time monitoring enabling administrators to instantly track dangerous Brute Force attacks without having to manually monitor logs or check the AWS Console continuously.
- Instant email notifications whenever "Failed password" attempts exceed 5 times per minute.