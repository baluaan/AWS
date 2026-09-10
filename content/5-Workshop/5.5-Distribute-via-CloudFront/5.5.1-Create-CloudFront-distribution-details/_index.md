---
title: "Create a CloudFront Distribution"
date: 2026-08-24
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

This article provides detailed, step-by-step instructions on creating an Amazon CloudFront Distribution to deliver a static website from an S3 Bucket, configuring Origin Access Control (OAC) security, and updating the S3 Bucket Policy.

---

## 1. Create an Amazon CloudFront Distribution

### Step 1: Access the Amazon CloudFront Service

1. Log in to the **AWS Management Console**.
2. In the search bar, type `CloudFront` and select the **CloudFront** service.
3. On the CloudFront Dashboard interface, click **Create distribution**.

![Access CloudFront Console](/images/Workshop/5.5-CloudFront/5.5.1-cloudfront-console.png)

---

### Step 2: Configure Origin Settings & Origin Access Control (OAC)

1. **Origin domain:** Select the S3 Bucket created in step 5.4 (e.g., `tung-static-website-2026.s3.us-east-1.amazonaws.com`).
2. **Name:** Keep the default suggested name.
3. **Origin access:** Select **Origin access control settings (recommended)**.
4. Click **Create new OAC** (if an OAC is not already available) -> Keep the default name -> Click **Create**.

![Configure Origin and OAC](/images/Workshop/5.5-CloudFront/5.5.1-origin-oac-config.png)

---

### Step 3: Configure Default Cache Behavior

1. **Viewer protocol policy:** Select **Redirect HTTP to HTTPS** to enforce encryption for all traffic.
2. **Allowed HTTP methods:** Keep the default **GET, HEAD**.
3. **Cache key and origin requests:** Keep the default **CachingOptimized**.

![Configure Default Cache Behavior](/images/Workshop/5.5-CloudFront/5.5.1-cache-behavior.png)

---

### Step 4: Configure Web Application Firewall (WAF) & Settings

1. **Web Application Firewall (WAF):** Select **Do not enable security protections** for now (AWS WAF integration will be covered in detail in step 5.6).
2. **Default root object:** Enter `index.html`.
3. Scroll to the bottom of the page and click **Create distribution**.

![Configure Root Object and Click Create](/images/Workshop/5.5-CloudFront/5.5.1-create-distribution.png)
![Configure Root Object and Click Create](/images/Workshop/5.5-CloudFront/5.5.1-create-distribution2.png)
![Configure Root Object and Click Create](/images/Workshop/5.5-CloudFront/5.5.1-create-distribution3.png)

---

## 2. Update S3 Bucket Policy with OAC

After successfully creating the CloudFront Distribution, a blue banner notification will appear requiring an update to the S3 Bucket Policy to grant permissions to the OAC.

### Step 1: Copy Bucket Policy from CloudFront

1. On the details page of the newly created CloudFront Distribution, click the **Copy policy** button in the blue notification box.

![Copy S3 Bucket Policy](/images/Workshop/5.5-CloudFront/5.5.1-copy-bucket-policy.png)

---

### Step 2: Paste Policy into Amazon S3 Bucket

1. Return to the **Amazon S3** service and select your S3 Bucket.
2. Switch to the **Permissions** tab.
3. Under the **Bucket policy** section, click **Edit**.
4. Paste the copied Policy JSON from CloudFront into the editor box.
5. Click **Save changes**.

![Update S3 Bucket Policy](/images/Workshop/5.5-CloudFront/5.5.1-update-s3-policy.png)

![Update S3 Bucket Policy](/images/Workshop/5.5-CloudFront/5.5.1-update-s3-policy2.png)

---

## 3. Verify Content Delivery (Distribution Domain Name)

### Step 1: Obtain CloudFront Domain Name

1. Return to the **CloudFront Distributions** interface.
2. Find the **Domain name** column or copy the **Distribution domain name** string on the details page (e.g., `dxd2hh9ocuujg.cloudfront.net`).

![Get CloudFront Domain Name](/images/Workshop/5.5-CloudFront/5.5.1-get-domain-name.png)

---

### Step 2: Access the Test Website

1. Wait until the **Last modified** status of the Distribution changes from `Deploying` to a completed timestamp.
2. Open a new browser tab and navigate to: `https://dxd2hh9ocuujg.cloudfront.net/`

![Test CloudFront Domain Access](/images/Workshop/5.5-CloudFront/5.5.1-test-website-access.png)

---

## 4. Expected Outcomes

Upon completing this practical exercise:

- The **CloudFront Distribution** is successfully created and correctly linked to the S3 Origin.
- The **S3 Bucket Policy** is updated with OAC permissions, completely blocking direct S3 URL access while exclusively accepting requests from CloudFront.
- The `portfolio` website interface loads smoothly and securely over HTTPS using the CloudFront Domain Name.
