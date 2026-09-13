---
title: "Initialize Automated Incident Response"
date: 2026-09-11
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

### Objective

Initialize Amazon Lambda, DynamoDB, IAM, CloudWatch, and NACL services to create an automated incident response workflow when under SSH attack.
Successfully block the attacker's IP address.

---

## 1. Overview

In this architecture:
- **Amazon CloudWatch** CloudWatch Agent collects `/var/log/auth.log` from the EC2 instance. Subscription Filter scans for Failed password events and forwards the payload to AWS Lambda.
- **AWS Lambda** Decodes logs, extracts offending IPs via Regex, interacts with DynamoDB to track failed attempt counts, sends notification emails via SNS, and automatically inserts DENY rules into the NACL.
- **Amazon DynamoDB** Stores failed login counts grouped by IP and timestamp (within 1 minute) to evaluate violation thresholds (≥ 5 times/1 minute).
- **AWS IAM** Provides secure permissions for EC2 to push logs to CloudWatch and grants Lambda access to operate on DynamoDB, Network ACL, and CloudWatch Logs.
- **Network ACL** Completely blocks SSH packets from offending IPs directly at the VPC network layer before connections can reach the EC2 instance.

---

## 2. Deployment Procedure

1. Initialize the IAM role following these steps:

**Step 1** Navigate to **IAM** > **Roles** and select **Create Role**
- Configure as follows:
  - Trusted entity type: AWS service 
  - Use case: Lambda
  - Select: Next
![Step1](/images/5/7/8.png)

**Step 2** Add permissions
  - Select: Create inline policy
  - Policy:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateNetworkAclEntry",
                "ec2:DescribeNetworkAcls"
            ],
            "Resource": "*"
        }
    ]
}
```
  - Select: Next 
![Step2](/images/5/7/9.png)

**Step 3** 
Set role name: `Lambda-SSH-AutoBlock-Role` 
Set Inline policy name: `LambdaSSHAutoBlockRole`
Select: **Create**
![Role name](/images/5/7/10.png)
![Inline policy name](/images/5/7/11.png)
Go to the created IAM role to add policy `AWSLAmbdaBasicExcutionRole`
![Add Policy](/images/5/7/14.png)

2. Initialize and configure **Network ACLs**

**Step 1** Initialize Network ACL
Navigate to **VPC** > **Network ACLs** > **Create network ACL**
- Name: Name: SSH-Auto-Block-NACL
- VPC: select the VPC created during the EC2 creation step
- Tag: select Key: Name, Value: Name: SSH-Auto-Block-NACL
- Create network ACL
![Step1](/images/5/7/4.png)

**Step 2** Configure the newly created Network ACL
Navigate to **VPC** > **Network ACLs** > **acl-050ea4da0cd7e08aa / Name: SSH-Auto-Block-NACL**  
First select: **Edit subnet associations**  
Select `Subnet public 1` as the EC2 instance was created using that Subnet  
Then **Save changes**
![Step2](/images/5/7/5.png)  
Next, move to configuring `Inbound` and `outbound` rules  
`Add new rule` with configuration: 
  - Rule number: 100
  - Type: All traffic
  - Destination: 0.0.0.0/0
  - Allow
![Inbound rule](/images/5/7/6.png)
![Outbound rule](/images/5/7/7.png)

3. Initialize and configure Lambda function
**Step 1** Create Lambda function
Navigate step by step: **Lambda** > **Functions** > **Create function**
  - Select: Author from scratch
  - Function name: ssh-auto-block
  - Select: Python 3.14
  - Additional settings > Custom execution role: Lambda-SSH-AutoBlock-Role
  - Save
  - Create Function
![Step1](/images/5/7/12.png)

**Step 2** Configuration
Access the newly created function `ssh-auto-block`
Find the `Configuration` section, select `Environment variables`, and select `Edit`
Configure:
  - Key: NACL_ID
  - Value: ID value of the newly created NACL `acl-050ea4da0cd7e08aa`
  - Save
![Step2](/images/5/7/13.png)

4. Create Subscription filters
Navigate step by step: **CloudWatch** > **Log management** > **/aws/ec2/security/auth**
Select **Subscription filters**
Select `Create lambda subscription filter`
![Navigate to filter creation section](/images/5/7/15.png)

After entering the filter creation section:
- Set destination: ssh-auto-block
- Subscription filter pattern: `"Failed password"`
- Subscription filter name: ssh
- Then select `Start streaming`
![Configuration](/images/5/7/16.png)

5. Create DynamoDB 
**Step 1** Navigate step by step: **DynamoDB** > **Tables** > **Create table**
Configure the table used for counting attack attempts to integrate with Lambda
  - Name: SSHBruteforceCounter
  - Partition key: id - string
  - Create
![Create DynamoDB](/images/5/7/18.png)

**Step 2** Link environment
Access the function `ssh-auto-block`
Find the `Configuration` section, select `Environment variables`, and select `Edit`
Configure:
  - Key: TABLE_NAME
  - Value: SSHBruteforceCounter
  - Save
![Step2](/images/5/7/19.png)

**Step 3** Add permissions to IAM role
Access `Lambda-SSH-AutoBlock-Role`, then `add permission` via `create inline policy`
Add code to grant permission to update the DynamoDB table
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "dynamodb:UpdateItem"
            ],
            "Resource": "arn:aws:dynamodb:*:133665990651:table/SSHBruteForceCounter"
        }
    ]
}
```
![Add permission](/images/5/7/20.png)

Then name the newly created permission:
Name: LambdaSSHBruteForceDynamoDB
Then select: Create policy
![Name policy](/images/5/7/21.png)
6. Paste code for the function
Access the function `ssh-auto-block`
Go to the code section:
Then select `Deploy`
```python
import boto3
import os
import ipaddress
import base64
import gzip
import json
import re
import time

ec2 = boto3.client("ec2")
dynamodb = boto3.resource("dynamodb")

NACL_ID = os.environ["NACL_ID"]
TABLE_NAME = os.environ["TABLE_NAME"]

table = dynamodb.Table(TABLE_NAME)

THRESHOLD = 5
WINDOW_SECONDS = 60


def lambda_handler(event, context):

    print("Received CloudWatch Logs event")

    # ==========================================
    # 1. Decode CloudWatch Logs event
    # ==========================================

    try:

        compressed_payload = base64.b64decode(
            event["awslogs"]["data"]
        )

        payload = gzip.decompress(
            compressed_payload
        )

        data = json.loads(payload)

    except Exception as e:

        print("Failed to decode CloudWatch event:")
        print(str(e))

        return {
            "statusCode": 400,
            "message": "Invalid CloudWatch Logs event"
        }


    # ==========================================
    # 2. Process each SSH log
    # ==========================================

    for log_event in data["logEvents"]:

        message = log_event["message"].strip()

        print("SSH LOG:")
        print(message)


        # ==========================================
        # 3. Extract attacker IP
        # ==========================================

        match = re.search(
            r"Failed password.*from\s+(\d{1,3}(?:\.\d{1,3}){3})",
            message
        )

        if not match:

            print("No attacker IP found")

            continue


        attacker_ip = match.group(1)

        print("ATTACKER IP:", attacker_ip)


        # ==========================================
        # 4. Validate IP
        # ==========================================

        try:

            ipaddress.ip_address(attacker_ip)

        except ValueError:

            print("Invalid IP:", attacker_ip)

            continue


        # ==========================================
        # 5. Create 1-minute bucket
        # ==========================================

        current_time = int(time.time())

        minute_bucket = current_time // WINDOW_SECONDS

        item_id = f"{attacker_ip}#{minute_bucket}"

        expiration = current_time + 120


        print("Counter ID:", item_id)


        # ==========================================
        # 6. Increase failure counter
        # ==========================================

        response = table.update_item(

            Key={
                "id": item_id
            },

            UpdateExpression="""
                ADD failure_count :one
                SET expires_at = if_not_exists(expires_at, :expiration)
            """,

            ExpressionAttributeValues={
                ":one": 1,
                ":expiration": expiration
            },

            ReturnValues="ALL_NEW"
        )


        failure_count = response["Attributes"]["failure_count"]


        print(
            "Failed attempts:",
            failure_count,
            "/",
            THRESHOLD
        )


        # ==========================================
        # 7. Check threshold
        # ==========================================

        if failure_count < THRESHOLD:

            print(
                f"{attacker_ip}: "
                f"{failure_count}/{THRESHOLD} "
                f"attempts - NOT BLOCKED"
            )

            continue


        # ==========================================
        # 8. Threshold reached
        # ==========================================

        print(
            f"THRESHOLD REACHED: "
            f"{attacker_ip}"
        )


        cidr = attacker_ip + "/32"


        # ==========================================
        # 9. Check existing NACL rules
        # ==========================================

        response = ec2.describe_network_acls(
            NetworkAclIds=[NACL_ID]
        )

        used_rules = []

        already_blocked = False


        for acl in response["NetworkAcls"]:

            for entry in acl["Entries"]:

                used_rules.append(
                    entry["RuleNumber"]
                )

                if (
                    entry.get("CidrBlock") == cidr
                    and entry.get("RuleAction") == "deny"
                    and entry.get("Egress") is False
                ):

                    already_blocked = True

                    break


        if already_blocked:

            print(
                "IP already blocked:",
                cidr
            )

            continue


        # ==========================================
        # 10. Find available rule number
        # ==========================================

        rule_number = 50

        while rule_number in used_rules:

            rule_number += 1


        # ==========================================
        # 11. Create NACL DENY rule
        # ==========================================

        try:

            ec2.create_network_acl_entry(

                NetworkAclId=NACL_ID,

                RuleNumber=rule_number,

                Protocol="-1",

                RuleAction="deny",

                Egress=False,

                CidrBlock=cidr
            )

            print(
                "SUCCESSFULLY BLOCKED:",
                attacker_ip
            )

            print(
                "NACL rule:",
                rule_number
            )

        except Exception as e:

            print(
                "Failed to create NACL rule:"
            )

            print(str(e))


    return {

        "statusCode": 200,

        "message":
            "SSH brute-force detection processed"

    }
```
![code](/images/5/7/22.png)
---


## 4. Expected Results

After completing this chapter, you will achieve:

- Successfully creating NACL rules to block or allow inbound and outbound traffic.
- Successfully creating a DynamoDB table to count `Failed Password` instances, combined with the Lambda function to trigger blocking of harmful IPs.
- Completing the configuration of the automated IP blocking feature when under SSH attack.