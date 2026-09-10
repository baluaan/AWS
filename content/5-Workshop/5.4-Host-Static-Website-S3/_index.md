---
title: "Static Website Hosting on Amazon S3"
date: 2026-08-24
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Objectives

Build the Origin Storage layer for the static website using Amazon S3, ensuring secure source code storage and adherence to core security principles before serving content through a CDN distribution layer.

---

## 1. Overview

The Origin Layer serves as the central repository for all static application assets, including HTML source code, stylesheets, scripts, and media files. In this project, **Amazon S3 (Simple Storage Service)** is selected as the Origin Server due to its high availability, exceptional scalability, and cost-effective operational model.

The implementation architecture applies an enhanced security standard: **Enabling Block All Public Access** on the S3 Bucket. This ensures that objects within the bucket cannot be publicly accessed directly over the Internet via default S3 URLs (`s3.amazonaws.com`), forcing all incoming user requests to pass through the **Amazon CloudFront** CDN distribution layer configured in subsequent chapters.

---

## 2. Deployment Workflow

The process of setting up the Origin Storage layer on Amazon S3 is divided into two primary steps:

- **Step 1:** Create a dedicated S3 Bucket in the `us-east-1` Region with full public access blocking enabled.
- **Step 2:** Upload the static website source code (`portfolio`) into the Bucket.

Detailed console procedures and step-by-step instructions with screenshots are covered in the next section: **5.4.1. Create an S3 Bucket**.

---

## 3. Practical Exercises

Complete the following practical exercise in sequence:

- **[5.4.1. Create an S3 Bucket](5.4.1-Create-S3-bucket-details)**

---

## 4. Expected Outcomes

Upon completing this chapter, you will achieve the following:

- An **Amazon S3 Bucket** successfully created in the `us-east-1 (N. Virginia)` Region.
- **Block All Public Access** settings strictly enforced on the S3 Bucket.
- Static website source code (`portfolio`) securely uploaded and stored in the Bucket.
- An Origin Storage layer fully prepared for integration with Amazon CloudFront and AWS WAF in subsequent steps.
