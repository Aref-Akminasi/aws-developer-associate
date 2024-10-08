[Terms](./terms.md)

[Scoping](./Scoping.md)

[Lists](./Lists.md)

# AWS

## What's AWS?

- AWS (Amazon Web Services) is a Cloud Provider
- They provide you with servers and services that you can use **on demand** and **scale easily**

### AWS Regions

- A region is a cluster of data centers
- Region example: Europe (Paris) eu-west-3
- Most AWS services are region-scoped: that means, when we use a service in one region and we try to use it in another region, it would be like a new time of using the service

### AWS Partitions

- In AWS, partitions are separated regions, standard `aws`, china `aws-cn` or GovCloud `aws-us-gov`
- Using **IAM roles** and **resource-based policies** (ex: for S3 bucket) to grant cross-account-access across **different partitions** is not supported

### How to choose an AWS Region for your application?

- Compliance with data governance and legal requirements: data never leaves a region without your explicit permission
- Proximity to customers: reduced latency
- Available services within a region: new services and new features aren't available in every Region
- Pricing: pricing varies from region to region and is transparent in the service pricing page

### Availability Zones

- Each region has many availability zones (usually 3, min is 3, max is 6)
- Each availability zone (AZ) has one or more discrete data centers, each with redundant power, networking, and connectivity
- Availability Zones are separated from each other, so that they're isolated from disasters
- AZs in a region are connected with high bandwidth, ultra-low latency networking **(They're not connected through the internet)**
- Naming convention: us-east-1 (Region) has us-east-1a, us-east-1b, us-east-1c (Availability Zones)

## High Availability & Scalability

- Vertical Scaling: Increase instance size (= scale up / down)
- Horizontal Scaling: Increase number of instances (= scale out / in)
- High Availability: Run something as multi AZ

## AWS CLI Profiles

- You can manage multiple profiles using AWS CLI Profiles
- To create a new profile, in your terminal

  `aws configure --profile <profile-name>`

- By default, AWS in the terminal will use the `default` profile, if you want to use the other profile use:

  `aws <command> --profile <my-another-aws-profile>`

## MFA with CLI

- To use MFA with the CLI, you must create a **temporary session**
- To do so, you must run the `STS GetSessionToken` API call, in the terminal `aws sts get-session-token`

## AWS Limits (Service Quotas)

### Rate Limits

- **DescribeInstances** API for EC2 has a limit of 100 calls per seconds
- **GetObject** on S3 has a limit of 5500 GET per second **per prefix**
- Running **On-Demand** Standard Instances: 1152 vCPU

### Rate Limits Increase

- You can request a service limit increase by **opening a ticket**
- You can request a service limit increase by using the **Service Quotas API** (programmatically request)

### Exponential Backoff

- If you get **ThrottlingException** occasionally: use Exponential Backoff
- For Consistent **ThrottlingException** Errors: request an API throttling limit increase
- Exponential backoff: Retry mechanism starting at a time and doubling each retry (e.g., 100ms, 200ms, 400ms, 800ms, 1600ms).
- Retry mechanism is already included in AWS SDK API calls
- Must implement it yourself if using the AWS API
  - Must only implement the retries on **throttling** 4xx or 5xx errors (ex: client 429 throttling error)

## AWS Default credentials provider chain

1. Command Line Options (ex: --profile)
2. Environment Variables
3. ...
4. ...
5. ...
6. EC2 Instance Profile Credentials (IAM Roles)

- If an application on an EC2 instance uses environment variables for IAM user credentials (bad practice) with full S3 access, it will override the instance profile's limited permissions (IAM Role). To fix this, unset the environment variables to enforce the use of the instance profile.

## AWS Signature v4 signing (SigV4)

- You should **sign an AWS HTTP request** for **authorization** using **Signature v4 (SigV4)** as one of the following methods:
  - HTTP Header (signature in **Authorization** header)
  - Query String (signature included in the URL, using the **X-Amz-Signature** query parameter)

## Common Error Codes

- 401/403 - Returned when wrong user credentials are entered for login / no token is used for authorization
- 403 - S3, make sure the bucket policy allows public reads (This is not a CORS Error)
- 404 - Not Found
- 429 - Throttling Error (use exponential backoff)
- 502 - output format of a Lambda function in a proxy integration is in a different format
- 503 - Service unavailable (When an ELB doesn't have any target groups)
- 504 - API Gateway Timeout
- 505 - HTTP version is not supported by the server

## Edge Locations

AWS edge locations are data centers located worldwide that help deliver content quickly and efficiently to users.

## Topics

[IAM](./IAM.md)

[EC2](./EC2.md)

[ELB](./ELB.md)

[ASG](./ASG.md)

[SSL/TLS](./SSL-TLS.md)

[RDS](./RDS.md)

[ElastiCache](./ElastiCache.md)

[Route 53](./Route%2053.md)

[VPC](./VPC.md)

[S3](./S3.md)

[CloudFront](./CloudFront.md)

[ECS](./ECS.md)

[Elastic Beanstalk](./ElasticBeanstalk.md)

[CloudFormation](./CloudFormation.md)

[SQS & SNS](./SQS&SNS.md)

[Kinesis](./Kinesis.md)

[CloudWatch](./CloudWatch.md)

[X-Ray](./xray.md)

[EventBridge](./EventBridge.md)

[CloudTrail](./CloudTrail.md)

[Lambda](./Lambda.md)

[DynamoDB](./DynamoDB.md)

[API Gateway](./APIGateway.md)

[CICD](./CICD.md)

[SAM](./SAM.md)

[CDK](./CDK.md)

[Cognito](./Cognito.md)

[Step Functions](./stepfunctions.md)

[AWS Security](./Security.md)

[Other](./Other.md)
