---
title: "Create an S3 Bucket"
date: 2026-08-24
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

This article provides detailed, step-by-step instructions on creating an Amazon S3 Bucket to serve as an Origin Server for static website hosting and uploading application source code to S3.

---

## 1. Create an Amazon S3 Bucket

### Step 1: Access the Amazon S3 Service

1. Log in to the **AWS Management Console**.
2. In the search bar, type `S3` and select the **S3** service.
3. On the S3 console interface, ensure the correct AWS Region is selected (e.g., `us-east-1` - US East N. Virginia).
4. Click the **Create bucket** button.

![Access S3 Console](/images/Workshop/5.4-S3/5.4.1-s3-console.png)

---

### Step 2: Configure General Configuration

1. **Bucket name:** Enter a globally unique name across AWS (e.g., `tung-static-website-2026` or `portfolio-bucket-2026`).
2. **AWS Region:** Select **US East (N. Virginia) us-east-1**.
3. **Object Ownership:** Keep the default **ACLs disabled (recommended)** setting to optimize access control management.

![Configure General Configuration](/images/Workshop/5.4-S3/5.4.1-general-config.png)

---

### Step 3: Configure Access Settings (Block Public Access)

1. Under the **Block Public Access settings for this bucket** section:
   - Check **Block _all_ public access**.
2. _Reason:_ We will not expose the S3 Bucket directly to the public Internet. All user traffic will be routed through the CloudFront CDN using Origin Access Control (OAC) in subsequent steps.

![Enable Block All Public Access](/images/Workshop/5.4-S3/5.4.1-block-public-access.png)

---

### Step 4: Complete Bucket Creation

1. Leave all remaining default settings unchanged (Bucket Versioning, Encryption).
2. Scroll to the bottom of the page and click **Create bucket**.

![Click Create Bucket](/images/Workshop/5.4-S3/5.4.1-create-bucket-finish.png)

---

## 2. Upload Static Website Source Code to S3 Bucket

### Step 1: Open the Created Bucket

1. In the **Buckets** list, click on the name of the newly created S3 Bucket (e.g., `tung-static-website-2026`).

![Select S3 Bucket](/images/Workshop/5.4-S3/5.4.1-select-bucket.png)

---

### Step 2: Upload Files

1. On the **Objects** tab, click the **Upload** button.
2. Click **Add files** or **Add folder** to select all website source code files from the local `portfolio` directory prepared in step 5.3 (including `index.html`, CSS files, JS files, images, etc.).
3. Scroll to the bottom of the page and click **Upload**.

![Add and Upload Files to S3](/images/Workshop/5.4-S3/5.4.1-upload-files.png)

---

### Step 3: Upload Completion

1. Wait until the upload process completes and displays a green **Upload succeeded** notification banner.
2. Click **Close** to return to the object list inside the Bucket.

![Upload Succeeded](/images/Workshop/5.4-S3/5.4.1-upload-success.png)

---

## 3. Verify S3 Resource Directory

Verify the list of objects inside the bucket to ensure that the primary `index.html` file resides at the root level of the S3 Bucket:

![Check File List](/images/Workshop/5.4-S3/5.4.1-bucket-objects-list.png)

---

## 4. Expected Outcomes

Upon completing this practical exercise:

- The S3 Bucket is successfully created in the `us-east-1` Region.
- Direct public access is completely blocked (**Block all public access**).
- The `index.html` file and all `portfolio` website assets are ready inside the S3 Bucket for integration with CloudFront OAC.
