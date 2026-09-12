---
title: "Worklog - Week 6"
date: 2026-09-07
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

- Explore High Availability architecture using Elastic Load Balancer (ELB) and Auto Scaling Groups (ASG).
- Differentiate between load balancer types: Application Load Balancer (ALB) vs. Network Load Balancer (NLB).
- Configure ALB to distribute incoming web traffic across EC2 instances in multiple Availability Zones (AZs).
- Implement Auto Scaling Groups linked with Target Groups to automatically adjust compute capacity based on system load.

### Tasks to be implemented this week:

| Day | Task Description | Start Date | End Date | Resource / Documentation |
| --- | --- | --- | --- | --- |
| Mon | - Overview of Elastic Load Balancing (ELB): Core mechanics, Target Groups, and Health Checks.<br>- Compare Application Load Balancer (Layer 7 HTTP/HTTPS) vs. Network Load Balancer (Layer 4 TCP/UDP). | 09/07/2026 | 09/07/2026 | https://cloudjourney.awsstudygroup.com/ |
| Tue | - Launch 2 EC2 web instances across 2 distinct Subnets/AZs.<br>- Provision an Internet-facing ALB, configure Target Group, and register instances.<br>- Test load distribution via ALB DNS endpoint. | 09/08/2026 | 09/08/2026 | https://cloudjourney.awsstudygroup.com/ |
| Wed | - Study AWS Auto Scaling Groups (ASG): Launch Templates, Desired/Min/Max capacities.<br>- Create an EC2 Launch Template with custom User Data bootstrapping web server setup. | 09/09/2026 | 09/09/2026 | https://cloudjourney.awsstudygroup.com/ |
| Thu | - Provision an Auto Scaling Group associated with the Launch Template and ALB Target Group.<br>- Configure Target Tracking Dynamic Scaling Policy (maintain target CPU usage at 50%). | 09/10/2026 | 09/10/2026 | https://cloudjourney.awsstudygroup.com/ |
| Fri | - Execute Load Stress Test: Simulate high CPU load using tools (`stress` utility) to verify automatic scale-out triggering.<br>- Observe automatic scale-in behavior when load drops and clean up lab resources. | 09/11/2026 | 09/11/2026 | https://cloudjourney.awsstudygroup.com/ |

### Key Achievements in Week 6:

- Mastered High Availability (HA) and Fault Tolerance concepts using Elastic Load Balancing across multiple Availability Zones.
- Configured a fully operational Application Load Balancer (ALB) with customized Health Checks.
- Created Launch Templates and configured dynamic Auto Scaling policies.
- Successfully verified automated scaling: ASG automatically launched new instances under heavy traffic and terminated excess capacity when idle.