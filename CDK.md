[Back](./AWS.md)

# CDK

## CDK - Basics

- CDK let's you define your cloud infrastructure using a programming language: JavaScript/TypeScript, python, Java and .NET
- The code is "compiled" into a CloudFormation template (JSON/YAML) through **cdk synth**

## CDK vs SAM

| Feature                   | SAM                                               | CDK                                                                                            |
| ------------------------- | ------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Primary Focus             | Serverless focused                                | All AWS Services                                                                               |
| Template Definition       | Write your template declaratively in JSON or YAML | Write infrastructure in a programming language (JavaScript/TypeScript, Python, Java, and .NET) |
| Infrastructure Management | Leverages CloudFormation                          | Leverages CloudFormation                                                                       |

## CDK + SAM

- You can use SAM CLI to locally test your CDK apps
- you must first run **cdk synth**

![cdk + sam](./assets/65.png)

## CDK Constructs

- CDK Construct is a component that encapsulates everything CDK needs to create the final CloudFormation stack
- Can represent a single AWS resource or multiple related resources
- AWS Construct Library: a collection of constructs included in AWS CDK (L1,L2,L3)
- Construct Hub: contains additional constructs from AWS 3rd parties

### Layer 1 Constructs

- Can be called CFN Resources which represents all resources directly available in CloudFormation
- Construct names starts with **Cfn** (ex: CfnBucket)
- You must explicity configure all resource properties that are required such as a bucket name for a S3 bucket

### Layer 2 Constructs

- Represents AWS resources but with a higher level
- Similar functionality as L1 but with convenient defaults and boilerplate
- Provide methods that make it simpler to work with the resource

### Layer 3 Constucts

- Can be called **patterns**, which represents multiple related resources
- Examples: build api gateway with lambda or ECS with fargate with minimal effor

## CDK - Commands

- `cdk bootstrap` The process of provisioning resources for CDK before you can deploy CDK apps into AWS Environment. you have to run this command for every region and will leverage a S3 Bucket to store files and IAM Roles to grant permissions for deployments
- `cdk synth` Synthesizes and prints the CloudFromation template
- `cdk deploy` Deploy a stack
- `cdk diff` View differences of local CDK and deployed Stack
- `cdk destroy` Destroy the Stack

## CDK - Testing

- To test CDK apps, use CDK Assertions Module
- Two Types of tests:
  - Fine-grained Assertions (common): test specific aspects of the CloudFormation template
  - Snapshot Tests: test the template against a previously stored template
- To import a template in the test:
  - Template.fromStack(MyStack): if the stack is built in CDK
  - Template.fromString(mystring): stack build outside CDK (ex: a file)
