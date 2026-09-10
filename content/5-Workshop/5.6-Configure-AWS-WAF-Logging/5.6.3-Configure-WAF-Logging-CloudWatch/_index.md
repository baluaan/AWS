---
title: "Configure WAF Access Logging to CloudWatch Logs"
date: 2026-08-24
weight: 3
chapter: false
pre: " <b> 5.6.3. </b> "
---

This article provides detailed instructions on creating a standardized Amazon CloudWatch Log Group and enabling access logging on AWS WAF to automatically stream all access logs to CloudWatch.

---

## 1. Create a CloudWatch Log Group

### Step 1: Access the CloudWatch Logs Service

1. Log in to the **AWS Management Console**.
2. In the search bar, type `CloudWatch` and select the **CloudWatch** service.
3. In the left navigation menu, expand **Logs** and select **Log groups**.
4. Ensure your active AWS Region is set to **US East (N. Virginia) us-east-1** (as CloudFront WAF requires logs to be delivered to this region).

![Access CloudWatch Log Groups](/images/Workshop/5.6-WAF/5.6.3-cloudwatch-log-groups-menu.png)

---

### Step 2: Create a WAF-Compliant Log Group

1. Click the **Create log group** button.
2. **Log group name:** Enter the required AWS WAF prefix exactly: `aws-waf-logs-cloudfront` (or `aws-waf-logs-website-protection`).
   > **Important Note:** The AWS WAF Log Group name must strictly begin with the `aws-waf-logs-` prefix for the WAF Console to detect and allow association.
3. **Retention setting:** Select a log retention policy (e.g., **1 day** or **7 days** to optimize costs for this lab).
4. Click **Create**.

![Create CloudWatch Log Group](/images/Workshop/5.6-WAF/5.6.3-create-log-group.png)
![Create CloudWatch Log Group](/images/Workshop/5.6-WAF/5.6.3-create-log-group2.png)

---

## 2. Enable WAF Access Logging on Web ACL

### Step 1: Open WAF Logging Settings

1. Return to the **AWS WAF & Shield** service.
2. Select **Web ACLs** in the left menu -> Select **Global (CloudFront)**.
3. Click on the `WebsiteProtectionACL` Web ACL created in section 5.6.2.
4. Switch to the **Logging and metrics** tab.
5. In the **Logging** section, click **Enable**.

![Enable Logging on Web ACL](/images/Workshop/5.6-WAF/5.6.3-enable-waf-logging.png)

---

### Step 2: Associate with CloudWatch Log Group

1. **Logging destination:** Select **CloudWatch Logs log group**.
2. **CloudWatch Logs log group:** Select the `aws-waf-logs-cloudfront` Log Group created in Section 1.
3. **Redacted fields (Optional):** Keep the default (do not redact any fields) or specify sensitive fields to redact if required.
4. **Filter logs (Optional):** Keep the default settings to capture all traffic (All traffic).
5. Click **Save**.

![Link WAF to CloudWatch Log Group](/images/Workshop/5.6-WAF/5.6.3-link-log-destination.png)
![Link WAF to CloudWatch Log Group](/images/Workshop/5.6-WAF/5.6.3-link-log-destination2.png)

---

## 3. Verify Captured Log Data

### Step 1: Generate Web Traffic

1. Open a browser and navigate to the CloudFront Distribution domain name (obtained in section 5.5.1) or send HTTP requests to generate live web traffic.

---

### Step 2: Inspect Log Streams in CloudWatch

1. Return to the **CloudWatch** service -> **Log groups** -> select `aws-waf-logs-cloudfront`.
2. Under the **Log streams** tab, verify that new Log Streams containing WAF access records in JSON format appear.

![Verify WAF Log Streams](/images/Workshop/5.6-WAF/5.6.3-verify-log-streams.png)

---

## 4. Expected Outcomes

Upon completing this practical exercise:

- A **CloudWatch Log Group** named `aws-waf-logs-cloudfront` is successfully created in the `us-east-1` Region.
- **WAF Access Logging** is active on the `WebsiteProtectionACL` Web ACL.
- All HTTP/HTTPS traffic sent to CloudFront is automatically logged in JSON format inside CloudWatch Log Streams, serving as the data source for CloudWatch Alarms and Lambda automation in sections 5.7 & 5.8.
