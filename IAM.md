[Back](./AWS.md)

# IAM: Users & Groups

- IAM = Identity and Access Management
- IAM is a global service, so if we create a user it would be available everywhere
- Users are people within your organization, and can be grouped
- Groups **only** contain users, not other groups
- Some users doesn't have to belong to a group (but it's not the best practice)
- A user can belong to multiple groups

## IAM Permissions

- Users or Groups can assigned JSON documents called policies
- These policies define the permissions of the users
- In AWS you apply the **least privilege principle:** don't give more permissions than a user needs
- If a user doesn't belong to a group you could provide **inline policy** which is a policy only attached to a user
- Premissions are inherited from the group, example: if a user is part of the group 'admin' it will get the admin permissions (Policies inheritance)

## IAM Policies Structure

```json
{
  "Version": "2012-10-17",
  "Id": "S3-Account-Permissions", //optional
  "Statement": [
    //required one or more individual statements
    {
      "Sid": "1", //optional
      "Effect": "Allow", //allow or deny
      "Principal": {
        "AWS": ["arn:aws:iam::123456789012:root"] //account/user/role to which this policy is applied to
      },
      "Action": ["s3:GetObject", "s3:PutObject"], //list of actions this policy allows or denies
      "Rresource": ["arn:aws:s3::mybucket/*"] //list of resources to which the actions is applied to
      //Condition: We can add conditions here for when this policy is in effect (optional)
    }
  ]
}
```

## IAM Password Policy

- In AWS, you can setup a password policy:

  - minimum length
  - specific character types
  - allow IAM users to change their password or not
  - Require users to change their password after some time (password expiration)
  - Prevent password re-use (a password an user had before)
  - Changing the password policy (such as changing the minimum length) doesn't require users to change their old passwords immediately. Instead, they will have to meet the new password policy when their passwords expire.

- Multi Factor Authentication - MFA

  - MFA = password you know + security device you own (for generating tokens)

- MFA devices options in AWS
  - Virtual MFA device: Google Authenticator / Authy
  - Universal 2nd Factor (U2F) Securtiy Key (Physical key)
  - Hardware Key Fob MFA Device (Physical key)
  - Hardware Key Fob MFA Device for AWS GovCloud (US) (Physical key)

## IAM CLI & SDK

- AWS CLI: manage your AWS services using the command-line
  - AWS CLI requires Python as its runtime
- AWS SDK: manage your AWS services using a programming language
  - if you don't specify or configure a default region, then us-east-1 will be chosen by default

## AWS CloudShell

- Note: CloudShell is not available in all the regions
- CloudShell is a terminal in the cloud of AWS
- It will operate (as if the credentials has been used of the account)

## IAM Roles

- An IAM role is an identity you can create that has specific permissions with credentials that are valid for short durations. Roles can be assumed by entities that you trust. (It's sometimes called EC2 Instance Profile)
- To enable AWS services to perform actions on your behalf, assign permissions to these services using IAM Roles.
- You **cannot** directly assign an IAM role to an on-premises server. IAM roles are specifically designed for AWS resources.
- When we use an IAM Role the instance uses the **instance metadata** to get **temporarily** credentials
- Note: IAM role can **NOT** be assigned as a principal to a policy

## IAM Security Tools

- IAM Credentials Report (account-level): a report that lists all your account's users and the status of their various credentials
- IAM Access Advisor (user-level): Access advisor shows the service permissions granted to a user and when those services were last accessed: You can use this information to revise your policies

## Shared Responsibility Model for IAM

| AWS Responsibilities                     | Your Responsibilities                                    |
| ---------------------------------------- | -------------------------------------------------------- |
| Infrastructure (global network security) | Users, Groups, Roles, Policies management and monitoring |
| Configuration and vulnerability analysis | Enable MFA on all accounts                               |
| Compliance validation                    | Rotate all your keys often                               |
|                                          | Use IAM tools to apply appropriate permissions           |
|                                          | Analyze access patterns & review permissions             |

## IAM Tips

- Don't use the **root** account except for AWS account setup
- Assign users to groups and assign permissions to groups
- Policies: JSON document that outlines permissions for users or groups
- Create and use **Roles** for giving permissions to AWS services
- Access Keys: access AWS using the CLI or SDK
- Audit permissions of your account using IAM Credentials Report & IAM Access Advisor
