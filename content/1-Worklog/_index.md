---
title: "Worklog"
date: 2026-07-18
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

<!-- # Worklog -->

This section documents the entire **8-week** internship journey, structured following a systematic learning and hands-on implementation path — from building a solid foundation in core AWS services and enhancing cloud infrastructure security to designing, deploying, and evaluating a fully automated cybersecurity incident response solution.

During the **first 5 weeks**, the focus was on in-depth research into AWS network architecture and security, covering critical services such as IAM, VPC, Transit Gateway, Hybrid DNS, Site-to-Site VPN, S3 Cross-Region Replication (CRR), AWS Security Hub, data governance, and process automation using AWS Lambda. This period established the foundational infrastructure knowledge essential for the core internship project.

In **Weeks 6 and 7**, I expanded infrastructure capabilities into high-availability architecture and application containerization. I successfully implemented Application Load Balancers (ALB) paired with Auto Scaling Groups (ASG) for seamless fault tolerance, while mastering Docker fundamentals (Dockerfiles, Amazon ECR) and serverless container orchestration using Amazon ECS Fargate.

In **Week 8**, I successfully completed and validated the Capstone Project: **"Automated Monitoring, Detection, and Incident Response System for SSH Brute-Force Attacks on EC2 Instances"**. Built by integrating cloud-native services including **Amazon EC2, Amazon CloudWatch Logs & Metric Filters, CloudWatch Alarms, AWS Lambda (Python/boto3), AWS DynamoDB**, and **Amazon SNS**, the solution automatically analyzes system authentication logs, detects malicious SSH login patterns, instantly triggers a Lambda function to isolate offending IP addresses via Security Groups/NACLs, and dispatches real-time security alerts to system administrators.

The detailed log for each week is structured as follows:

**Week 1:** [AWS Overview: Account Setup, IAM Security & AWS Budgets Management](1.1-week1/)

**Week 2:** [AWS Core Services: Deep Dive into IAM, Amazon S3 Storage & EC2 Compute](1.2-week2/)

**Week 3:** [AWS Networking: Custom VPC, Subnets, Gateways & Dual-Layer Security (SG/NACLs)](1.3-week3/)

**Week 4:** [Serverless Architecture: AWS Lambda, Boto3 SDK & Automated EC2 Scheduling](1.4-week4/)

**Week 5:** [AWS Observability: CloudWatch Monitoring, SNS Alerts & CloudTrail Auditing](1.5-week5/)

**Week 6:** [High Availability: Elastic Load Balancing (ALB) & Auto Scaling Groups (ASG)](1.6-week6/)

**Week 7:** [Containerization: Docker Basics, Amazon ECR & AWS ECS Fargate Deployment](1.7-week7/)

**Week 8:** [Capstone Project: Automated SSH Attack Monitoring, Isolation & Response on EC2](1.8-week8/)