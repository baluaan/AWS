---
title: "Initialize Amazon EC2 Ubuntu Server"
date: 2026-08-24
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Objective

Build the target host infrastructure for the system using the Amazon EC2 service running the Ubuntu Server operating system, serving as an SSH server subject to automated monitoring and protection against password guessing attacks (Brute Force).

---

## 1. Overview

The target host is where the SSH service (`sshd`) runs and receives connections from users as well as unauthorized scanning attempts from attackers. In this project, **Amazon EC2 (Elastic Compute Cloud)** running the **Ubuntu Server** operating system is selected as the protected server due to its flexibility, popularity, and deep integration capabilities with AWS monitoring services.

The deployment model applies security standards and centralized monitoring:
- The EC2 instance is attached to an **IAM Role** integrated with the "CloudWatchAgentServerPolicy" policy, allowing secure pushing of system logs to CloudWatch.
- All SSH login logs are directly recorded in the system file "/var/log/auth.log".
- The **CloudWatch Agent** installed on the EC2 instance will act as a Log Collector, pushing log data in real-time to the CloudWatch Logs Group to serve automated analysis in the subsequent chapters.

---

## 2. Deployment Workflow

1. Initialize the VPC according to the following steps:
Access **VPC** > **Your VPCs** and select **Create VPC**
- Configure as follows:
  - Select: VPC and more
  - Name: project-AWS-vpc
  - Select: Create VPC
![Initialize VPC](/images/5/4/5.4.1.png)
2. Initialize the IAM role for EC2 according to the following steps:
Access **IAM** > **Roles** and select **Create Role**
- Configure as follows:
  - Select service: ec2
  - Permission: CloudWatchAgentServerpolicy
  - Select **Next** 
  - Name: EC2-CloudWatch-Agent-Role
![Initialize IAM](/images/5/4/5.4.8.png)
3. The process of initializing the Ubuntu EC2 instance is divided into the following steps:

**Step 1:** Initialize EC2:
- Access **EC2** > **Instances** and select **Launch Instances**
- Configure the parameters as follows:
  - Name: AWS-Security-Monitoring
  - AMI: Ubuntu Server 26.04 LTS
  - Instance type: t3.micro 
  - Key pair: unbutu.pem
  - Network: select **edit**, then select the newly created vpc and select the public subnet
  - Select: **Launch instance**
![Initialize EC2](/images/5/4/5.4.2.png)
![Continue initialization](/images/5/4/5.4.3.png)
**Step 2:** Configure the newly created EC2 instance
- Execute the following commands: 
  - sudo apt update
  - sudo apt install -y wget
  - wget https://amazoncloudwatch-agent.s3.amazonaws.com/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
  - sudo dpkg -i -E ./amazon-cloudwatch-agent.deb
![Download CloudWatch environment](/images/5/4/5.4.4.png)
  - sudo nano /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.d/file_amazon-cloudwatch-agent.json
  Then paste this block of code:

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
![Login Monitoring Configuration](static/images/5/4/5.4.5.png)

- Use the following command to run the newly created file:
```json 
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
-a fetch-config \
-m ec2 \
-c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.d/file_amazon-cloudwatch-agent.json \
-s
```
4. Attach IAM role to EC2 instance
Access the newly created EC2 instance, then select **Actions** > **Security** > **Modify IAM role**
![Open attach role interface](/images/5/4/5.4.6.png)
Select the newly created IAM role
![Select role](/images/5/4/5.4.7.png)

---

## 3. Expected Results

After completing this chapter, you will achieve:

- An **EC2 Ubuntu** instance successfully created in Region `us-east-1a`.
- **EC2 CloudWatch** configured on the monitored instance.
- Successful setup of the monitoring server to facilitate tasks in subsequent chapters.