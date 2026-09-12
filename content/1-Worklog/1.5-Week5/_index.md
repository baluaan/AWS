---
title: "Worklog - Week 5"
date: 2026-08-31
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:

- Study Amazon CloudWatch (Resource & Application Monitoring) and AWS CloudTrail (API Auditing & Event Logging).
- Understand core CloudWatch building blocks: Metrics, Logs, Alarms, and Dashboards.
- Build an automated monitoring & alert pipeline for metric threshold breaches (High CPU, Memory, Disk).
- Practice forensic investigation and API action auditing using CloudTrail event histories.

### Tasks to be implemented this week:

| Day | Task Description | Start Date | End Date | Resource / Documentation |
| --- | --- | --- | --- | --- |
| Mon | - Study Amazon CloudWatch Metrics and Logs: Collecting performance telemetry from EC2, S3, and Lambda.<br>- Learn CloudWatch Agent installation for OS-level metrics (RAM/Disk usage) and custom logs. | 08/31/2026 | 08/31/2026 | https://cloudjourney.awsstudygroup.com/ |
| Tue | - Configure CloudWatch Alarms: Define warning thresholds when EC2 CPU utilization exceeds 80%.<br>- Integrate Amazon SNS (Simple Notification Service) to dispatch automated email alerts on ALARM trigger. | 09/01/2026 | 09/01/2026 | https://cloudjourney.awsstudygroup.com/ |
| Wed | - Build customized CloudWatch Dashboards: Add visualization widgets monitoring EC2 CPU, Network I/O, and Storage performance. | 09/02/2026 | 09/02/2026 | https://cloudjourney.awsstudygroup.com/ |
| Thu | - Study AWS CloudTrail: Understanding Management Events, Data Events, and Trail configurations.<br>- Create a multi-region CloudTrail logging API activity centrally into an Amazon S3 bucket. | 09/03/2026 | 09/03/2026 | https://cloudjourney.awsstudygroup.com/ |
| Fri | - Forensic auditing via CloudTrail Event History: Search and identify who deleted resources (e.g., S3 Buckets or terminated EC2 Instances).<br>- Review week 5 progress on AWS Observability. | 09/04/2026 | 09/04/2026 | https://cloudjourney.awsstudygroup.com/ |

### Key Achievements in Week 5:

- Understood the core principles of Cloud Observability, monitoring operational health via Amazon CloudWatch.
- Successfully built an automated alarm system sending immediate email alerts via SNS upon server overload.
- Custom-designed centralized CloudWatch Dashboards for operational visibility across compute instances.
- Mastered audit logging with AWS CloudTrail to track account activity, user attribution, and security compliance.