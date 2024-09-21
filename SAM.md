[Back](./AWS.md)

# SAM

## SAM - Basics

- SAM = Serverless Application Model
- All the configuration is YAML code or JSON
- Supports anything from CloudFormation: Outputs, Mappings, Parameters, Resources
- Using pre-built serverless applications with AWS Serverless Application Repository (SAR)

## SAM - Structure

- Transform header indicates it's SAM template: `Transform: 'AWS::Serverless-2016-10-31'`
- It accepts the following resources
  - `AWS::Serverless::Function` - Creates a Lambda Function
  - `AWS::Serverless::LayerVersion` - Creates a lambda layered version
  - `AWS::Serverless:Api` - Creates API Gateway
  - `AWS::Serverless::SimpleTable` - Creates DynamoDB table
  - `AWS::Serverless::Application` - Embed applications from S3 bucket
- Package & Deploy: use `sam package` and `sam deploy`

## SAM Accelerate (SAM Sync)

- SAM Accelerate is a set of features to reduce latency while deploying resources to AWS
- Ability to synchronizes code changes to AWS without updating infrastructure (uses service APIs & bypass CloudFormation)

![SAM Accelerate](./assets/63.png)

### Examples

- `sam sync` synchronize code and infrastructure configuration
- `sam sync --code` synchronize code changes without updating infrastructure (bypass CloudFormation, fast update)
- `sam sync --code --resource AWS::Serverless::Function` synchronize only all Lambda functions and their dependencies
- `sam sync --code --resource-id HelloWorldLambdaFunction` synchronize only a specific resource by its ID
- `sam sync --watch` Monitor for file changes and automatically synchronize when changes are detected

## SAM - Lambda Policies

- List of templates to apply permissions to your Lambda functions

```yaml
MyFunction:
  Type: 'AWS::Serverless::Function'
  Properties:
    CodeUri: ${codeuri}
    Handler: hello.handler
    Runtime: python2.7
    # It is the policy below
    Policies:
      - SQSPollerPolicy:
          QueueName: !GetAtt MyQueue.QueueName
```

## SAM and CodeDeploy

- SAM Framework natively uses CodeDeploy to update Lambda functions
- In the SAM Template we can add:
  - `AutoPublishAlias: live` specify the name of the alias (will publish the alias instantly)
  - `DeploymentPreference` Canary, Linear, AllAtOnce (is used when you don't want an instant switch)
  - `Alarms` Alarms that can trigger a rollback
  - `Hooks` Validation Lambda function that are run before & after traffic shifting
    - `PreTraffic`
    - `PostTraffic`

![SAM & CodeDeploy](./assets/71.png)

## SAM - Local Capabilities

- **SAM CLI + AWS Toolkits** allows you to debug your Lambda functions locally, inspect variables, and execute code line-by-line.
- `sam local start-lambda` Start a local endpoint that emulates AWS Lambda service (is **NOT** used for testing a specific lambda funcion)
- `sam local invoke` Invoke lambda function with payload once and quit after invocation completes (is used for testing a specific lambda funcion)
- `sam local generate-event` Generate sample payloads for event sources (S3, SQS, etc...)
- `sam local start-api` Start a local HTTP server that hosts all your functions

## SAM - Multiple Environments

- We use **samconfig.toml** file to have different configurations for your environments (ex: dev, prod)
- Use `sam deploy --config-env dev` to use the defined configuration in the **samconfig.toml** file

![SAM Multiple Environments](./assets/64.png)
