---
title: "Distribute Content via Amazon CloudFront"
date: 2026-08-24
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Objectives

Build a Content Delivery Network (CDN) using Amazon CloudFront to accelerate website loading speeds for global users and configure Origin Access Control (OAC) security to safeguard the underlying S3 Bucket.

---

## 1. Overview

The Content Delivery Network (CDN Layer) serves as the primary entry point for Internet traffic. In this project, **Amazon CloudFront** is deployed to improve load performance through its global network of Edge Locations and act as the frontline security boundary.

Specifically, integrating **Origin Access Control (OAC)** enables CloudFront to sign requests directed to Amazon S3. Consequently, the S3 Bucket denies direct Internet access while exclusively accepting authorized requests routed through CloudFront.

---

## 2. Deployment Workflow

The process of configuring the CloudFront distribution network consists of two main steps:

- **Step 1:** Create a CloudFront Distribution connected to the S3 Origin, enable OAC, and define the Default Root Object (`index.html`).
- **Step 2:** Update the S3 Bucket Policy to grant exclusive access permissions to the newly created CloudFront Distribution.

Detailed procedures on the AWS Management Console with step-by-step screenshots are presented in the following lesson: **5.5.1. Create a CloudFront Distribution**.

---

## 3. Practical Exercises

Complete the following practical exercise in sequence:

- **[5.5.1. Create a CloudFront Distribution](5.5.1-Create-CloudFront-distribution-details)**

---

## 4. Expected Outcomes

Upon completing this chapter, you will achieve the following:

- An **Amazon CloudFront Distribution** successfully created and linked to the S3 Bucket.
- **Origin Access Control (OAC)** configured with accurate access permissions applied to the S3 Bucket Policy.
- The static website safely accessible via the CloudFront domain name (`dxxxxxxxxxxxx.cloudfront.net`).
- The underlying S3 Bucket fully protected against direct access attempts bypassing CloudFront.
