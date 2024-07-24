[Back](./AWS.md)

# CloudTrail

- Provides **governance**, **compliance** and **audit** for your AWS account
- Get a history of API calls made within you AWS account by users / services
- Events are stored for **90 days**

## CloudTrail - Targets

- Can send events from CloudTrail into:
  - CloudWatch Logs
  - S3
  - EventBridge

## CloudTrail - Events Types

### Management Events

- Management Events: Operations that are performed on resources in you AWS account

### Data Events

- Data Events: By default, data events are not logged (because of high volume operations)
- In order to capture data events in CloudTrail, you need to specifically enable them for each individual data resource

### Insights Events

- Continous analysis of management events to detect unusual activity (bursts of IAM actions, hitting service limits, gaps in periodic maintenance...) Insights events are logged only when CloudTrail detects changes in your account's API usage that differ significantly from the account's typical usage patterns.
- Ability to send CloudTrail Insights Events to:
  - CloudTrail console
  - S3
  - EventBridge

## CloudTrail - Events Retention

- Events are stored for 90 days in CloudTrail
- To keep events beyond this period, send them to S3
- Use **Athena** to analyze CloudTrail events stored in a S3 bucket

## CloudTrail - Inegration with EventBridge (Example)

- API Call (ex: DeleteTable on DynamoDB) -> CloudTrail -> EventBridge -> SNS Topic (Send an email alert)

## EventBridge vs CloudTrail

| EventBridge                                    | CloudTrail                                |
| ---------------------------------------------- | ----------------------------------------- |
| provides event streaming                       | provides a historical record of API calls |
| events are sent to targets defined in the rule | events are just stored in CloudTrail      |
| Used to route an event to another service      | Used for governace, compliance and audit  |
