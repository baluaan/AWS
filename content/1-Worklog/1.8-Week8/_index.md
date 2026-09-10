---
title: "Week 8 - Worklog"
date: 2026-09-05
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives:

- Load Testing & Attack Simulation: Utilize stress testing tools (Artillery, Apache JMeter) to simulate HTTP Flood / DDoS attack scenarios targeting the web application.

- End-to-End Automation Verification: Evaluate the entire automated incident response lifecycle: abnormal traffic spike -> WAF logging -> CloudWatch Alarm trigger -> Lambda IP extraction -> WAF Blocked IP Set update -> Amazon SNS notification dispatch.

- Performance Measurement & Optimization: Measure latency across the automated mitigation workflow, fine-tune Metric Filter patterns, Alarm evaluation thresholds, and IP unblock mechanisms (TTL) to minimize false positives.

- Project Finalization & Handover: Aggregate empirical data, finalize technical documentation, record benchmark metrics, and deliver system handover reports.

### Tasks to be carried out this week:

| Day | Task                                                                                                                                                                                                                           | Start Date | Completion Date | Reference Material                      |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | --------------------------------------- |
| 2   | - Prepare load testing environment & simulate attack vectors.<br>- Configure HTTP Flood simulation scenarios using Artillery/JMeter.<br>- Generate high-rate traffic exceeding defined thresholds from test IP addresses.      | 07/09/2026 | 07/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 3   | - Conduct End-to-End automated response testing.<br>- Monitor CloudWatch Alarm state transition latency.<br>- Verify accurate IP extraction and dynamic entry addition into WAF IP Set.<br>- Confirm SNS email alert delivery. | 08/09/2026 | 08/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 4   | - Performance evaluation & fine-tuning.<br>- Measure end-to-end response time across the mitigation pipeline.<br>- Adjust evaluation periods and threshold limits on CloudWatch Alarms to optimize detection sensitivity.      | 09/09/2026 | 09/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 5   | - Verify automated IP expiration logic (TTL).<br>- Validate automated removal of blocked IP addresses from WAF IP Set after specified timeouts.<br>- Test traffic restoration for legitimate users post-mitigation.            | 10/09/2026 | 10/09/2026      | https://cloudjourney.awsstudygroup.com/ |
| 6   | - Project finalization & report delivery.<br>- Compile load test results, performance metrics, and system benchmarks.<br>- Finalize end-to-end architecture diagrams, technical documentation, and operation manuals.          | 11/09/2026 | 11/09/2026      | https://cloudjourney.awsstudygroup.com/ |

### Week 8 Achievements:

- Successfully Simulated Real-World Attack Scenarios: Executed comprehensive HTTP Flood load tests, generating sufficient traffic to trigger automated edge security mechanisms.

- Verified End-to-End Automation Pipeline: Validated 100% operational accuracy of the automated mitigation flow from threat detection and IP blocking to instant SNS alerts.

- Optimized Performance & Reduced False Positives: Benchmark response times across the pipeline, fine-tuning detection parameters to balance system sensitivity and operational reliability.

- Completed Project Acceptance & Handover: Delivered technical reports, architecture diagrams, and system operations guides for cloud-native automated incident response on AWS.
