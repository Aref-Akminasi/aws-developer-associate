[Back](./AWS.md)

# Security

# KMS

## KMS - Basics

- Able to audit KMS key usage using CloudTrail
- KMS keys are scoped to a region
- We can encrypt any secret, like environment variables in a lambda function, or SSM Paramter Store
- Services must have appropriate IAM premissions for calling KMS
- Use **Encrypt** and **Decrypt** APIs

## KMS - Keys Types

### Symmetric (AES-256 keys)

- Single encryption key that is used to Encrypt and Decrypt
- AWS services that are integrated with KMS use symmetric
- You never get access to the KMS key (must call KMS API to use)

### Asymmetric (RSA & ECC key pairs)

- Public (Encrypt) and Private Key (Decrypt) pair
- The public key is downloadable, but you can't access the private key
- Use case: encryption outside of AWS by users who can't call the KMS API

## KMS - Key Management

| Feature            | AWS Owned Keys (not part of KMS service) | AWS Managed Keys              | Customer Managed Keys created in KMS                | Customer Managed Keys Imported            |
| ------------------ | ---------------------------------------- | ----------------------------- | --------------------------------------------------- | ----------------------------------------- |
| Example            | SSE-S3, SSE-SQS, SSE-DDB (default key)   | aws/service-name, ex: aws/rds | User-created keys in KMS                            | User-imported keys in KMS                 |
| Cost of the Key    | Free                                     | Free                          | $1/Month per key                                    | $1/Month per key                          |
| Cost of API calls  | Free                                     | $0.03/10000 calls             | $0.03/10000 calls                                   | $0.03/10000 calls                         |
| Key Policy Control | No Control                               | No Control                    | Full control                                        | Full control                              |
| Key Rotation       | Managed by AWS                           | automatic every 1 year        | must be enabled, automatic & on-demand (rotate now) | only manual rotation possible using alias |

## KMS - Copying Snapshots across regions

- KMS keys are scoped to a region
- Snapshot copied to a new region will be encrypted using a new key (AWS will do this for you)

![Keys regions](./assets/79.png)

## KMS - Key Policies

### Default KMS Key Policy

- Created if you don't provide a specific KMS Key Policy
- Complete access to the key to the entire AWS account
- A user must have the proper IAM permissions to access the key even if the KMS key policy allows every one
- If the policy explicitly allow a user in the principal, the user doesn't need an IAM policy
- You cannot use IAM policies to allow access to a KMS key. Without permission from the key policy

### Custom KMS Key Policy

- Define users, roles that can access the KMS key
- Useful for cross account access of your KMS key

## KMS - Envelope Encryption

- KMS Encrypt API call has a limit of **4KB**
- If you want to encrypt **> 4KB**, we need to use **Envelope Encryption** method
- For Envelope Encryption we should use **GenerateDataKey API** that will return a **DEK (Data Encryption Key)** that we can use it to encrypt locally and send file back
- **GenerateDataKeyWithoutPlaintext API** to use DEK at some point (not immediately)
- The AWS Encryption SDK implement Envelope Encryption for us
- The Encryption SDK has DEK caching mechanism to reduce requests to KMS

## KMS - Limits

- All operations (encrypt/decrypt) share the same quota (same limit)
- When you exceed the quota you will get **ThrottlingException**

### Throttling Strategies

- To respond, use exponential backoff
- For GenerateDataKey API, consider using DEK caching from the encryption SDK
- You can request a request quota increase through API or AWS support

# CloudHSM

- ClousHSM = Hardware Security Model
- If we use CloudHSM AWS will provision encryption dedicated hardware
- You manage your own encryption keys entirely (not AWS)
- Supports both symmetric and asymmetric encryption
- Must use the CloudHSM Client Software for encryption

# SSM Parameter Store

## SSM - Basics

- Secure storage for configuration and secrets
- KMS encryption is optional
- Security through IAM
- Has version tracking to see previous versions
- Make sure the service consuming the parameter has access to SSM and to KMS if the secret is encrypted (in order to decrypt it)
- No secret rotation (can enable rotation using Lambda triggered by EventBridge every X days)

## SSM - Integrations

- Notifications with Amazon EventBridge (ex: notify the user for parameters that will soon expire)
- Integration with CloudFormation to reference parameters using the `resolve` keyword ex: `{{resolve:ssm:S3AccessControl:2}}`

## SSM - Store Hierarchy

- SSM has store hierarchy helps you define your parameters in a organized way and simplify IAM policies specific to a path

```
/my-department/
    my-app/
        dev/
            db-url
            db-password
        prod/
            db-url
            db-password
    other-app/
```

## SSM Standard vs SSM advanced

| Feature                | Standard | Advanced |
| ---------------------- | -------- | -------- |
| Total nr of parameters | 10000    | 100000   |
| Max size of parameter  | 4KB      | 8KB      |
| Parameter Policy (TTL) | No       | Yes      |
| Free                   | Yes      | No       |

# AWS Secrets Manager

- Meant for storing secrets
- KMS encryption is mandatory
- Capability to force rotation of secrets every X days (via a lambda function)
- It has a tight integration with RDS, Aurora
- Ability to replicate secrets across multiple AWS regions (for disaster recovery)
- Secrets Manager keeps read replicas in sync with the primary secret
- Ability to promote read replica secret to standalone secret
- Integration with CloudFormation to reference parameters using the `resolve` keyword ex: `{{resolve:ssm:S3AccessControl:2}}`
