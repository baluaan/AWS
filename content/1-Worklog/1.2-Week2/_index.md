---
title: "Week 2 - Worklog"
date: 2026-07-25
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:

- Master AWS Networking Fundamentals: Gain in-depth knowledge of VPC architecture, Subnets, Route Tables, Internet/NAT Gateways, and network security mechanisms (Security Groups, NACLs, VPC Flow Logs).

- Deploy Secure Network Infrastructure: Configure secure connectivity using EC2 Instance Connect Endpoints, establish Site-to-Site VPN connections, and set up VPC Peering.

- Integrate Hybrid DNS & Identity Services: Set up Hybrid DNS using Route 53 Resolver, deploy Microsoft Active Directory on AWS, and establish access via RDGW.

- Implement Monitoring & Automation: Deploy CloudWatch Monitoring, initialize infrastructure using CloudFormation templates, and refine security group configurations.

### Tasks to be carried out this week:

| Day | Task                                                                                                                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                      |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | --------------------------------------- |
| 2   | - Learn about VPC: Concepts, architecture and scope, core components: Subnets, Route Tables, Internet Gateways, NAT Gateways.<br>- Learn about Firewall in VPC: Security Groups, Network ACLs, VPC Resource Map.<br>- Create VPC.<br>- Create Subnets.<br>- Create Internet Gateway.                   | 27/07/2026 | 27/07/2026      | https://cloudjourney.awsstudygroup.com/ |
| 3   | - Create Route Table.<br>- Create Security Group.<br>- Enable VPC Flow Logs.                                                                                                                                                                                                                           | 28/07/2026 | 28/07/2026      | https://cloudjourney.awsstudygroup.com/ |
| 4   | - Create NAT Gateway.<br>- Create EC2 Instance Connect Endpoint.<br>- Deploy CloudWatch Monitoring.<br>- Configure Site-to-Site VPN:<br> + Set up VPN environment: Create VPN for VPC, create EC2 instance.<br> + Configure VPN Connection: Create Virtual Private Gateway, create Customer Gateway... | 29/07/2026 | 29/07/2026      | https://cloudjourney.awsstudygroup.com/ |
| 5   | - Set up Hybrid DNS with Route 53 Resolver.<br>- Generate key pair.<br>- Initialize cloudformation template. <br> - Configuring security group. <br> - Connecting to RDGW. <br> - Deploy Microsoft AD. <br> - Set up DNS.                                                                              | 30/07/2026 | 30/07/2026      | https://cloudjourney.awsstudygroup.com/ |
| 6   | - Setting up VPC Peering.<br>- Update Network ACL.<br>- Create Peering Connection. <br> - Set up Cross-Peer DNS.                                                                                                                                                                                       | 31/07/2026 | 31/07/2026      | https://cloudjourney.awsstudygroup.com/ |

### Week 2 Achievements:

- Successfully Built Core VPC Infrastructure: Provisioned a complete custom VPC with Public/Private Subnets, Route Tables, Internet Gateway, NAT Gateway, and enabled VPC Flow Logs for traffic inspection.

- Established Secure Connectivity & Peering: Deployed EC2 Instance Connect Endpoint, configured a Site-to-Site VPN (Virtual Private Gateway & Customer Gateway), and linked VPCs via VPC Peering with Cross-Peer DNS and updated NACLs.

- Deployed Advanced DNS & Directory Services: Integrated Route 53 Resolver for Hybrid DNS resolution and successfully launched Microsoft Active Directory with RDGW using CloudFormation templates.

- Configured Centralized Monitoring: Fully enabled CloudWatch Monitoring across the newly established network and instance infrastructure.
