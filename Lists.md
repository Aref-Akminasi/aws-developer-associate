[Back](./AWS.md)

### The following supports SNI

- ALB
- NLB
- CloudFront

### ALB Target Groups

- EC2 instances
- Lambda functions
- Private IP Addresses
- ECS tasks

### NLB Target Groups

- EC2 instances
- Private IP Addresses
- Application Load Balancer
- ECS Tasks

### GWLB Target groups

- EC2 instances
- Private IP Addresses

### Supported database engines on RDS

- PostgreSQL
- MySQL
- MariaDB
- Oracle
- Microsoft SQL Server
- IBM DB2
- Aurora (Supports MySQL and PostgreSQL)

### RDS proxy Supports

- MySQL
- PostgreSQL
- MariaDB
- Microsoft SQL Server
- Aurora (Supports MySQL and PostgreSQL)

### Alias AWS resources Record Targets

- ELB
- API Gateway
- VPC Endpoints
- S3 Websites (not S3 buckets)
- CloudFront Distribution
- Elastic Beanstalk environments
- Global Accelerator accelerator
- Route 53 record in the same hosted zone

### VPC Flow logs can be sent to

- S3
- CloudWatch Logs
- Kinesis Data Firehose

### CloudFront Origins (Content Sources)

- ALB
- EC2
- S3
- Any HTTP backend you want

### ECS - Data Volumes

- Not S3
- EFS
- EBS
- Bind mounts

### EKS Data Volumes

- EBS
- EFS
- FSx for Lustre
- FSx for NetApp ONTAP

### SNS Types of subscribers

- Emails
- SMS & Mobile Notifications
- HTTP(s) endpoints
- SQS
- Lambda
- Kinesis Data Firehose (NOT Kinesis Data Streams)

### KDS Producers

- AWS SDK
- Kinesis Producer Library (KPL)
- Kinesis Agent
- and more...

### KDS Consumers

- Kinesis Client Library (KCL)
- AWS SDK
- AWS Lambda
- Kinesis Data Firehose
- Kinesis Data Analytics

### KDF Consumers

- S3
- Redshift
- OpenSearch
- HTTP(s) endpoint
- 3rd party parnter: MongoDB, DataDog...
- Note: Lambda is not a consumer

### KDA for SQL applications Producers

- Kinesis Data Streams
- Kinesis Data Firehose

### KDA for SQL applications Consumers

- Kinesis Data Streams
- Kinesis Data Firehose

### Kinesis Data Analytics for Apache Flink Producers

- Kinesis Data Streams
- Amazon MSK

### CloudWatch Logs Sources

- CloudWatch Logs Agent (on EC2 or on Premises Server)
- CloudWatch Unified Agent (on EC2 or on Premises Server)
- And more...

### CloudWatch Logs Targets

- Amazon S3 (Batch export)
- AWS Lambda
- OpenSearch
- Kinesis Data Firehose
- Kinesis Data Streams

### Alarms Targets

- EC2 Instances (trigger stop, trigger terminate, trigger reboot)
- ASG (Trigger Auto Scaling)
- SNS
- Trigger a lambda function

### X-Ray - Compatibility

- AWS Lambda
- API Gateway
- Any application server on premises
- EC2
- ECS
- ELB
- Elastic Beanstalk

### OpenTelemetry destinations

- AWS X-Ray
- Amazon CloudWatch
- Amazon Managed Service for Prometheus
- Partner Monitoring Solutions

### CloudTrail - Targets

- CloudWatch Logs
- S3
- EventBridge

### CloudTrail Insights Events - Targets

- CloudTrail console
- S3
- EventBridge

### Lambda - Storage Options

- Not EBS
- Ephemeral /tmp
- S3
- EFS

### Lambda - Destinations

- SQS
- SNS
- EventBridge
- Lambda

### Lambda - Synchronous Invocation

- CLI
- SDK
- API Gateway
- Application Load Balancer
- CloudFront (Lambda@Edge)
- Cognito User Pool
- and more...

### Lambda - Asynchronous Invocations

- S3
- SNS
- EventBridge
- and more...

### Lambda - Event Source Mapping

- Kinesis Data Streams
- SQS & SQS FIFO queue
- DynamoDB Streams
- and more...

### API Gateway - Targets

- Lambda Function: invoke Lambda Function
- HTTP endpoint
- AWS Service: ex: start an AWS Step function workflow, send a message to SQS

### CUP - Access to AWS resources

- API Gateway
- ALB

### Stepfunctions - Start workflow via

- AWS Console
- SDK
- API Gateway
- EventBridge

### AppSync - Fetch data from

- DynamoDB
- Aurora
- OpenSearch
- Lambda
- Public HTTP APIs

### AppSync - Security

- API key
- AWS IAM
- OpenID Connect
- Congnito User Pools

### OpenSearch usages

- Saving/Searching DynamoDB table data (search OpenSearch and retrieve data from the DynamoDB table)
- Saving/Searching CloudWatch logs
- Saving/Searching KDS and KDF data

### MSK - Consumers

- Kinesis Data Analytics for Apache Flink
- AWS Glue
- Lambda
- Applications running on EC2, ECS, EKS

### ACM - Load TLS certificates on

- ELB
- CloudFront
- API Gateway
