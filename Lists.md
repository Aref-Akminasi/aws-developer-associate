[Back](./AWS.md)

### Support CORS

- Lambda function URL
- API Gateway
- S3

### The following supports SNI

- ALB
- NLB
- CloudFront

### ALB Target Groups

- EC2 instances
- Lambda functions
- Private IP Addresses
- ECS tasks

### ALB Routing

- Routing tables to different **target groups**

  - Routing based on path in URL (`example.com/users` & `example.com/posts`)
  - Routing based on hostname in URL (`one.example.com` & `other.example.com`)
  - Routing based on Query String/Query Parameters (`example.com/users?id=123&order=false`)
  - Routing based on HTTP Headers
  - Routing based on a cookie value (ex: `version=beta` this will route to the beta target group)
  - Routing based on Source IP address

- Routing based on Geo-location is **not** possible

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

### RDS Logs

- Error logs
- General query log
- Slow query log

### Alias AWS resources Record Targets

- ELB
- API Gateway
- VPC Endpoints
- S3 Websites (not S3 buckets)
- CloudFront Distribution
- Elastic Beanstalk environments
- Global Accelerator accelerator
- Route 53 record in the same hosted zone

### Route 53 - Routing Policies

- IP-based Routing: IP or IP range
- Simple
- Multi-value
- Weighted
- Latency
- Failover
- Geolocation
- Geo-proximity

### VPC Flow logs can be sent to

- S3
- CloudWatch Logs
- Kinesis Data Firehose

### S3 Event Notifications

- Lambda
- SQS
- SNS

### CloudFront Origins (Content Sources)

- ALB
- EC2
- S3
- Any HTTP backend you want

### CloudFront - Cache Policy

- HTTP Headers
- Cookies
- Query Strings

### CloudFront - Origin Request Policy

- HTTP Headers
- Cookies
- Query Strings

### ECS - Task Definitions

- Image Name
- IAM Role
- Port binding for Container and Host
- Environment variables (Hardcoded, reference to SSM Parameter store or AWS secrets manager)
- Memory and CPU required
- Networking information
- Logging configuration (ex: CloudWatch)

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
- 3rd party partner: MongoDB, DataDog...
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

### X-Ray - Required segment fields

- Name
- Id
- Trace_id
- Start_time
- End_time
- In_progress

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

### Lambda - Synchronous Invocations

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

### DynamoDB Streams records can be sent to

- Kinesis Data Streams
- Kinesis Client Library (KCL)
- AWS Lambda

### API Gateway - Targets

- Lambda Function: invoke Lambda Function
- HTTP endpoint
- AWS Service: ex: start an AWS Step function workflow, send a message to SQS

### API Gateway - Correct order for API keys and usage plans

1. Create one or more APIs
2. Configure the methods to require an API key
3. Deploy API to stages
4. Generate or import API keys to distribute
5. Create the usage plan with the desired throttle and quota limits
6. Associate API stages and API keys with the usage plan

### CodeDeploy - Deploy new applications versions to

- EC2 Instances
- ECS
- Lambda functions
- On-premises server

### CodeDeploy - lifecycle for EC2

1. ApplicationStop
2. DownloadBundle
3. BeforeInstall
4. Install
5. AfterInstall
6. ApplicationStart
7. ValidateService

### CodeDeploy lifecycle for Lambda

- BeforeAllowTraffic (supports lambda validation functions)
- AllowTraffic
- AfterAllowTraffic (supports lambda validation functions)

### CUP - Access to AWS resources

- API Gateway
- ALB

### CUP - Lambda Triggers

- Authentication Events
- Sign-up
- Messages
- Token Creation

### Step functions - Start workflow via

- AWS Console
- SDK
- API Gateway
- EventBridge

### Step Functions - States

- Choice
- Parallel
- Map: Dynamically iterate steps
- Pass
- Wait
- Success
- Fail

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
- Cognito User Pools

### OpenSearch usages

- Saving/Searching DynamoDB table data (search OpenSearch and retrieve data from the DynamoDB table)
- Saving/Searching CloudWatch logs
- Saving/Searching KDS and KDF data

### Athena - Performance improvement

- Use columnar data
- Partition datasets in S3
- Use larger files
- Compress data

### MSK - Consumers

- Kinesis Data Analytics for Apache Flink
- AWS Glue
- Lambda
- Applications running on EC2, ECS, EKS

### ACM - Load TLS certificates on

- ELB
- CloudFront
- API Gateway

### Support for WebSocket

- ALB
- API Gateway
- AppSync
