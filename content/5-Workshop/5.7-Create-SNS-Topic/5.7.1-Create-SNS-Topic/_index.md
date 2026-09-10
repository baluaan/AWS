---
title: "Create SNS Topic"
date: 2026-08-24
weight: 1
chapter: false
pre: " <b> 5.7.1. </b> "
---

This article provides detailed instructions on creating an Amazon Simple Notification Service (Amazon SNS) Topic using the AWS Management Console. This SNS Topic serves as an intermediary messaging channel to receive alerts from CloudWatch Alarms.

---

## 1. Create an Amazon SNS Topic

### Step 1: Access the Amazon SNS Service

1. Log in to the **AWS Management Console**.
2. In the search bar, type `SNS` and select the **Simple Notification Service** service.
3. In the left navigation menu, select **Topics**.
4. Ensure your active AWS Region is set to **US East (N. Virginia) us-east-1**.

![Access Amazon SNS Topics](/images/Workshop/5.7-SNS/5.7.1-sns-topics-menu.png)

---

### Step 2: Configure Topic Parameters

1. On the Topics interface, click the **Create topic** button.
2. **Type:** Select **Standard** (suitable for sending Email notifications and triggering Lambda functions with high-throughput delivery).
3. **Name:** Enter `WAFAlertTopic`.
4. **Display name:** Enter `WAF Alert Topic` (This name will appear as the sender/header in alert emails sent to administrators).

![Configure SNS Topic Parameters](/images/Workshop/5.7-SNS/5.7.1-configure-sns-topic.png)

---

### Step 3: Complete Topic Creation

1. Scroll to the bottom of the page and keep all default advanced configuration settings.
2. Click the **Create topic** button.

![Click Create Topic](/images/Workshop/5.7-SNS/5.7.1-finish-create-topic.png)

---

## 2. Verify SNS Topic Information

Upon successful creation, the topic details page will display the following key properties:

- **Name:** `WAFAlertTopic`
- **ARN:** `arn:aws:sns:us-east-1:<ACCOUNT_ID>:WAFAlertTopic`
- **Type:** Standard

> **Note:** Copy the **ARN** string of this SNS Topic for use in CloudWatch Alarms configuration in section 5.8 and IAM Policy permissions in later steps.

![Verify SNS Topic ARN](/images/Workshop/5.7-SNS/5.7.1-verify-sns-topic.png)

---

## 3. Expected Outcomes

Upon completing this practical exercise:

- An **Amazon SNS Topic** named `WAFAlertTopic` is successfully created in the `us-east-1` Region with the `Standard` type.
- The Topic **ARN** is ready for Email Subscription registration in section 5.7.2 and integration with CloudWatch Alarms in subsequent modules.
