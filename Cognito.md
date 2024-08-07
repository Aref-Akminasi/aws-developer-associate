[Back](./AWS.md)

# Cognito User Pools

## CUP - Basics

- Create a serverless database of users for your web & mobile apps
- Simple login: Username/Password
- Password reset
- Email & PHone number Verification
- MFA
- Federated Identities: login with Facebook, Google, SAML

## CUP - Integrations

- CUP integrates with:
  - API Gateway
  - Application Load Balancer

![CUP Integrations](./assets/69.png)

## CUP - Lambda Triggers

- CUP can invoke a lambda function synchronously on triggers such as:
  - Authentication Events: event logging for custom analytics
  - Sign-up: Custom welcome message
  - Messages
  - Token Creation

## CUP - Hosted UI Customs

- You can customize the logo and the CSS
- For custom domains, you must create an ACM certificate in us-east-1
- The custom domain must be defined in the **App Integration Section**

## CUP - Adaptive Authentication

- Cognito examines each sign-in attempt and generates a risk score for how likely the sign-in request is to be from a malicious attacker based on same device, location or IP
- Users are prompted for a second MFA only when risk is detected
- Adaptive Authentication has integration with CloudWatch Logs (sign-in attempts, risk score, failed challenges)

## CUP - JSON Web Token

- Login sends back a JWT
- Base64 encodes that contains:
  - Header
  - Payload
  - Signature

# Cognito Identity Pools (Federated Identities)

## CIP - Basics

- Users can be unauthenticated (guests) - in this case no Cognito User Pool needed
- Default IAM roles for authenticated and guest users
- Get identities for "users" so they obtain temporary AWS credentials
- Users can then access AWS services directly or through API Gateway, the IAM Policies applied to the credentials are defined in Cognito
- IAM credentials are obtained by CIP through STS
- The roles must have a trust policy of CIP

- Auth with 3rd party services happening via CUP

![CIP](./assets/66.png)

- Auth with 3rd party services not happening via CUP

![CIP](./assets/68.png)

## CIP - Policy variable

- You can partition you users acess using **policy variables**
- Example: giving access to users access to their own files in a prefix in an S3 bucket or DynamoDB LeadingKey

![policy variable](./assets/67.png)

## CUP vs CIP

- CUP is used for authentication = identity verification
- CUP grants access to AWS resources through API Gateway or ALB
- CUP returns a token to the user
- CIP is used for authorization = access control
- CIP grants access to more AWS resources directly with an IAM Role given to the user
- CIP returns IAM credentials to the user
