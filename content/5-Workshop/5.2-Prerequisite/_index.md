---
title: "Prerequisites"
date: 2026-08-24
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

### Objective

Ensure you have full access to the AWS Management Console, a ready local testing environment, and the necessary source code resources prepared before deploying the automated protection system.

---

## 1. Tools and Resources to Prepare

This workshop is carried out mainly through the **AWS Management Console (Web UI)**, combined with a local workstation environment for hands-on testing. You will need to prepare the following:

- **AWS Account:** Must have permissions to create and manage AWS WAF, Lambda, S3, CloudFront, CloudWatch, SNS, and IAM (`AdministratorAccess` is recommended).
- **Web Browser:** The latest version of Google Chrome, Microsoft Edge, or Mozilla Firefox.
- **CLI Testing Tool (Terminal / PowerShell):** Used to run commands that simulate traffic/attacks (HTTP Flood).
  - On Windows: **PowerShell** (comes with `Invoke-WebRequest`).
  - On macOS/Linux: **Terminal** (comes with `curl`).
- **Code Editor:** Visual Studio Code (or Notepad++) for viewing/editing the Lambda source code (Python 3.12) and the static website interface (`index.html`).
- **Email Address (Gmail):** Used to subscribe to incident alert emails from the Amazon SNS Topic.

---

## 2. Detailed Preparation Steps

### Step 1: Sign in to the AWS Console and Verify the Region

1. Sign in to the [AWS Management Console](https://aws.amazon.com/console/).
2. Make sure the selected working Region is **US East (N. Virginia) — us-east-1**, shown in the top-right corner of the Console.

> ⚠️ **IMPORTANT NOTE:** AWS WAF for Amazon CloudFront (Global) and its dependent resources must be created and managed in the **us-east-1 (N. Virginia)** Region.

**Checkpoint:** The Region name in the toolbar correctly displays **US East (N. Virginia) us-east-1**.

---

### Step 2: Verify Local Command-Line Tools

Open Terminal (macOS/Linux) or PowerShell (Windows) and check that the HTTP request testing command is available:

**On Windows (PowerShell):**

```powershell
Get-Command Invoke-WebRequest
```

**On macOS/Linux (Terminal):**

```bash
curl --version
```

**Checkpoint:** The commands return valid tool information and are ready to send HTTP/HTTPS requests.

---

### Step 3: Prepare the Application and Lambda Source Files

Download or create a local project folder containing the following files:

- A static website interface file (`index.html`) to be hosted on Amazon S3.
- A Python source file (`lambda_function.py`) that automates extracting offending IPs and calling the AWS WAF API.

**Checkpoint:** The website source code and the Lambda script are ready on your computer to be uploaded to AWS in the next steps.

---

## 3. Expected Outcomes

After completing this chapter, you should have:

- Successfully signed in to the AWS Management Console in the **us-east-1 (N. Virginia)** Region.
- A local workstation environment with PowerShell/Terminal ready for attack simulation testing.
- An email address ready to receive alert notifications from SNS.
- The static website source code and the Python Lambda function ready for deployment.
