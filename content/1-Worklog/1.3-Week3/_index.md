---
title: "Week 3 - Worklog"
date: 2026-08-01
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

- Master AWS Interconnectivity & Hybrid Storage: Gain practical hands-on experience with AWS Transit Gateway, AWS File Storage Gateway, and hybrid virtual machine migration (VM Import/Export).

- Deploy Automated Data Protection & Backup: Configure centralized backup policies using AWS Backup, integrated with S3 storage and notification mechanisms.

- Configure Object Storage & Static Web Hosting: Learn Amazon S3 management, including bucket policies, Block Public Access settings, static website hosting, and Access Control Lists (ACLs).

- Execute On-Premises to Cloud Migration: Practice exporting, uploading, importing, and deploying virtual machine images (AMIs) between on-premises environments and AWS.

### Tasks to be carried out this week:

| Day | Task                                                                                                                                                                                                                             | Start Date | Completion Date | Reference Material                      |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- | --------------- | --------------------------------------- |
| 2   | - Set up AWS Transit Gateway.<br>- Create Transit Gateway.<br>- Create Transit Gateway route tables.<br>- Add Transit Gateway routes to VPC route tables.                                                                        | 03/08/2026 | 03/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 3   | - Deploy AWS Backup to the System.<br>- Create S3 Bucket.<br>- Create Backup plan. <br> - Set up notifications.                                                                                                                  | 04/08/2026 | 04/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 4   | - Deploy File Storage Gateway.<br>- Create EC2 for Storage Gateway.<br>- Create Storage Gateway.<br>- Create File Shares.<br> - Connect File Shares on On-premise machine.                                                       | 05/08/2026 | 05/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 5   | - Starting with Amazon S3.<br>- Enable static website feature.<br>- Configuring public access block. <br> - Configuring public objects.                                                                                          | 06/08/2026 | 06/08/2026      | https://cloudjourney.awsstudygroup.com/ |
| 6   | - Export Virtual Machine from On-premises.<br>- Upload virtual machine to AWS.<br>- Import virtual machine to AWS. <br> - Deploy Instance from AMI. <br> - Setting up S3 bucket ACL.<br> - Export virtual machine from Instance. | 07/08/2026 | 07/08/2026      | https://cloudjourney.awsstudygroup.com/ |

### Week 3 Achievements:

- Successfully Configured AWS Transit Gateway: Created Transit Gateway resource, established route tables, and updated VPC route tables to enable seamless cross-network routing.

- Deployed Automated Backup Management: Provisioned S3 target buckets and configured automated AWS Backup plans with event notification systems.

- Established File Storage Gateway: Deployed EC2-based Storage Gateway, configured file shares, and successfully mounted shares onto on-premises machines for hybrid access.

- Implemented S3 Static Web Hosting & Security: Enabled static website hosting on Amazon S3 while properly configuring Block Public Access rules and object-level permissions.

- Executed End-to-End VM Migration & Management: Completed full-cycle virtual machine export/import processes between on-premises and AWS, launched EC2 instances from converted AMIs, managed S3 ACLs, and exported instances back to VM formats.
