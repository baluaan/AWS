---
title: "Create and Configure Web ACL"
date: 2026-08-24
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

This article provides detailed instructions on creating a new Web Access Control List (Web ACL) in AWS WAF, configuring security rules (Rate-based rule and IP Set rule), and associating it directly with the Amazon CloudFront Distribution to protect the system.

---

## 1. Create a Web ACL

### Step 1: Access Web ACL Creation

1. Log in to the **AWS Management Console**.
2. Navigate to the **AWS WAF & Shield** service.
3. In the left navigation menu, select **Web ACLs**.
4. Under the **Region** dropdown, select **Global (CloudFront)**.
5. Click the **Create web ACL** button.

![Access Web ACLs](/images/Workshop/5.6-WAF/5.6.2-web-acl-menu.png)

---

### Step 2: Configure General Details (Describe web ACL)

1. **Name:** `WebsiteProtectionACL`
2. **Description:** `Web ACL including Rate-based rule and IP Block rule protecting CloudFront`
3. **Resource type:** Keep default **CloudFront distributions**.
4. **Associated AWS resources:**
   - Click the **Add AWS resources** button.
   - Select **Amazon CloudFront distributions**.
   - Check the box next to the CloudFront Distribution created in section 5.5.
   - Click **Add**.
5. Click **Next**.

![Configure Web ACL Information](/images/Workshop/5.6-WAF/5.6.2-describe-web-acl.png)
![Configure Web ACL Information](/images/Workshop/5.6-WAF/5.6.2-describe-web-acl2.png)

---

## 2. Configure Rules and Actions

### Step 1: Add IP Set Rule (Block Offending IPs)

1. At the **Add rules and rule groups** step, click **Add rules** -> select **Add my own rules and rule groups**.
2. Configure the IP blocking rule:
   - **Rule type:** Select **IP set**.
   - **Name:** `BlockAutoIPSetRule`
   - **IP set:** Select the `AutoBlockedIPSetV6` IP Set created in section 5.6.1.
   - **Source IP location:** Select **Source IP address**.
   - **Action:** Select **Block**.
3. Click **Add rule**.

![Configure IP Set Rule](/images/Workshop/5.6-WAF/5.6.2-add-ip-set-rule.png)

---

### Step 2: Add Rate-based Rule (Traffic Rate Limiting)

1. Click **Add rules** again -> select **Add my own rules and rule groups**.
2. Configure the Rate limit rule:
   - **Rule type:** Select **Rate-based rule**.
   - **Name:** `HTTPRateLimitRule`
   - **Rate limit:** Enter the rate threshold, e.g., `100` (or `100` - `2000` depending on workshop requirements).
   - **Evaluation window:** Select **5 minutes** (or the default timeframe).
   - **Criteria to aggregate requests:** Select **IP address** -> **Source IP address**.
   - **Action:** Select **Block**.
3. Click **Add rule**.

![Configure Rate-based Rule](/images/Workshop/5.6-WAF/5.6.2-add-rate-rule.png)

---

### Step 3: Configure Default Web ACL Action

1. Under the **Default action** section, select **Allow** (Allows all normal traffic that does not trigger any rules).
2. Click **Next**.

---

## 3. Complete Web ACL Configuration

### Step 1: Set Rule Priority

1. Keep the rule evaluation order intact (ensure `BlockAutoIPSetRule` is evaluated prior to or logically ordered alongside `HTTPRateLimitRule`).
2. Click **Next**.

---

### Step 2: Review and Create Web ACL

1. Review all configured parameters.
2. Click **Create web ACL** at the bottom of the page.

![Complete Web ACL Creation](/images/Workshop/5.6-WAF/5.6.2-finish-web-acl.png)

---

## 4. Expected Outcomes

Upon completing this practical exercise:

- A **Web ACL** named `WebsiteProtectionACL` is successfully created under the `Global (CloudFront)` scope.
- The **IP Set Rule** (`BlockAutoIPSetRule`) is integrated and linked with `AutoBlockedIPSetV6`.
- The **Rate-based Rule** (`HTTPRateLimitRule`) is configured to monitor and limit request volume per IP.
- The Web ACL is successfully attached to the **CloudFront Distribution**, actively filtering and processing network traffic at the Edge.
