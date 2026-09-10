---
title: "Blog 3"
date: 2026-09-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Xây dựng hệ thống RAG tra cứu tài liệu doanh nghiệp với Amazon Bedrock & OpenSearch Serverless

## Bối cảnh & Vấn đề

Trong các doanh nghiệp và tổ chức lớn, khối lượng tài liệu nội bộ (quy trình vận hành, tài liệu kỹ thuật, chính sách nhân sự, báo cáo tài chính) tăng trưởng rất nhanh theo thời gian. Các công cụ tìm kiếm truyền thống dựa trên từ khóa (keyword search) thường gặp nhiều hạn chế:

- **Thiếu hiểu biết về ngữ cảnh:** Trả về danh sách đường dẫn tài liệu chứa từ khóa nhưng không trả lời trực tiếp câu hỏi của người dùng.
- **Tốn thời gian tổng hợp:** Nhân viên phải mở từng tài liệu, đọc và tự tổng hợp thông tin thủ công, làm giảm hiệu suất làm việc.
- **Rủi ro rò rỉ dữ liệu:** Việc gửi tài liệu nội bộ lên các dịch vụ public LLM bên ngoài vi phạm khắt khe các chính sách bảo mật và tuân thủ dữ liệu.

Mô hình **Retrieval-Augmented Generation (RAG)** ra đời để giải quyết vấn đề này bằng cách kết hợp khả năng tìm kiếm ngữ nghĩa (semantic search) trên dữ liệu nội bộ với khả năng tổng hợp câu trả lời của Large Language Model (LLM).

Tuy nhiên, việc tự triển khai một hạ tầng RAG truyền thống đòi hỏi phải quản lý cụm Vector Database phức tạp, tự xây dựng pipeline phân đoạn tài liệu (chunking) và duy trì các máy chủ phục vụ mô hình 24/7 với chi phí rất cao.

**Làm thế nào để xây dựng một hệ thống RAG chuẩn doanh nghiệp, vừa bảo mật, chính xác, vừa hoạt động hoàn toàn dưới dạng Serverless để tối ưu chi phí vận hành?**

---

## Kiến trúc giải pháp: Hệ thống RAG Serverless trên AWS

![Kiến trúc RAG tra cứu tài liệu doanh nghiệp với Amazon Bedrock và OpenSearch Serverless](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2025/06/18/ML-18649-architecture.jpg)

Giải pháp tận dụng sức mạnh của **Knowledge Bases for Amazon Bedrock** kết hợp với **Amazon OpenSearch Serverless (Vector Engine)**, **AWS Lambda** và **Amazon S3** để tạo nên một kiến trúc RAG hoàn chỉnh không quản lý hạ tầng:

- **Pipeline nạp dữ liệu tự động (Ingestion Pipeline):** Tài liệu lưu trữ trên Amazon S3 được tự động phân đoạn (chunking), chuyển đổi thành chuỗi vector biểu diễn ngữ nghĩa (embeddings) bằng **Amazon Titan Text Embeddings**, sau đó lưu trữ vào **Amazon OpenSearch Serverless Vector Engine**.
- **Truy vấn ngữ nghĩa & Tổng hợp câu trả lời (Retrieval & Generation):** Khi người dùng đặt câu hỏi, **Knowledge Bases for Amazon Bedrock** thực hiện vector search trên OpenSearch Serverless để lấy ra các đoạn ngữ cảnh liên quan nhất, gửi kèm prompt đến **Anthropic Claude 3** trên Amazon Bedrock để sinh câu trả lời kèm trích dẫn nguồn (citation).
- **Bảo mật & Phân quyền:** Dữ liệu được mã hóa ở trạng thái nghỉ (at rest) và trên đường truyền (in transit), tuân thủ các tiêu chuẩn bảo mật dữ liệu doanh nghiệp của AWS.

---

## 1. Cấu hình IAM Policy cho Knowledge Base & Lambda

Để **Knowledge Bases for Amazon Bedrock** có thể đọc tài liệu từ S3, gọi mô hình Embedding và ghi/đọc dữ liệu từ OpenSearch Serverless, hãy thiết lập IAM Role với các quyền hạn tối thiểu (least-privilege).

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

## 2. Logic xử lý RAG & Truy vấn: AWS Lambda (Python 3.12)

Lambda function dưới đây nhận câu hỏi từ người dùng thông qua API, gọi API `RetrieveAndGenerate` của Amazon Bedrock để truy vấn Knowledge Base và trả về câu trả lời kèm tài liệu tham khảo.

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

## 3. Tự động hóa Pipeline đồng bộ dữ liệu (Data Ingestion Pipeline)

Khi có tài liệu mới được tải lên Amazon S3, một Amazon EventBridge Rule hoặc S3 Event Notification sẽ kích hoạt một Lambda function phụ để tự động khởi chạy quy trình đồng bộ (Ingestion Job) vào Knowledge Base.

### Cấu hình S3 Event Notification & Lambda Trigger

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

## Kết quả & Phân tích tác động

Chuyển đổi từ cơ chế tra cứu từ khóa truyền thống sang hệ thống Serverless RAG giúp cải thiện vượt bậc cả về hiệu suất làm việc lẫn chi phí vận hành.

| Chỉ số                        | Tra cứu truyền thống (Keyword Search)              | Hệ thống Serverless RAG (Bedrock + OpenSearch)        |
| ----------------------------- | -------------------------------------------------- | ----------------------------------------------------- |
| Độ chính xác câu trả lời      | Thấp (Chỉ trả về đường dẫn file, cần đọc thủ công) | Cao (Trả lời trực tiếp kèm trích dẫn nguồn chuẩn xác) |
| Thời gian tìm kiếm trung bình | ~10 - 15 phút / yêu cầu                            | ~2 - 3 giây / yêu cầu                                 |
| Chi phí hạ tầng cố định       | Chi phí duy trì máy chủ Search Engine 24/7         | $0 khi không có truy vấn (Hoàn toàn Serverless)       |
| Quản lý cụm Vector DB         | Phức tạp (Cấu hình, backup, scale node thủ công)   | Tự động 100% với OpenSearch Serverless (OCU scaling)  |
| Bảo mật dữ liệu               | Rủi ro rò rỉ nếu dùng public AI API                | Dữ liệu được cô lập hoàn toàn trong AWS VPC & Account |

---

## Điểm quan trọng & Best Practice

- **Tối ưu chiến lược Phân đoạn (Chunking Strategy):** Lựa chọn kích thước đoạn tài liệu (chunk size) phù hợp (ví dụ: 300 - 500 tokens với overlap 20%) giúp duy trì đầy đủ ngữ cảnh mà không làm nhiễu thông tin khi vector search.
- **Sử dụng Hybrid Search:** Cấu hình OpenSearch Serverless kết hợp cả Vector Search (Semantic) và Text Search (Keyword) để đạt độ chính xác tối ưu cho các thuật ngữ chuyên ngành hoặc mã định danh.
- **Tối ưu chi phí với OpenSearch Serverless (OCU):** Giám sát chỉ số OpenSearch Compute Units (OCU) để điều chỉnh ngưỡng scaling tối thiểu, tránh phát sinh chi phí cho môi trường Dev/Test khi không sử dụng.
- **Đảm bảo tính chính xác (Hallucination Control):** Điều chỉnh thông số temperature về mức thấp (ví dụ: 0.0 – 0.2) trên model Claude 3 để đảm bảo mô hình chỉ tổng hợp thông tin dựa trên ngữ cảnh được cung cấp trong Knowledge Base.

---

## Tài liệu tham khảo

- [Knowledge Bases for Amazon Bedrock User Guide](https://docs.aws.amazon.com/bedrock/latest/userguide/knowledge-base.html)
- [Amazon OpenSearch Serverless Developer Guide](https://docs.aws.amazon.com/opensearch-service/latest/developerguide/serverless.html)
- [Boto3 Documentation — Bedrock Agent Runtime](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/bedrock-agent-runtime.html)
