---
title: "Project Preparation"
date: 2026-09-11
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

### Objective

In this section, you will prepare the Python automation script configuration file for AWS Lambda and ready the SSH attack simulation test scripts prior to deployment on the AWS infrastructure. You will also understand the standard list of parameters and resource names to ensure a seamless deployment process.

---

## 1. Prepare SSH Brute Force Test Scripts on Local Workstation

This project uses an Amazon EC2 Ubuntu server as the target object protected from SSH password guessing attacks. To prepare for testing the automated incident response system, ready the following test commands on your local Terminal or PowerShell:

Execute a manual SSH connection test command (simulating an invalid password login):
"ssh invalid_user@<EC2_PUBLIC_IP>"

Or prepare the Hydra tool to execute automated high-frequency Brute Force attack simulations (to trigger the violation threshold of ≥ 5 times/1 min):
"hydra -l admin -P passwords.txt <EC2_PUBLIC_IP> ssh -t 4"

---

## 2. Prepare AWS Lambda Function Source Code (Python)

Create a "lambda_function.py" file saved on your local machine. This Python script will be uploaded to the AWS Lambda service in subsequent steps to decode CloudWatch log data, extract offending IP addresses, update counters in Amazon DynamoDB, send alerts via Amazon SNS, and automatically insert "DENY" rules into the Network ACL (NACL).

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

---

## 3. Expected Outcomes

Upon completing this section, you will have:

- Prepared test scripts for SSH Brute Force attack simulations on your local workstation.
- Created the placeholder Python source code file ("lambda_function.py") for IP extraction and automated response logic.
- Mastered the standard resource names, database tables, and configuration parameters required for consistent deployment on the AWS Management Console in subsequent steps.