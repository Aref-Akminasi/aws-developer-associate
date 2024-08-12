[Back](./AWS.md)

# AppSync

## AppSync - Usage

- AppSync is a managed service that uses GraphQL (uploading a GraphQL schema is required)
- GraphQL makes it easy for applications to get exactly the data they need
- Ability to have a merged API (combining multiple GraphQL APIs)
- Retrieve data in real-time with WebSocket or MQTT on WebSocket

## AppSync - Integrations

- Fetch data from:
  - DynamoDB
  - Aurora
  - OpenSearch
  - Lambda
  - Public HTTP APIs

## AppSync - Security

- There are four ways you can authorize applications to interact with your AWS AppSync:

  - API key
  - AWS IAM
  - OpenID Connect
  - Congnito User Pools

- For custom domain names & HTTPS, it is recommended to use CloudFront in front of AppSync

# Amplify

## Amplify - Basics

- Create mobile and web applications
- Amplify Studio: build full stack app, front end and backend in the console
- Amplify CLI: configure an Amplify backend with CLI
- Amplify Libraries: Connect your app to existing AWS Services
- Amplify Hosting: Deploy your application

## Amplify - Authentication

- run `amplify add auth`
- Leverages Amazon Cognito

## Amplify - Datastore

- run `amplify add api`
- Leverages Amazon AppSync and Amazon DynamoDB

## Amplify - E2E Testing

- Use the test step to run any test commands at build time in the file **amplify.yml**
- Integrated with Cypress testing framework

# SES

- Simple Email Service

# OpenSearch

- Imagine we have a DynamoDB table, queries only exist by primary key or indexes
- With OpenSearch, you can search any field
- Doesn't support SQL natively but we have a plugin for it

## OpenSearch - Architectures

![DynamoDB and OpenSearch](./assets/74.png)
![CloudWatch Logs and OpenSearch](./assets/75.png)
![KDS and OpenSearch](./assets/76.png)

# Athena

- **Serverless** query service to analyze data stored in S3
- Uses Standard SQL Language to query the files
- Pricing $5.00 per TB of data scanned
- Commonly used with Amazon Quicksight for reporting/dashboards

## Athena - Performance Improvement

- Use columnar data for less scan
- Compress data
- Partition datasets in S3 for easy querying
- Use larger files

## Athena - Federated Query

- Federated query allows you to run SQL queries across data stored in relational, non-relational, and custom data sources
- Uses **Data Sources Connectors** that run on AWS Lambda

# AWS MSK

- MSK = Managed Streaming for Apache Kafka
- Alternative to Amazon Kinesis to stream data
- Data is stored on EBS volumes for as long as you want

## MSK vs KDS

| Feature              | KDS                         | MSK                                   |
| -------------------- | --------------------------- | ------------------------------------- |
| Message Size         | 1MB                         | 1MB default, max is 10MB              |
| Uses                 | Shards                      | Topics with Partitions                |
| Resizing             | Shard splitting and merging | Can only add partitions to a topic    |
| In-flight encryption | TLS in-flight encryption    | PLAINTEXT or TLS in-flight encryption |
| At-rest encryption   | KMS at-rest encryption      | KMS at-rest encryption                |

## MSK - Consumers

- Kinesis Data Analytics for Apache Flink
- AWS Glue
- Lambda
- Applications running on EC2, ECS, EKS

# ACM

- ACM = AWS Certificate Manager
- Manage and deploy SSL/TLS Certificates
- Load TLS certificates on
  - ELB
  - CloudFront
  - API Gateway

# AWS Macie

- Amazon Macie is a fully managed data security and data privacy service that uses **machine learning** and **pattern matching** to discover and protect your sensitive data in AWS (ex: in S3 bucket)
- Macie helps identify and alert you to sensitive data, such as personally identifiable information (PII)

![Macie](./assets/77.png)

# AWS AppConfig

- Deploy dynamic configuration changes to your applications independently of any code deployments
- You don't need to restart the application
- Validate configuration changes before deployment using:
  - JSON Schema
  - Lambda function
