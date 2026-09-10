---
title: "Create WAF IP Set"
date: 2026-08-24
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

This article provides detailed instructions on creating a WAF IP Set using the AWS Management Console. This IP Set acts as a container for offending IP addresses, ready to be linked with a Web ACL and enabling AWS Lambda to automatically update the blocklist.

---

## 1. Create a WAF IP Set

### Step 1: Access the AWS WAF Service

1. Log in to the **AWS Management Console**.
2. In the search bar, type `WAF` and select the **AWS WAF & Shield** service.
3. In the left navigation menu, select **IP sets**.

![Access WAF IP Sets](/images/Workshop/5.6-WAF/5.6.1-waf-ip-sets-menu.png)

---

### Step 2: Configure IP Set Parameters

1. Under the **Region** dropdown, select **Global (CloudFront)**.
2. Click the **Create IP set** button.
3. Enter the following details:
   - **IP set name:** `AutoBlockedIPSetV6`
   - **Description:** `IP Set containing IP addresses automatically blocked by Lambda`
   - **Region:** Keep the default `Global (CloudFront)`
   - **IP version:** Select **IPv6**.
   - **IP addresses:** Leave empty (do not enter any IP addresses) as this list will be automatically populated by AWS Lambda upon detecting violations.

![Configure IP Set Name and Region](/images/Workshop/5.6-WAF/5.6.1-configure-ip-set.png)

---

### Step 3: Complete IP Set Creation

1. Scroll to the bottom of the page and click **Create IP set**.

![Click Create IP Set](/images/Workshop/5.6-WAF/5.6.1-finish-create-ip-set.png)

---

## 2. Verify WAF IP Set List

Upon successful creation, the IP sets list will display `AutoBlockedIPSetV6` with the following properties:

- **Region:** Global (CloudFront)
- **IP version:** IPv6
- **Capacity:** 1

![Verify IP Set List](/images/Workshop/5.6-WAF/5.6.1-verify-ip-set.png)

---

## 3. Expected Outcomes

Upon completing this practical exercise:

- A **WAF IP Set** named `AutoBlockedIPSetV6` is successfully created under the `Global (CloudFront)` scope using the **IPv6** addressing format.
- The initial IP list is kept empty, prepared for association with the Web ACL in section 5.6.2 and for automated IP blocking via the Lambda function in section 5.8.
