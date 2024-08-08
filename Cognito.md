[Back](./AWS.md)

# Cognito User Pools

## CUP - Basics

- Create a **serverless database** of users for your apps

### Features

- Simple login: Username/Password
- Password reset
- Email & Phone number Verification
- MFA
- Federated Identities: login with Facebook, Google, SAML

## CUP - Integrations

### API Gateway

### Application Load Balancer

- Your application load balancer can securely authenticate users
- Authenticate users through:
  - Identity provider (IdP): OpenID Connect (OIDC) compliant
  - Cognito User Pools
- Must use an HTTPS listener
- **OnUnauthenticatedRequest** options:
  - authenticate (default): ask the user to authenticate
  - deny
  - allow

![CUP Integrations](./assets/69.png)

## CUP - Lambda Triggers

- CUP can invoke a lambda function **synchronously** on triggers such as:
  - Authentication Events: event logging for custom analytics
  - Sign-up: Custom welcome message
  - Messages
  - Token Creation

## CUP - Hosted UI Customs

- You can customize the logo and the CSS on the hosted UI
- For custom domains Hosted UI, you must create an **ACM certificate** in **us-east-1**
- The custom domain must be defined in the **App Integration** section

## CUP - Adaptive Authentication

- Cognito examines each sign-in attempt and generates a risk score for how likely the sign-in request is to be from a malicious attacker based on same device, location or IP
- Users are prompted for a second MFA only when risk is detected
- Adaptive Authentication has integration with **CloudWatch Logs** (sign-in attempts, risk score, failed challenges)

## CUP - JSON Web Token

- Login sends back a JWT
- Base64 encodes that contains:
  - Header
  - Payload
  - Signature

# Cognito Identity Pools

## CIP - Basics

- Users can be unauthenticated (guests) - in this case no Cognito User Pool needed
- Users can then access AWS services directly or through API Gateway
- IAM credentials are obtained by CIP through STS
- The roles must have a trust policy of CIP

### Auth with 3rd party services happening via CUP

![CIP](./assets/66.png)

### Auth with 3rd party services not happening via CUP

![CIP](./assets/68.png)

## CIP - Policy variable

- You can partition you users acess using **policy variables**
- Example: giving access to users access to their own files in a prefix in an S3 bucket or DynamoDB LeadingKey

![policy variable](./assets/67.png)

## CUP vs CIP

| Feature                     | Cognito User Pools (CUP)                                                             | Cognito Identity Pools (CIP)                                           |
| --------------------------- | ------------------------------------------------------------------------------------ | ---------------------------------------------------------------------- |
| **Primary Use**             | Authentication (Identity Verification)                                               | Authorization (Access Control)                                         |
| **Access to AWS Resources** | Through API Gateway or ALB                                                           | Directly access to AWS resources (ex: S3, DynamoDB) or via API Gateway |
| **Return Value**            | Token                                                                                | IAM Credentials                                                        |
| **guests**                  | Support via OnUanauthenticatedRequest on the load balancer (but use CIP in the exam) | Native Support                                                         |
