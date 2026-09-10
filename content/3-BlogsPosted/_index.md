---
title: "Blogs Posted"
date: 2026-08-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

During my 8-week internship, I authored and published **3 in-depth technical blog posts** for the [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj) community. These articles focus on solving real-world enterprise challenges: automated FinOps cost optimization, VPN-less Zero-Trust security architecture, and leveraging Serverless Generative AI for internal knowledge retrieval.

| #      | Topic                                                                                      | Category (per FCAJ requirement)                          | Date       |
| ------ | ------------------------------------------------------------------------------------------ | -------------------------------------------------------- | ---------- |
| Blog 1 | **Automated EC2 & RDS Cost Optimization with AWS Lambda and EventBridge**                  | Cost optimization & FinOps automation with Serverless    | 01/08/2026 |
| Blog 2 | **Implementing AWS Verified Access in a TIC 3.0 Architecture without VPN**                 | Advanced Networking & Zero-Trust Access Security         | 15/08/2026 |
| Blog 3 | **Building an Enterprise Document RAG System with Amazon Bedrock & OpenSearch Serverless** | Generative AI Applications & Serverless Knowledge Search | 01/09/2026 |

---

### [3.1. Blog 1](3.1-Blog1/)

Running non-production environments (Dev, Test, Staging) 24/7 generates massive idle cloud spend for enterprises. This blog introduces an automated FinOps pattern combining **AWS Lambda**, **Amazon EventBridge**, and **Resource Tagging** to dynamically shut down idle EC2 instances and RDS databases outside working hours. By scheduling automated start/stop actions and validating instance tags, this solution helps DevOps teams reduce non-production compute costs by up to **60-70%** without impacting developer workflows.

---

### [3.2. Blog 2](3.2-Blog2/)

Implementing Zero-Trust security for government agencies and enterprises following TIC 3.0 (Trusted Internet Connections) guidelines often faces bottlenecks due to reliance on traditional VPNs. This blog analyzes how to leverage **AWS Verified Access (AVA)** alongside **AWS Network Firewall** and **Amazon Route 53 Resolver DNS Firewall** to build a secure VPN-less access architecture. The solution provides continuous verification based on user identity and device posture, satisfying strict TIC 3.0 compliance controls while optimizing remote worker productivity.

---

### [3.3. Blog 3](3.3-Blog3/)

Traditional keyword-based enterprise knowledge search systems struggle with semantic context and accurate answer synthesis. This article details the end-to-end implementation of a serverless, enterprise-grade **Generative AI Chatbot (RAG)**. By integrating **Amazon Bedrock (Titan Embeddings & Claude 3)**, **Amazon OpenSearch Serverless (Vector Engine)**, and **Knowledge Bases for Amazon Bedrock**, the solution delivers highly accurate document retrieval, automated chunking and indexing pipelines, and robust data security aligned with AWS standards.
