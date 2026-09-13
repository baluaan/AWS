---
title: "System Testing"
date: 2026-09-11
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

## Objectives

After completing the configuration of the entire infrastructure (EC2, CloudWatch Logs, Metric Filter, Subscription filter, CloudWatch Alarm, AWS Lambda, DynamoDB, NACL, SNS Topic), this testing step aims to verify the system's automated response capability against SSH brute-force attack attempts.

---
### Test Scenario
Use Kali Linux to execute a Hydra attack against the IP address of the Ubuntu EC2 instance to trigger the automated response mechanism.

---
### Test Execution Steps

#### Step 1: Execute attack using Kali Linux

Open Command Prompt (CMD) on the Kali Linux machine to use the Hydra tool for attempting SSH login to user `root` using a available password list.

```cmd
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://44.202.74.162 -t 4 
```

#### Step 2: Check Logs and CloudWatch Alarm

**CloudWatch Logs:**

- Navigate to **CloudWatch > Log management > /aws/ec2/security/auth**
- Check the latest Log Streams to view the attack history and logged events.
![Latest logs when under attack](/images/5/8/1.png)

**CloudWatch Alarm:**

- Navigate to **CloudWatch > Alarms > All alarms**.
- Observe the `SSH-BruteForce-Detected` alarm. After 1–3 minutes, the Metric Filter detects that the threshold of 5 attempts / 1 minute has been exceeded, and the Alarm transitions to the **In alarm** state.

![Alert from Alarm](/images/5/8/2.png)


#### Step 3: Check data in DynamoDB

- Navigate to **DynamoDB > Tables > SSHBruteForceCounter > Explore table items**
- Observe the `SSHBruteForceCounter` table list to see the number of failed login attempts.
![DynamoDB table counting failed login attempts](/images/5/8/3.png)


#### Step 4: Check AWS Lambda execution logs

- Navigate to **AWS Lambda > select function `WAFAutoBlockFunction` > select Monitor tab > select View CloudWatch logs**.
- Open the latest Log Stream and inspect the log execution details:
![Execution log of the Lambda function](/images/5/8/4.png)

#### Step 5: Check blocked IP list in NACL

- Navigate to **VPC > Network ACLs > acl-050ea4da0cd7e08aa / Name: SSH-Auto-Block-NACL**
- Locate Inbound rules and observe the blocked IP list.
- IP address `58.187.56.50/32` has been added to the block list.

![Inbound rule blocking IP](/images/5/8/5.png)

#### Step 6: Check notification Email from SNS

Check the inbox of the Gmail account registered with the SNS Topic; you will receive the alert email `SH-BruteForce-Detected`
![Alert email sent to inbox](/images/5/8/6.png)


---
### Results Evaluation

| Test Item | Expected State | Actual State | Conclusion |
| --- | --- | --- | --- |
| WAF Log Recording | Push access logs to CloudWatch Logs | Logs appeared in Log Group | PASS |
| Trigger Alarm | Switch to `IN ALARM` when > 5 failed logins / 1 min | Alarm triggered accurately upon reaching threshold | PASS |
| Lambda Automation | Accurately extract offending IP (`ATTACKER IP`) | Successfully extracted offending IPv6/IPv4 address | PASS |
| Update NACL | Block attacker's IP address | Successfully denied inbound traffic from attacker's IP address | PASS |
| DynamoDB Data | Count failed login attempts for an IP | Counted failed login attempts for an IP | PASS |
| Send Email Notification | Send alert email regarding the incident | SNS Email sent to Gmail to alert about the incident | PASS |

**Overall Assessment:** The system responded entirely automatically, successfully detecting and mitigating the attacking IP address according to the intended architecture design.