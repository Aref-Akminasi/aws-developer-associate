[Back](./AWS.md)

# Scoping

## Global Scope

- IAM
- Route 53
- CloudFront

## Within AZ, Cross AZ or Cross Region

- RDS Read Replicas

## Multi-AZ (same region) Scope

- AMI
- Security Group
- EFS
- ALB (by default, and can be disabled at the target group level)
- RDS
- VPC
- S3 Access Logs
- ASG
- SQS Queue
- SNS Topic
- Kinesis Data Streams
- CloudWatch logs
- Lambda Function

## Availability Zone Scope

- EBS
- EC2
- NLB (Can be enabled to support multi AZ)
- Subnets