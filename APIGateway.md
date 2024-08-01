[Back](./AWS.md)

# API Gateway

## API Gateway - Timeout

- The maximum integration timeout for AWS API Gateway is 29 seconds. This is the time limit for the backend integration (e.g., Lambda function, HTTP endpoint) to respond to the API Gateway.
- If the Integration didn't process the request and return a response in 29 seconds, API Gateway will timeout
- Error 504: Integration Failure if API Gateway requests time out after 29 second maximum

## API Gatway - APIs

- HTTP API, much cheaper APIs, doesn't support resource policies
- Rest API, doesn't support Native OpenID Connect / OAuth2.0 / JWT
- WebSocket API

## API Gateway - Integration Types

### Lambda Function

- Invoke Lambda Function
- Easy way to expose REST API backed by AWS Lambda

### HTTP endpoint

- Expose HTTP endpoints in the backend
- Example: Internal HTTP API on premise, ALB
- Why? add rate limiting, caching, user authentications, API keys, etc...

### AWS Service

- Expose any AWS API through API Gateway
- Example: start an AWS Step function workflow, send a message to SQS
- Why? Add authentication, deploy publicly, rate control

## API Gateway - Endpoint Types

### Edge-Optimized (default)

- For global clients
- Requests are routed through CloudFront Edge Locations
- The API Gateway still lives in only one region

### Regional

- For clients within the same region
- Could manually combine with CloudFront (more control over the caching strategies and the distribution)

### Private

- Can only be accessed from your VPC using an interface VPC endpoint (ENI)
- Use a resource policy to define access

## API Gateway - Security

### IAM Permissions

- Create an IAM Policy authorization and attach to User/Role
- Authentication = IAM
- Authroization = IAM Policy
- Leverages "SigV4" capability where IAM credentials are in headers

![IAM Auth](./assets/52.png)

### Resource Policies

- Resource Policies on API Gateway
- Allow for cross account access combined with IAM security
- Allow from VPC
- Allow only from IP ranges

### Cognito User Pools

- Cognito fully manages user lifecycle, token expiration
- Authentication = Congnito
- Authorization = API Gateway methods

![Cognito Auth](./assets/53.png)

### Lambda Authorizer (fromerly Custom Authorizers)

- Token-based authorizer: JWT or Oauth
- This method is used mainly when you use 3rd party authentication system
- Lambda Authrorizer returns a policy that is cached
- Authentication = external
- Authrorization = Lambda Function

![Lambda Authorizer](./assets/54.png)

### Custom Domain Name HTTPS Security

- Custom Domain Name HTTPS through intergration with AWS Certificate Manager (ACM)
- If using Edge-Optimized endpoint, then the certificate must be in **us-east-1**
- If using Regional endpoint, the certificate must be in the API Gateway region
- Must setup CNAME or A-alias record in Route 53

## API Gateway - Stages

- Making changes in the API Gateway does not mean they're effective
- You need to make a **deployment** for them to be in effect
- Changes are deployed to **stages**
- Use the naming you like for stages (dev,test,prod)
- Each stage has its own configuration parameters
- Stages can be rolled back, because the history of deployments is kept

### Stage Variables

- Stage variables are like environment variables for API Gateway
- Use them to change often changing configuration values
- You can access stage variables in the mapping templates, or pass configuration parameters to:
  - Lambda Function
  - HTTP Endpoint
- Use cases:
  - Configure HTTP endpoints your stages talk to (dev,test,prod)
  - Pass configuration parameters to AWS Lambda through mapping templates
  - Stage variables are passed to the "context" object in AWS Lambda
- Format: `${stageVariables:variableName}`
- Format example: `${stageVariables.lambdaAlias}` this will give us a input field names 'lambdaAlias' to enter which alias of lambda we want to invoke (ex: DEV, PROD, TEST)

## API Gateway - Canary Deployment

- Choose the % of traffic the canary channel receives
- Metrics & logs are separate (for better monitoring)
- Possibility to override stage variables for canary
- This is also called blue/green deployment with AWS lambda & API Gateway
  ![Canary deployment via API Gateway](./assets/50.png)
- We can also have canary deployment via lambda (with no API gateway changes)
  ![Canary deployment via Lambda](./assets/51.png)

## API Gateway - Integration Types

- Integration Type MOCK
  - API Gateway returns a response without sendint the request to the backend
- Integration Type HTTP/AWS Lambda/AWS Services
  - You must configure both the integration request and the integration response
  - Setup data mapiing using **mapping templates** for the request & response
- Integration Type AWS_PROXY (lambda proxy)
  - incoming request from the client is the input to lambda
  - the function is responsible for the logic of request/response
  - No mapping template, headers, query string parameters
- Integration type HTTP_PROXY
  - No mapping template
  - The HTTP request is passed to the backend
  - The HTTP response from the backend is forwarded by API Gateway
  - Possibility to add HTTP headers if need be (ex: API key)

## API Gateway - Mapping Templates

- Mapping templates can be used to modify request/responses
- Rename / Modify query string parameters
- Modify body content
- Add headers
- Used for filter output results (remove unnecessary data)
- Uses Verlocity Template Language (VTL): for loop, if etc...
- Content-Type can be set to application/json or application/xml

### Use cases

- Transform JSON to XML for a SOAP API:
  - SOAP API are XML based, whereas REST API are JSON based
- Rename Query String Parameters: from:
  - `example.com/path?name=foo&other=bar` to
  - `{"my_variable":"foo", "other_variable":"bar"}`

## API Gateway - Open API spec

- You can import/export API's
- Old name (swagger)
- Common way of defining REST APIs, using API definition as code (YAML or JSON)
- We can export our own API as Open API spec
- Because you are using the Open API format you can generate SDKs for your applications (JavaScript, swift, ruby, etc...) through the console that will match your API

### Open API spec for Request Validation

- We can use the open API spec to preform request validation
- You can configure API gateway to perform basic validation of an API request before proceeding with the integration request
- When the validation fails, API gateway immediately fails the request (return 400 error to the caller)
- This reduces unnecessary calls to the backend
- Checks:
  - The required request parameters in the URL, query string, and headers of an incoming request are included and non-blank
  - The applicable request payload adheres to the configured JSON schema request model of the method
- The request validators can be also created without using the Open API spec, just via the console

## API Gateway - Caching

- Caching reduces the number of calls made to the backend
- Default TTL: 300sec (min: 0s, max: 3600s)
- Caches are defined per stage
- Possible to override cache setting set on the stage per method (GET/POST, etc...)
- Cache encryption option
- Cache capaciy between 0.5GB to 237GB
- Cache is expensive, makes sense in production, may not make sense in dev/test

### Cache Invalidation

- Able to flush the entire cache through the console
- Clients can invalidate the cache with the HTTP header `cache-control:max-age:0` (require InvalidateCache action in IAM policy), when 'Require authorization' is checked
- If you do not select the 'Require authorization' checkbox in the console, any client can invalidate the API cache.

## API Gateway - Usage Plans & API Keys

- If you want to make an API available as an offering ($) to your customers
- Usage Plans:
  - who can access one or more deployed API stages and methods
  - how much and how fast they can access them
  - uses API keys to identify API clients and meter access
  - configure throttling limits (requests per seconds) and quota (requests per month) limits that are enforced on indiviual API key
- Callers of the API must supply an assigned API key in the x-api-key header in requests to the API

### Correct order for API keys and usage plans

1. Create one or more APIs
2. Configure the methods to require an API key
3. Deploy API to stages
4. Generate or import API keys to distribute
5. Create the usage plan with the desired throttle and quota limits
6. Associate API stages and API keys with the usage plan

## API Gateway - Logging and tracing

### CloudWatch Logs

- Log contains information about request/response body
- Enable CloudWatch logging at the stage level (with log level: ERROR, DEBUG, INFO)
- Can override settings on a per API basis

### CloudWatch Metrics

- Metrics are collected by a stage, possibility to enable detailed metrics
- CacheHitCount & CacheMissCount: efficiency of the cache
- Count: The total number API requests in a given period
- InegrationLatency: The time between when API Gateway relays a request to the backend and when it receives a response from the backend
- Latency: The time between when API Gateway receives a request from a client and when it returns a response to the client. The latency includes the integration latency and other API Gateway overhead
- 4XXError metric (client-side)
- 5XXError metric (server-side)

### X-Ray

- Enable Tracing to get extra information about requests in API Gateway

## API Gateway - Throttling

- Account Limit
  - API Gateway throttles requests at 10000 rps across all APIs
  - If one API consumes all quotas, other APIs will be throttled
  - Soft limit can be increased upon request
- In case of throttling, **429 Too Many Requests** error will occur (retriable error)
- Can set **Stage limit & Method limits** to improve performance
- Or you can define **Usage Plans** to throttle per customer

## API Gateway - Errors

- 4xx (client errors)
  - 400: Bad Request
  - 403: Access Denied
  - 429: Quota exceeded, throttle
- 5xx (server errors)
  - 502: Bad Gateway
  - 503: Service Unavaliable
  - 504: Integration Failure, 29 second timeout

## API Gateway - CORS

- CORS must be enabled on API Gateway when you request API calls from another domain (ex: S3 website) to make a request to API Gateway

## API Gateway - WebSocket API

- What's WebSocket?
  - Two-way interactive communication between a user's browser and a server
  - Server can push information to the client
  - The enables **stateful** application use cases
- WebSocket APIs are often used in real-time applications:
  - chat applications
  - multiplayer games
  - financial trading platforms

![WebSocket communication](./assets/55.png)

### WebSocket API Routing

- Incoming JSON messages can be routed to different backends
- Routing tables should be defined in API Gateway
- An action is specified in the JSON message, this actions is matched with the value on the route table

## API Gateway - Architecture

- Create a signel interface for all the microservices in your company

![Architecture](./assets/56.png)
