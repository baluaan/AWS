---
title: "Initialize Amazon SNS Topic"
date: 2026-08-24
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

### Objectives

Provision the Amazon Simple Notification Service (Amazon SNS) to act as a central notification hub, receiving alerts from CloudWatch Alarms to automatically dispatch notifications to administrator emails and trigger AWS Lambda functions for incident response.

---

## 1. Overview

In this automated security architecture, **Amazon SNS (Simple Notification Service)** serves as a core Pub/Sub messaging service. When the system detects abnormal traffic volume exceeding configured thresholds, a CloudWatch Alarm publishes an alert message to the SNS Topic.

The **Amazon SNS Topic** then handles the simultaneous delivery of these messages to two distinct subscriber groups:

- **Email Subscription:** Delivers immediate alert notifications via email to keep administrators and DevOps teams informed.
- **AWS Lambda Subscription:** Directly triggers a Python Lambda function to parse log data and automatically block offending IP addresses.

---

## 2. Deployment Workflow

The configuration process for the Amazon SNS Topic is divided into two separate practical modules:

- **Section 5.7.1:** Create an Amazon SNS Topic (`WAFAlertTopic`) in the `us-east-1 (N. Virginia)` Region.
- **Section 5.7.2:** Subscribe a personal email address to receive notifications and complete the verification process (Confirm Subscription).

---

## 3. Practical Exercises

Complete the following practical exercises in sequence:

- **[5.7.1. Create SNS Topic](5.7.1-Create-SNS-Topic)**
- **[5.7.2. Create and Confirm Email Subscription](5.7.2-Create-Email-Subscription)**

---

## 4. Expected Outcomes

Upon completing this chapter, you will achieve the following:

- An **Amazon SNS Topic** named `WAFAlertTopic` successfully created in the `us-east-1` Region.
- An **Email Subscription** successfully attached and verified in the **Confirmed** state.
- A functional notification pipeline ready for integration with CloudWatch Alarms and AWS Lambda in subsequent modules.
