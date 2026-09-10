---
title: "Blog 1"
date: 2026-08-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Automating EC2 & RDS Cost Optimization with AWS Lambda and Amazon EventBridge

## Context & Problem

In cloud infrastructure management, non-production environments (Dev, Test, Staging) are typically only needed during business hours (e.g., 8:00 AM – 6:00 PM, Monday through Friday). However, these resources are frequently left running **24/7 (168 hours/week)**, whereas they are actively used for only **~50 hours/week**.

Leaving compute instances and database clusters idle overnight and over weekends leads to up to **~70% unnecessary cloud spend** on non-production workloads.

Doing this manually — asking developers to manually stop and start their instances daily — is error-prone, inconsistent, and drains engineering productivity.

So, how do we automate resource scheduling at enterprise scale while giving teams control over which workloads should be managed?

**So what's the right approach?**

## Solution Architecture: Event-Driven FinOps Automation

![Automated EC2 & RDS Cost Optimization Architecture](/images/blog/blog1.png)

The solution uses a **Serverless & Event-Driven** approach combining **Amazon EventBridge**, **AWS Lambda (Python/Boto3)**, and **AWS Resource Tagging**:

- **Tag-based Selection**: Only resources tagged with `AutoScheduler = true` are targeted, protecting critical infrastructure.
- **Scheduled Triggers**: EventBridge cron rules trigger Lambda functions at designated start/stop times (e.g., Stop at 7:00 PM, Start at 7:00 AM).
- **Multi-Service Support**: Handles both standalone EC2 instances and Amazon RDS DB instances/clusters seamlessly.
- **Zero Idle Infrastructure Cost**: The automation relies entirely on serverless services, operating well within the AWS Free Tier.

## 1. IAM Policy & Role Configuration

To allow AWS Lambda to inspect and manage EC2 and RDS instances securely, create an IAM Role with a least-privilege policy.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "EC2SchedulerPermissions",
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances",
        "ec2:StartInstances",
        "ec2:StopInstances"
      ],
      "Resource": "*"
    },
    {
      "Sid": "RDSSchedulerPermissions",
      "Effect": "Allow",
      "Action": [
        "rds:DescribeDBInstances",
        "rds:StartDBInstance",
        "rds:StopDBInstance",
        "rds:DescribeDBClusters",
        "rds:StartDBCluster",
        "rds:StopDBCluster"
      ],
      "Resource": "*"
    },
    {
      "Sid": "LoggingPermissions",
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    }
  ]
}
```

## 2. Core Automation Logic: AWS Lambda (Python 3.12)

The single Lambda function below dynamically evaluates an input payload (`"action": "start"` or `"action": "stop"`) to filter and execute status changes for both EC2 and RDS resources.

```python
import os
import logging
import boto3

logger = logging.getLogger()
logger.setLevel(logging.INFO)

ec2 = boto3.client('ec2')
rds = boto3.client('rds')

TARGET_TAG_KEY = os.environ.get('TARGET_TAG_KEY', 'AutoScheduler')
TARGET_TAG_VALUE = os.environ.get('TARGET_TAG_VALUE', 'true')


def lambda_handler(event, context):
    action = event.get('action', '').lower()
    if action not in ['start', 'stop']:
        logger.error(f"Invalid action '{action}'. Expected 'start' or 'stop'.")
        return {'statusCode': 400, 'body': 'Invalid action'}

    logger.info(f"Starting resource {action} sequence...")

    # Manage EC2 Instances
    manage_ec2_instances(action)

    # Manage RDS DB Instances
    manage_rds_instances(action)

    return {'statusCode': 200, 'body': f"Resource {action} execution completed."}


def manage_ec2_instances(action):
    filters = [
        {'Name': f'tag:{TARGET_TAG_KEY}', 'Values': [TARGET_TAG_VALUE]},
        {'Name': 'instance-state-name', 'Values': ['stopped' if action == 'start' else 'running']}
    ]

    response = ec2.describe_instances(Filters=filters)
    instance_ids = []

    for reservation in response.get('Reservations', []):
        for instance in reservation.get('Instances', []):
            instance_ids.append(instance['InstanceId'])

    if not instance_ids:
        logger.info(f"No EC2 instances eligible for action: {action}")
        return

    if action == 'start':
        ec2.start_instances(InstanceIds=instance_ids)
        logger.info(f"Successfully initiated START for EC2 Instances: {instance_ids}")
    else:
        ec2.stop_instances(InstanceIds=instance_ids)
        logger.info(f"Successfully initiated STOP for EC2 Instances: {instance_ids}")


def manage_rds_instances(action):
    response = rds.describe_db_instances()

    for db in response.get('DBInstances', []):
        db_id = db['DBInstanceIdentifier']
        db_status = db['DBInstanceStatus']

        # Verify tag existence
        arn = db['DBInstanceArn']
        tags_response = rds.list_tags_for_resource(ResourceName=arn)
        tag_list = tags_response.get('TagList', [])

        has_target_tag = any(
            t['Key'] == TARGET_TAG_KEY and t['Value'].lower() == TARGET_TAG_VALUE.lower()
            for t in tag_list
        )

        if not has_target_tag:
            continue

        if action == 'start' and db_status == 'stopped':
            rds.start_db_instance(DBInstanceIdentifier=db_id)
            logger.info(f"Successfully initiated START for RDS Instance: {db_id}")
        elif action == 'stop' and db_status == 'available':
            rds.stop_db_instance(DBInstanceIdentifier=db_id)
            logger.info(f"Successfully initiated STOP for RDS Instance: {db_id}")
```

## 3. Scheduling with Amazon EventBridge

Create two Amazon EventBridge rules to trigger the same Lambda function with custom JSON payloads.

### Rule 1: Stop Non-Prod Resources (Evening Rule)

- **Schedule expression:** `cron(0 12 ? * MON-FRI *)` → 12:00 UTC = 19:00 UTC+7
- **Target:** Lambda function
- **Constant JSON input:**

```json
{
  "action": "stop"
}
```

### Rule 2: Start Non-Prod Resources (Morning Rule)

- **Schedule expression:** `cron(0 0 ? * MON-FRI *)` → 00:00 UTC = 07:00 UTC+7
- **Target:** Lambda function
- **Constant JSON input:**

```json
{
  "action": "start"
}
```

## Results & Impact Analysis

By shifting non-production resources from a 24/7 execution cycle to a scheduled ~50-hour work week model (7:00 AM – 7:00 PM, Mon–Fri), runtime is drastically reduced.

| Metric                         | Unscheduled (Baseline)     | Automated via Lambda & EventBridge         |
| ------------------------------ | -------------------------- | ------------------------------------------ |
| Weekly operating hours         | 168 hours/week             | ~60 hours/week (includes buffer)           |
| Monthly billable compute time  | ~720 hours/month           | ~260 hours/month                           |
| Monthly compute cost reduction | 0%                         | ~64% savings                               |
| Human intervention             | High (manual start/stop)   | Zero (100% event-driven)                   |
| Operational impact             | Risk of forgetting to stop | Automatic & fully auditable via CloudWatch |

## Key Takeaways & Best Practices

- **Tagging policy enforcement:** Enforce standard tagging practices using AWS Organizations Tag Policies or AWS Config rules to ensure new non-production instances are automatically picked up by the scheduler.
- **Handling Multi-AZ & Aurora clusters:** For Multi-AZ or Aurora DB clusters, use the `describe_db_clusters` and `stop_db_cluster` API endpoints accordingly.
- **Graceful shutdowns:** Ensure developers do not run long-running, uncheckpointed background jobs outside business hours that could be disrupted by automated shutdowns.
- **AWS RDS auto-restart caveat:** Amazon RDS automatically restarts stopped DB instances after 7 days to apply maintenance updates. The EventBridge cron schedule handles this by systematically enforcing stop commands on non-working days.

## References

- [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/)
- [Amazon EventBridge Schedule Expressions](https://docs.aws.amazon.com/eventbridge/)
- [Boto3 Documentation — EC2 Client](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2.html)
- [Boto3 Documentation — RDS Client](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/rds.html)
