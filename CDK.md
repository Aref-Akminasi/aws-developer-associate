[Back](./AWS.md)

# CDK

## CDK - Basics

- CDK let's you define your cloud infrastructure using a programming language: JavaScript/TypeScript, python, Java and .NET
- The code is **compiled** into a CloudFormation template (JSON/YAML) using `cdk synth`

## CDK vs SAM

| Feature                   | SAM                                               | CDK                                                                                            |
| ------------------------- | ------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Services                  | Serverless focused                                | All AWS Services                                                                               |
| Language                  | Write your template declaratively in JSON or YAML | Write infrastructure in a programming language (JavaScript/TypeScript, Python, Java, and .NET) |
| Infrastructure Management | Leverages CloudFormation                          | Leverages CloudFormation                                                                       |

## CDK - Testing Locally via SAM

- You can use SAM CLI to locally test your CDK apps
- you must first run **cdk synth**

![cdk + sam](./assets/65.png)

## CDK - Constructs

Constructs are the basic building blocks of AWS Cloud Development Kit (AWS CDK) applications. A construct is a component within your application that represents one or more AWS CloudFormation resources and their configuration. You build your application, piece by piece, by importing and configuring constructs.

### Construct Libraries

- AWS Construct Library: a collection of constructs included in AWS CDK (L1,L2,L3)
- Construct Hub: contains additional constructs from AWS 3rd parties

### Layer 1 Constructs

- These are low-level constructs that directly correspond to AWS CloudFormation resources.
- Construct names starts with **Cfn** (ex: CfnBucket)
- You must explicity configure all resource properties that are required such as a bucket name for a S3 bucket

### Layer 2 Constructs

- Represents AWS resources but with a higher level
- Similar functionality as L1 but with convenient defaults, methods and boilerplate

### Layer 3 Consrtucts

- Can be called **patterns**, which represents multiple related resources
- Examples: build api gateway with lambda or ECS with fargate with minimal effort

## CDK - Commands

- `cdk bootstrap` You have to run this command for every region and will leverage a **S3 Bucket** to store files and **IAM Roles** to grant permissions for deployments
- `cdk synth` Synthesizes and prints the CloudFromation template
- `cdk deploy` Deploy a stack
- `cdk diff` View differences of local CDK and deployed Stack
- `cdk destroy` Destroy the Stack

## CDK - Testing

- To test CDK apps, use **CDK Assertions Module**
- Two Types of tests:
  - Fine-grained Assertions (common): test specific aspects of the CloudFormation template
  - Snapshot Tests: test the template against a previously stored template
- To import a template in the test file use:
  - Template.fromStack(MyStack): if the stack is built in CDK
  - Template.fromString(mystring): stack build outside CDK (ex: a file)
