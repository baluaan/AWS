---
title: "Blog 3"
date: 2026-09-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Building an Enterprise Document RAG System with Amazon Bedrock & OpenSearch Serverless

## Context & Problem

In large enterprises and organizations, the volume of internal documents (operating procedures, technical documentation, HR policies, financial reports) grows very quickly over time. Traditional keyword-based search tools suffer from several limitations:

- **Lack of contextual understanding:** They return a list of document paths containing the keyword but don't directly answer the user's question.
- **Time-consuming synthesis:** Employees have to open each document, read it, and manually piece together the information, which reduces productivity.
- **Data leakage risk:** Sending internal documents to public external LLM services strictly violates enterprise data security and compliance policies.

The **Retrieval-Augmented Generation (RAG)** model was created to solve this problem by combining semantic search over internal data with the answer-synthesis capability of a Large Language Model (LLM).

However, building a traditional RAG infrastructure from scratch requires managing a complex Vector Database cluster, building a custom document chunking pipeline, and running model-serving servers 24/7 at very high cost.

**How can we build an enterprise-grade RAG system that is secure, accurate, and fully serverless in order to optimize operating costs?**

---

## Solution Architecture: Serverless RAG System on AWS

![Enterprise document RAG architecture with Amazon Bedrock and OpenSearch Serverless](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2025/06/18/ML-18649-architecture.jpg)

The solution leverages **Knowledge Bases for Amazon Bedrock** together with **Amazon OpenSearch Serverless (Vector Engine)**, **AWS Lambda**, and **Amazon S3** to build a complete RAG architecture with no infrastructure to manage:

- **Automated data ingestion pipeline:** Documents stored in Amazon S3 are automatically chunked, converted into semantic vector representations (embeddings) using **Amazon Titan Text Embeddings**, and then stored in the **Amazon OpenSearch Serverless Vector Engine**.
- **Semantic retrieval & answer generation:** When a user asks a question, **Knowledge Bases for Amazon Bedrock** performs a vector search on OpenSearch Serverless to retrieve the most relevant context chunks, then sends them along with the prompt to **Anthropic Claude 3** on Amazon Bedrock to generate an answer with source citations.
- **Security & access control:** Data is encrypted at rest and in transit, complying with AWS enterprise data security standards.

---

## 1. Configuring the IAM Policy for the Knowledge Base & Lambda

For **Knowledge Bases for Amazon Bedrock** to read documents from S3, invoke the embedding model, and read/write data to OpenSearch Serverless, set up an IAM Role with least-privilege permissions.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "BedrockModelInvocationPermissions",
      "Effect": "Allow",
      "Action": ["bedrock:InvokeModel"],
      "Resource": [
        "arn:aws:bedrock:us-east-1::foundation-model/amazon.titan-embed-text-v2:0",
        "arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-sonnet-20240229-v1:0"
      ]
    },
    {
      "Sid": "S3BucketAccessPermissions",
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:ListBucket"],
      "Resource": [
        "arn:aws:s3:::enterprise-knowledge-base-bucket",
        "arn:aws:s3:::enterprise-knowledge-base-bucket/*"
      ]
    },
    {
      "Sid": "OpenSearchServerlessAccessPermissions",
      "Effect": "Allow",
      "Action": ["aoss:APIAccessAll"],
      "Resource": "arn:aws:aoss:us-east-1:123456789012:collection/*"
    }
  ]
}
```

---

## 2. RAG Query Handling Logic: AWS Lambda (Python 3.12)

The Lambda function below receives the user's question via an API call, invokes the Amazon Bedrock `RetrieveAndGenerate` API to query the Knowledge Base, and returns the answer along with the referenced source documents.

```python
import os
import json
import logging
import boto3

logger = logging.getLogger()
logger.setLevel(logging.INFO)

bedrock_agent_runtime = boto3.client('bedrock-agent-runtime')

KNOWLEDGE_BASE_ID = os.environ.get('KNOWLEDGE_BASE_ID')
MODEL_ARN = os.environ.get('MODEL_ARN', 'arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-sonnet-20240229-v1:0')


def lambda_handler(event, context):
    try:
        body = json.loads(event.get('body', '{}')) if isinstance(event.get('body'), str) else event
        user_query = body.get('question')

        if not user_query:
            logger.error("Missing 'question' parameter in the request payload.")
            return {
                'statusCode': 400,
                'headers': {'Content-Type': 'application/json'},
                'body': json.dumps({'error': 'Parameter "question" is required.'})
            }

        logger.info(f"Processing query for Knowledge Base ID {KNOWLEDGE_BASE_ID}: {user_query}")

        # Execute RetrieveAndGenerate API call
        response = bedrock_agent_runtime.retrieve_and_generate(
            input={
                'text': user_query
            },
            retrieveAndGenerateConfiguration={
                'type': 'KNOWLEDGE_BASE',
                'knowledgeBaseConfiguration': {
                    'knowledgeBaseId': KNOWLEDGE_BASE_ID,
                    'modelArn': MODEL_ARN,
                    'retrievalConfiguration': {
                        'vectorSearchConfiguration': {
                            'numberOfResults': 5
                        }
                    }
                }
            }
        )

        output_text = response.get('output', {}).get('text', '')
        citations = extract_citations(response.get('citations', []))

        return {
            'statusCode': 200,
            'headers': {'Content-Type': 'application/json'},
            'body': json.dumps({
                'answer': output_text,
                'citations': citations
            }, ensure_ascii=False)
        }

    except Exception as e:
        logger.error(f"Error processing RAG query: {str(e)}", exc_info=True)
        return {
            'statusCode': 500,
            'headers': {'Content-Type': 'application/json'},
            'body': json.dumps({'error': 'Internal server error processing the request.'})
        }


def extract_citations(citation_list):
    sources = []
    for citation in citation_list:
        for reference in citation.get('retrievedReferences', []):
            location = reference.get('location', {}).get('s3Location', {}).get('uri', '')
            content_snippet = reference.get('content', {}).get('text', '')
            sources.append({
                'source_uri': location,
                'snippet': content_snippet
            })
    return sources
```

---

## 3. Automating the Data Ingestion Pipeline

When a new document is uploaded to Amazon S3, an Amazon EventBridge Rule or S3 Event Notification triggers a secondary Lambda function to automatically kick off the sync process (Ingestion Job) into the Knowledge Base.

### Configuring the S3 Event Notification & Lambda Trigger

```python
import os
import logging
import boto3

logger = logging.getLogger()
logger.setLevel(logging.INFO)

bedrock_agent = boto3.client('bedrock-agent')

KNOWLEDGE_BASE_ID = os.environ.get('KNOWLEDGE_BASE_ID')
DATA_SOURCE_ID = os.environ.get('DATA_SOURCE_ID')


def lambda_handler(event, context):
    logger.info("Triggered S3 Data Ingestion Pipeline...")

    try:
        response = bedrock_agent.start_ingestion_job(
            knowledgeBaseId=KNOWLEDGE_BASE_ID,
            dataSourceId=DATA_SOURCE_ID,
            description="Auto-triggered ingestion job from new S3 upload"
        )

        job_id = response.get('ingestionJob', {}).get('ingestionJobId')
        logger.info(f"Successfully started ingestion job ID: {job_id}")

        return {
            'statusCode': 200,
            'body': f"Ingestion job {job_id} started successfully."
        }

    except Exception as e:
        logger.error(f"Failed to start ingestion job: {str(e)}")
        raise e
```

---

## Results & Impact Analysis

Moving from a traditional keyword-search mechanism to a Serverless RAG system delivers dramatic improvements in both productivity and operating cost.

| Metric                       | Traditional Search (Keyword Search)                    | Serverless RAG System (Bedrock + OpenSearch)            |
| ---------------------------- | ------------------------------------------------------ | ------------------------------------------------------- |
| Answer accuracy              | Low (returns only file paths, requires manual reading) | High (direct answers with accurate source citations)    |
| Average search time          | ~10-15 minutes / request                               | ~2-3 seconds / request                                  |
| Fixed infrastructure cost    | Cost of running a search engine server 24/7            | $0 when there are no queries (fully serverless)         |
| Vector DB cluster management | Complex (manual configuration, backup, node scaling)   | 100% automatic with OpenSearch Serverless (OCU scaling) |
| Data security                | Leakage risk when using a public AI API                | Data fully isolated within the AWS VPC & Account        |

---

## Key Points & Best Practices

- **Optimize the chunking strategy:** Choosing the right chunk size (e.g., 300-500 tokens with 20% overlap) helps preserve full context without introducing noise during vector search.
- **Use hybrid search:** Configure OpenSearch Serverless to combine Vector Search (semantic) and Text Search (keyword) for optimal accuracy on domain-specific terms or identifiers.
- **Optimize cost with OpenSearch Serverless (OCU):** Monitor OpenSearch Compute Unit (OCU) metrics to fine-tune the minimum scaling threshold and avoid unnecessary costs in idle Dev/Test environments.
- **Ensure accuracy (hallucination control):** Set the temperature parameter low (e.g., 0.0-0.2) on the Claude 3 model so it only synthesizes information based on the context provided by the Knowledge Base.

---

## References

- [Knowledge Bases for Amazon Bedrock User Guide](https://docs.aws.amazon.com/bedrock/latest/userguide/knowledge-base.html)
- [Amazon OpenSearch Serverless Developer Guide](https://docs.aws.amazon.com/opensearch-service/latest/developerguide/serverless.html)
- [Boto3 Documentation — Bedrock Agent Runtime](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/bedrock-agent-runtime.html)
