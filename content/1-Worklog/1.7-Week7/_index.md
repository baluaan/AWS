---
title: "Week 7 - Worklog"
date: 2026-08-29
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:

- Incident Detection & Alarm Triggering: Configure Metric Filters in CloudWatch Logs to parse WAF access logs and set up CloudWatch Alarms to detect high-frequency IP requests exceeding set thresholds.

- Automated Blocking Logic Development: Write and deploy an AWS Lambda function in Python (`boto3`) to extract offending IP addresses and dynamically update the WAF Blocked IP Set via API calls (`GetIPSet` & `UpdateIPSet`).

- Real-Time Notification Integration: Create an Amazon SNS Topic and subscribe notification endpoints (Email/Webhook) to instantly alert administrators upon threat mitigation.

- Security Policy Hardening: Apply the Principle of Least Privilege to configure fine-grained IAM Roles and Inline Policies for Lambda execution.

### Tasks to be carried out this week:

| Day | Task                                                                                                                                                                                                         | Start Date | Completion Date | Reference Material                      |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | --------------------------------------- |
| 2   | - Configure metric extraction & alarms.<br>- Create Metric Filters on CloudWatch Log Groups for WAF logs.<br>- Provision CloudWatch Alarms to trigger on single-IP request threshold violations.             | 31/08/2026 | 31/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 3   | - Develop automated response Lambda logic.<br>- Write Python code using `boto3` SDK to handle `GetIPSet` and `UpdateIPSet`.<br>- Implement IP parsing and CIDR format transformation.                        | 01/09/2026 | 01/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 4   | - Integrate automation workflow.<br>- Configure CloudWatch Alarm state changes to invoke the Lambda function automatically.<br>- Define time-to-live (TTL) logic for temporary IP block handling.            | 02/09/2026 | 02/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 5   | - Set up SNS notification system & IAM hardening.<br>- Create Amazon SNS Topic and configure Email/Webhook subscriptions.<br>- Define IAM Role and strict policy statements for Lambda execution privileges. | 03/09/2026 | 03/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 6   | - Functionality validation & Week 7 review.<br>- Test manual invocation of Lambda function with sample alarm payload.<br>- Verify dynamic IP addition in WAF IP Set and refine code execution paths.         | 04/09/2026 | 04/09/2026      | https://cloudjourney.awsstudygroup.com/ |

### Week 7 Achievements:

- Successfully Configured Detection Alarms: Built CloudWatch Metric Filters on WAF access logs and established threshold-based Alarms capable of identifying malicious traffic spikes.

- Deployed Lambda Response Engine: Wrote and deployed Python-based AWS Lambda logic using `boto3`, enabling seamless parsing of attacker IPs and automated WAF IP Set modifications.

- Integrated Instant Incident Notifications: Established Amazon SNS alert pipelines to dispatch real-time warning messages to security administrators whenever an IP is blocked.

- Applied Strict IAM Governance: Hardened access control for all automated components by enforcing Least Privilege execution policies on Lambda functions.
