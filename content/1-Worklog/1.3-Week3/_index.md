---
title: "Worklog - Week 3"
date: 2026-08-17
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

- Master AWS Networking fundamentals: Virtual Private Cloud (VPC), Subnets, and Route Tables.
- Configure connectivity using Internet Gateways (IGW) and NAT Gateways for Private Subnets.
- Understand dual-layer network security: Security Groups (Stateful at Instance level) vs. NACLs (Stateless at Subnet level).
- Hands-on deployment of a production-grade Custom VPC containing isolated Public and Private Subnets.

### Tasks to be implemented this week:

| Day | Task Description | Start Date | End Date | Resource / Documentation |
| --- | --- | --- | --- | --- |
| Mon | - Overview of Amazon VPC: CIDR block allocation (IPv4), Public Subnets, and Private Subnets concepts.<br>- Design an IP addressing scheme for a custom VPC (e.g., 10.0.0.0/16). | 08/17/2026 | 08/17/2026 | https://cloudjourney.awsstudygroup.com/ |
| Tue | - Create Custom VPC, partition Subnets (Public: 10.0.1.0/24, Private: 10.0.2.0/24).<br>- Create an Internet Gateway (IGW) and attach it to the Custom VPC.<br>- Configure Route Tables to direct 0.0.0.0/0 traffic to the IGW for Public Subnets. | 08/18/2026 | 08/18/2026 | https://cloudjourney.awsstudygroup.com/ |
| Wed | - Study NAT Gateways: Enabling outbound internet access for Private Subnet instances.<br>- Allocate Elastic IP (EIP), launch NAT Gateway in Public Subnet, and update Private Route Tables. | 08/19/2026 | 08/19/2026 | https://cloudjourney.awsstudygroup.com/ |
| Thu | - Deep dive into Network Security: Security Groups vs Network Access Control Lists (NACLs).<br>- Configure Inbound/Outbound rules for SG and stateless ALLOW/DENY rules in NACLs. | 08/20/2026 | 08/20/2026 | https://cloudjourney.awsstudygroup.com/ |
| Fri | - Comprehensive Hands-on Lab: Deploy Bastion Host (Public EC2) and a Private EC2 Instance.<br>- SSH into Private EC2 via Bastion Host Jump Server.<br>- Verify outbound internet connectivity on Private EC2 via NAT Gateway and cleanup resources. | 08/21/2026 | 08/21/2026 | https://cloudjourney.awsstudygroup.com/ |

### Key Achievements in Week 3:

- Mastered AWS VPC Networking architecture, including CIDR calculations, subnetting, and route management.
- Successfully built a Custom VPC with fully functional Public and Private subnets.
- Understood stateful Security Groups vs. stateless NACLs and learned how to block specific IP addresses at the subnet perimeter.
- Completed comprehensive VPC Lab: Deployed Bastion Host jump server and verified NAT Gateway outbound internet capability for private resources.