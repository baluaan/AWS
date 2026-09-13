---
title: "Worklog - Week 7"
date: 2026-09-13
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:

- Introduction to Application Containerization and fundamental Docker concepts (Dockerfile, Images, Containers).
- Explore AWS Container Orchestration service - Amazon Elastic Container Service (AWS ECS).
- Compare ECS launch types: EC2 Launch Type vs. AWS Fargate (Serverless compute engine for containers).
- Hands-on deployment of a containerized application onto AWS ECS Fargate.

### Tasks to be implemented this week:

| Day | Task Description | Start Date | End Date | Resource / Documentation |
| --- | --- | --- | --- | --- |
| Mon | - Study Docker fundamentals: Containerization vs. Virtualization, Docker Engine architecture.<br>- Write a basic Dockerfile, build a container image (`docker build`), and execute container locally (`docker run`). | 09/14/2026 | 09/14/2026 | https://cloudjourney.awsstudygroup.com/ |
| Tue | - Study Amazon Elastic Container Registry (ECR): Private Docker image repositories on AWS.<br>- Create ECR Repository, authenticate Docker CLI with ECR, tag images, and push container images to ECR. | 09/15/2026 | 09/15/2026 | https://cloudjourney.awsstudygroup.com/ |
| Wed | - Study AWS ECS Architecture: ECS Clusters, Task Definitions, Tasks, and Services.<br>- Differentiate between ECS EC2 Launch Type vs Serverless AWS Fargate Launch Type. | 09/16/2026 | 09/16/2026 | https://cloudjourney.awsstudygroup.com/ |
| Thu | - Provision an AWS ECS Cluster.<br>- Draft an ECS Task Definition linking the ECR container image, setting vCPU/Memory specifications and port mappings.<br>- Deploy an ECS Service running Tasks on AWS Fargate. | 09/17/2026 | 09/17/2026 | https://cloudjourney.awsstudygroup.com/ |
| Fri | - Integrate the ECS Service with an Application Load Balancer (ALB) for dynamic request routing.<br>- Verify web access via ALB DNS, audit container status, and teardown ECS/ECR test assets. | 09/18/2026 | 09/18/2026 | https://cloudjourney.awsstudygroup.com/ |

### Key Achievements in Week 7:

- Understood core containerization principles and mastered essential Docker CLI commands.
- Successfully published container images securely using Amazon Elastic Container Registry (ECR).
- Mastered Amazon ECS core architecture (Task Definitions, Services, and Serverless Clusters).
- Successfully deployed a scalable containerized web application on AWS ECS Fargate integrated with an Application Load Balancer (ALB).