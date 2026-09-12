---
title: "Launch Amazon EC2 Ubuntu Server"
date: 2026-09-11
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Objective

Build the target server infrastructure (Target Host) for the system using the Amazon EC2 service running the Ubuntu Server operating system. This server acts as an SSH server subject to automated monitoring and protection against password guessing (Brute Force) attacks.

---

## 1. Overview

The target server is where the SSH service (`sshd`) operates and receives connections from legitimate users as well as invalid scanning attempts from attackers. In this project, **Amazon EC2 (Elastic Compute Cloud)** running the **Ubuntu Server** operating system was selected as the protected server due to its flexibility, popularity, and deep integration capabilities with AWS monitoring services[cite: 1].

The deployment model applies security standards and centralized monitoring:
- The EC2 server is attached with an **IAM Role** integrated with the "CloudWatchAgentServerPolicy" policy, allowing system logs to be safely pushed to CloudWatch[cite: 1].
- All SSH login logs are directly recorded in the system file "/var/log/auth.log"[cite: 1].
- **CloudWatch Agent** installed on the EC2 instance acts as a data collector (Log Collector), pushing log data in real time to the CloudWatch Logs Group for automated analysis in subsequent chapters[cite: 1].

---

## 2. Deployment Process

1. Initialize the VPC according to the following steps:

**Step 1:** Access **VPC** > **Your VPCs** and select **Create VPC**
- Configure as follows:
  - Choose: VPC and more
  - Name: project-AWS-vpc
  - Select: Create VPC
![Initialize VPC](/static/images/5/5.4.1.png)

**Step 2:**
2. The procedure for launching the Ubuntu EC2 machine is divided into the following steps:

**Step 1:** Launch EC2:
- Navigate to **EC2** > **Instances** and select **Launch Instances**
- Configure the following parameters:
  - Name: AWS-Security-Monitoring
  - AMI: Ubuntu Server 26.04 LTS
  - Instance type: t3.micro
  - Key pair: unbutu.pem
  - Network: select **edit**, then select the newly created VPC and choose a public subnet
  - Select: **Launch instance**
![Launch EC2](/static/images/5/5.4.2.png)
![Continue EC2 Launch](/static/images/5/5.4.3.png)

**Step 2:** Configure the newly launched EC2 instance
- Execute the following commands:
  - sudo apt update
  - sudo apt install -y wget
  - wget https://amazoncloudwatch-agent.s3.amazonaws.com/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
  - sudo dpkg -i -E ./amazon-cloudwatch-agent.deb
![Download CloudWatch Environment](/static/images/5/5.4.4.png)
  - sudo nano /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.d/file_amazon-cloudwatch-agent.json
  
  Then paste the following configuration code into the file:

```json
{
  "agent": {
    "metrics_collection_interval": 60
  },
  "metrics": {
    "metrics_collected": {
      "cpu": {
        "measurement": [
          "cpu_usage_idle",
          "cpu_usage_user",
          "cpu_usage_system"
        ],
        "metrics_collection_interval": 60
      },
      "disk": {
        "measurement": [
          "used_percent"
        ],
        "resources": [
          "/"
        ],
        "metrics_collection_interval": 60
      },
      "mem": {
        "measurement": [
          "mem_used_percent"
        ],
        "metrics_collection_interval": 60
      }
    }
  },
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/auth.log",
            "log_group_name": "/aws/ec2/security/auth",
            "log_stream_name": "{instance_id}"
          },
          {
            "file_path": "/var/log/syslog",
            "log_group_name": "/aws/ec2/system/syslog",
            "log_stream_name": "{instance_id}"
          }
        ]
      }
    }
  }
}
```
![Login Monitoring Configuration](static/images/5/5.4.5.png)

- Use the following command to run the newly created file:
```json 
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
-a fetch-config \
-m ec2 \
-c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.d/file_amazon-cloudwatch-agent.json \
-s
```
---

## 3. Expected Results

After completing this chapter, you will achieve:

- An **EC2 Ubuntu** instance successfully created in Region `us-east-1a`.
- **EC2 CloudWatch** configured on the monitored instance.
- Successful setup of the monitoring server to facilitate tasks in subsequent chapters.