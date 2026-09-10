---
title: "Week 6 - Worklog"
date: 2026-08-22
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

- Project Planning & Cybersecurity Analysis: Research common web attack scenarios (DDoS, HTTP Flood, Brute Force) and design an automated incident response architecture on AWS.

- Edge Protection Deployment: Configure Amazon CloudFront distribution as the content delivery layer and attach AWS WAF Web ACL to filter HTTP/HTTPS traffic.

- Filtering Rules & Blacklist Initialization: Define baseline AWS WAF Managed Rules and create an empty IP Set as a foundation for automated IP blocking in later phases.

- Centralized Logging & Monitoring Setup: Enable WAF Logging, route log streams to CloudWatch Logs, and monitor initial traffic patterns.

### Tasks to be carried out this week:

| Day | Task                                                                                                                                                                                        | Start Date | Completion Date | Reference Material                      |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- | --------------- | --------------------------------------- |
| 2   | - Project planning & architecture design.<br>- Analyze web application security challenges.<br>- Diagram the automated threat response architecture.                                        | 24/08/2026 | 24/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 3   | - Deploy edge distribution with CloudFront.<br>- Create Amazon CloudFront distribution.<br>- Point web application origin to CDN for traffic optimization and edge blocking.                | 25/08/2026 | 25/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 4   | - Configure AWS WAF firewall.<br>- Create AWS WAF Web ACL and associate it with CloudFront.<br>- Define standard AWS Managed Rule Groups.<br>- Initialize an empty IP Set (Blocked IP Set). | 26/08/2026 | 26/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 5   | - Configure centralized logging system.<br>- Enable WAF Logging feature.<br>- Route real-time access logs directly to Amazon CloudWatch Log Group.                                          | 27/08/2026 | 27/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 6   | - Traffic metrics evaluation.<br>- Analyze raw log structures collected in CloudWatch.<br>- Fine-tune edge security configurations.                                                         | 28/08/2026 | 28/08/2026      | https://cloudjourney.awsstudygroup.com/ |

### Week 6 Achievements:

- Completed Overall Architecture Design: Successfully analyzed web attack vectors and finalized the cloud-native automated incident response system architecture.

- Deployed Edge Protection Layer: Configured Amazon CloudFront as the frontend CDN, ensuring malicious traffic can be intercepted at edge locations.

- Provisioned AWS WAF & IP Set Infrastructure: Established WAF Web ACL linked with CloudFront, configured base protective rules, and initialized the blocked IP Set for upcoming automation tasks.

- Established Centralized Log Management: Enabled WAF Logging and integrated real-time access logs into CloudWatch Logs.
