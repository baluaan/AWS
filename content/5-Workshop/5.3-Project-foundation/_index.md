---
title: "Project Preparation"
date: 2026-08-24
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

### Objective

In this section, you will prepare the static website source code and the Python automation script configuration for AWS Lambda before deploying to AWS infrastructure. You will also verify the source code structure to ensure a smooth deployment process.

---

## 1. Prepare the Static Website Source Code

This project uses a static website (HTML/CSS/JS) as the protected asset. The sample website source code is hosted on GitHub:
[https://github.com/vutungvn/portfolio](https://github.com/vutungvn/portfolio)

To download the website source code to your local machine, open Terminal or PowerShell and run the following commands:

```bash
# Clone the repository containing the website source code
git clone https://github.com/vutungvn/portfolio.git

# Move into the project directory
cd portfolio
```

The interface after a successful run:

![Running the application](/images/Workshop/Project-foundation/run-localhost.png)

---

## 2. Prepare the AWS Lambda Function Source Code (Python)

Create a `lambda_function.py` file on your local machine. This Python code will be deployed to AWS Lambda in Step 5.8 to extract offending IPs from CloudWatch Logs and automatically update the WAF IP Set.

```python
import os
import boto3
import json
import time

# Initialize AWS SDK clients
wafv2 = boto3.client('wafv2', region_name='us-east-1')
logs = boto3.client('logs', region_name='us-east-1')
sns = boto3.client('sns', region_name='us-east-1')

def lambda_handler(event, context):
    # Get parameters from Environment Variables
    ip_set_name = os.environ['IP_SET_NAME']
    ip_set_id = os.environ['IP_SET_ID']
    log_group_name = os.environ['LOG_GROUP_NAME']
    sns_topic_arn = os.environ['SNS_TOPIC_ARN']

    print(f"Starting scan of CloudWatch Log Group: {log_group_name}")

    # Query CloudWatch Logs for the last 5 minutes
    now = int(time.time() * 1000)
    start_time = now - (5 * 60 * 1000)

    query = "fields clientIp | filter action = 'BLOCK' | stats count(*) by clientIp"
    start_query_response = logs.start_query(
        logGroupName=log_group_name,
        startTime=start_time,
        endTime=now,
        queryString=query
    )

    query_id = start_query_response['queryId']

    # Wait for the query to complete
    response = None
    while response is None or response['status'] == 'Running':
        time.sleep(1)
        response = logs.get_query_results(queryId=query_id)

    extracted_ips = []
    for result in response['results']:
        for field in result:
            if field['field'] == 'clientIp':
                ip = field['value']
                # Format as standard CIDR for the WAF IP Set
                formatted_ip = f"{ip}/128" if ":" in ip else f"{ip}/32"
                extracted_ips.append(formatted_ip)

    if not extracted_ips:
        print("No new offending IPs found in the logs.")
        return {'statusCode': 200, 'body': 'No IPs to block'}

    # Get the current IP Set information from AWS WAF
    ip_set = wafv2.get_ip_set(
        Name=ip_set_name,
        Scope='CLOUDFRONT',
        Id=ip_set_id
    )

    current_addresses = ip_set['IPSet']['Addresses']
    lock_token = ip_set['LockToken']

    # Add the new offending IPs to the block list
    updated_addresses = list(set(current_addresses + extracted_ips))

    # Call the API to update the WAF IP Set
    wafv2.update_ip_set(
        Name=ip_set_name,
        Scope='CLOUDFRONT',
        Id=ip_set_id,
        Addresses=updated_addresses,
        LockToken=lock_token
    )

    print(f"Successfully updated the WAF IP Set. Blocked IPs: {updated_addresses}")
    return {'statusCode': 200, 'body': f"Blocked IPs: {extracted_ips}"}
```

---

## 3. Define the Required Environment Parameters

When deploying via the AWS Console in the following steps, you will need to use the following standardized configuration parameters:

| Parameter            | Value                               |
| -------------------- | ----------------------------------- |
| Required Region      | `us-east-1` (US East - N. Virginia) |
| WAF IP Set Name      | `AutoBlockedIPSetV6`                |
| WAF Web ACL Name     | `WebsiteProtectionACL`              |
| Log Group Name       | `aws-waf-logs-cloudfront`           |
| SNS Topic Name       | `WAFAlertTopic`                     |
| Lambda Function Name | `WAFAutoBlockFunction`              |

---

## 4. Expected Outcomes

After completing this section, you should have:

- Successfully set up the static website project.
- Prepared the Python source code (`lambda_function.py`) that handles the automated IP-blocking logic.
- A clear understanding of the resource names and standard configuration parameters needed for deployment in the AWS Management Console in the following sections.
