[Back](./AWS.md)

# AppSync

## AppSync - Usage

- AppSync is a managed service that uses GraphQL (uploading a GraphQL schema is required)
- GraphQL makes it easy for applications to get exactly the data they need
- Ability to have a merged API (combining multiple GraphQL APIs)
- Retrieve data in real-time with WebSocket or MQTT on WebSocket

## AppSync - Integrations

- Fetch data from:
  - DynamoDB
  - Aurora
  - OpenSearch
  - Lambda
  - Public HTTP APIs

## AppSync - Security

- There are four ways you can authorize applications to interact with your AWS AppSync:

- API key
- AWS IAM
- OpenID Connect
- Congnito User Pools

- For custom domain names & HTTPS, it is recommended to use CloudFront in front of AppSync

# Amplify

## Amplify - Basics

- Create mobile and web applications
- Amplify Studio: build full stack app, front end and backend in the console
- Amplify CLI: configure an Amplify backend with CLI
- Amplify Libraries: Connect your app to existing AWS Services
- Amplify Hosting: Deploy your application

## Amplify - Authentication

- run `amplify add auth`
- Leverages Amazon Cognito

## Amplify - Datastore

- run `amplify add api`
- Leverages Amazon AppSync and Amazon DynamoDB

## Amplify - E2E Testing

- Use the test step to run any test commands at build time in the file **amplify.yml**
- Integrated with Cypress testing framework
