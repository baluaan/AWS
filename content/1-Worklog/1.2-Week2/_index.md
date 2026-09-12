---
title: "Worklog - Week 2"
date: 2026-08-10
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:

- Deep dive into AWS Core Services: IAM, S3, and EC2.
- Master identity and access management mechanisms (Users, Groups, Roles, JSON Policies).
- Understand object storage concepts with Amazon S3 (Buckets, Storage Classes, Bucket Policies, Versioning).
- Master Virtual Cloud Servers with Amazon EC2 (Instance Types, Key Pairs, Security Groups, EBS) and deploy a simple Web Server.

### Tasks to be implemented this week:

| Day | Task Description | Start Date | End Date | Resource / Documentation |
| --- | --- | --- | --- | --- |
| Mon | - Advanced IAM Study: Differentiate between Users, Groups, Roles, and Inline/Managed Policies (JSON syntax).<br>- Practice creating IAM users, granting restricted permissions, setting up AWS Account Aliases, and testing login flow. | 08/10/2026 | 08/10/2026 | https://cloudjourney.awsstudygroup.com/ |
| Tue | - Study Amazon S3 object storage: Bucket naming conventions, Storage Classes (Standard, Intelligent-Tiering, Glacier).<br>- Practice creating S3 Buckets, uploading objects, configuring Public Access Block, and applying Bucket Policies. | 08/11/2026 | 08/11/2026 | https://cloudjourney.awsstudygroup.com/ |
| Wed | - Study Amazon EC2: Instance families (t2.micro/t3.micro), AMIs, Key Pairs, and Elastic Block Store (EBS).<br>- Launch a virtual machine running Amazon Linux 2 / Ubuntu. | 08/12/2026 | 08/12/2026 | https://cloudjourney.awsstudygroup.com/ |
| Thu | - Configure Security Groups for EC2: Allow inbound HTTP (80), HTTPS (443), and SSH (22).<br>- SSH into the EC2 instance via SSH CLI/PuTTY using SSH Key Pairs.<br>- Install Apache/Nginx web server on EC2 to serve a test web page. | 08/13/2026 | 08/13/2026 | https://cloudjourney.awsstudygroup.com/ |
| Fri | - Configure Amazon S3 Static Website Hosting integrated with EC2 resources.<br>- Complete Lab review on AWS Core Services and terminate unused EC2/S3 resources. | 08/14/2026 | 08/14/2026 | https://cloudjourney.awsstudygroup.com/ |

### Key Achievements in Week 2:

- Gained hands-on experience in IAM permission management: Successfully created users, custom IAM Roles, and JSON policy structures.
- Mastered Amazon S3 configuration: Successfully hosted static content, configured bucket policies, and understood storage lifecycle rules.
- Successfully provisioned EC2 Instances: Selected proper AMIs, generated SSH Key Pairs, and secured instances using Security Groups.
- Successfully completed hands-on lab: Deployed an operational Nginx/Apache Web Server on EC2 accessible via Public IP.