---
title: "Create and Confirm Email Subscription"
date: 2026-08-24
weight: 2
chapter: false
pre: " <b> 5.7.2. </b> "
---

This article provides detailed instructions on subscribing a personal email address to receive notifications from an Amazon SNS Topic and completing the verification process (Confirm Subscription) to establish the alert delivery channel.

---

## 1. Create an Email Subscription on Amazon SNS

### Step 1: Access the SNS Topic

1. Log in to the **AWS Management Console**.
2. Navigate to the **Simple Notification Service (SNS)** service.
3. In the left navigation menu, select **Topics** and click on the `WAFAlertTopic` SNS Topic created in section 5.7.1.

![Select SNS Topic WAFAlertTopic](/images/Workshop/5.7-SNS/5.7.2-select-sns-topic.png)

---

### Step 2: Configure Subscription Parameters

1. On the `WAFAlertTopic` details page, switch to the **Subscriptions** tab.
2. Click the **Create subscription** button.

![Click Create Subscription](/images/Workshop/5.7-SNS/5.7.2-click-create-subscription.png)

3. Configure the following settings:
   - **Topic ARN:** Leave as default (auto-populated with the ARN of `WAFAlertTopic`).
   - **Protocol:** Select **Email**.
   - **Endpoint:** Enter your personal or administrator email address (e.g., `admin@example.com`).
4. Click the **Create subscription** button at the bottom of the page.

![Configure Email Subscription](/images/Workshop/5.7-SNS/5.7.2-configure-subscription.png)
![Configure Email Subscription](/images/Workshop/5.7-SNS/5.7.2-configure-subscription2.png)

---

## 2. Verify Email Subscription (Confirm Subscription)

Upon successful creation, the Subscription status will display **Pending confirmation**. AWS will automatically send a verification email to the registered address.

### Step 1: Check Email Inbox

1. Open the inbox of the email address registered in the previous step.
2. Look for an email with the subject: `AWS Notification - Subscription Confirmation` sent by **AWS Notifications**.

![Check Verification Email](/images/Workshop/5.7-SNS/5.7.2-check-email-inbox.png)

---

### Step 2: Click Verification Link

1. Open the email message and click the **Confirm subscription** link.

2. A browser window will open displaying the AWS confirmation page with the message **Subscription confirmed!**.

![Successful Confirmation Screen](/images/Workshop/5.7-SNS/5.7.2-subscription-confirmed-page.png)

---

## 3. Verify Status on AWS Console

### Step 1: Verify on AWS SNS Dashboard

1. Return to the **AWS Management Console** -> **SNS** service -> **Topics** -> `WAFAlertTopic`.
2. Under the **Subscriptions** tab, check the list:
   - **Status:** Changes from `Pending confirmation` to `Confirmed` (in green).

![Verify Confirmed Status on Console](/images/Workshop/5.7-SNS/5.7.2-verify-confirmed-status.png)

---

## 4. Expected Outcomes

Upon completing this practical exercise:

- The administrator email address is successfully linked to the `WAFAlertTopic` Amazon SNS Topic.
- The Subscription status reaches the **Confirmed** state, ready to receive automated alert notifications sent from CloudWatch Alarms whenever the system detects security threats.
