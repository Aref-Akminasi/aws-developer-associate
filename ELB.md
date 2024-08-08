[Back](./AWS.md)

# Elastic Load Balancer (ELB)

## What is load balancing?

- Load balancers are **servers** that forward traffic to multiple servers (ex: EC2 instances)
- You cannot change the Elastic Load Balancer type after deployment (you can only change the configuration)

## Why to use a load balancer?

- Spread load across multiple instances
- Expose a single point of access to your application
- Do regular health checks to your instances
- Provide SSL termination (HTTPS) for your websites
- Enforce stickiness with cookies
- High availability across zones
- Separate public traffic from private traffic

## Health Checks

- They enable the load balancer to know if instances it forwards traffic to are available to reply to requests
- The health check is done on a port and a route (Protocol: HTTP, Port: 4567, Endpoint: /health)
- If the response is not 200 (OK), then the instance is unhealthy and the elastic load balancer will not send traffic to that instance
- Health checks are configured at the target group level.

## Types of load balancer on AWS

| Load Balancer Type        | Layer                               | Supported Protocols                        |
| ------------------------- | ----------------------------------- | ------------------------------------------ |
| Application Load Balancer | Operates at layer 7                 | HTTP, HTTPS, WebSocket                     |
| Network Load Balancer     | Operates at layer 4                 | TCP, TLS (secure TCP), UDP                 |
| Gateway Load Balancer     | Operates at layer 3 (Network layer) | IP protocol (GENEVE protocol on port 6081) |

## Load balancers and security

- To ensure security around load balancers, users can access the load balancer from anywhere using HTTP or HTTPS, with security group rules allowing traffic on ports 80 (HTTP) and 443 (HTTPS) from any IP address (0.0.0.0/0).
- The EC2 instances security group rules should permit HTTP traffic on port 80, with the source being the security group of the load balancer, **not an IP range**. This setup ensures that EC2 instances only allow traffic originating from the load balancer, enhancing security.

## Application Load Balancer

### Specifications

- ALB is layer 7 (HTTP)
- Allows load balancing to multiple HTTP applications across machines (target groups)
- Allows load balancing to multiple applications on the same machine (ex: containers)
- An ALB requires a security group around it
- Support redirects (from HTTP to HTTPS for example)
- You get a static DNS hostname with ALB (`XXX.region.elb.amazonaws.com`)

### Routing

- Routing tables to different **target groups**

  - Routing based on path in URL (`example.com/users` & `example.com/posts`)
  - Routing based on hostname in URL (`one.example.com` & `other.example.com`)
  - Routing based on Query String/Query Parameters (`example.com/users?id=123&order=false`)
  - Routing based on Source IP address
  - Routing based on HTTP Headers

- Routing based on Geo-location is **not** possible

### Usage

- Micro services
- Container-based application (example: Docker & Amazon ECS), because it has a **dynamic host port mapping** feature to redirect to a dynamic port in ECS

### ALB Target Groups

- EC2 instances
- Lambda functions
- Private IP Addresses
- _ECS tasks_

### Headers Provided by ALB to the server

- The application servers don't see the IP of the client directly, but will get extra headers from the ALB:
  - **X-Forwarded-For** This header contains the real IP address of the client.
  - **X-Forwarded-Port** This header specifies the port number that the client used to connect to the ALB.
  - **X-Forwarded-Proto** This header specifies the protocol (HTTP or HTTPS) that the client used to connect to the ALB.

### Authenticate Users

- Your application load balancer can securely authenticate users
- Authenticate users through:
  - Identity provider (IdP): OpenID Connect (OIDC) compliant
  - Cognito User Pools
- Must use an HTTPS listener
- **OnUanauthenticatedRequest** options:
  - authenticate (default): ask the user to authenticate
  - deny
  - allow

## Network Load Balancer

- Network Load Balancer (Layer 4)
- Allow to forward TCP & UDP traffic to your instances
- High performance handles milions of requests per seconds
- Less latency ~ 100 ms (vs 400 ms for ALB)
- Provides a static DNS hostname
- NLB has **one static IP per AZ** and supports assigning Elastic IP (helpful for whitelisting specific IP)
- Not included in the AWS free tier
- Health Checks support the **TCP, HTTP and HTTPS** Protocols

### NLB Target Groups

- EC2 instances
- Private IP Addresses
- Application Load Balancer
- _ECS Tasks_

## Gateway Load Balancer

- Deploy, scale and manage a fleet of 3rd party network virtual appliances in AWS
- Operates at Layer 3 (Network Layer) - IP Packets
- Uses the **GENEVE** protocol on port 6081
- Usage:
  - Firewalls
  - Intrusion Detection
  - Prevention Systems
  - Deep Packet Inspection Systems
  - Payload manipulation

### Target groups:

- EC2 instances
- Private IP Addresses

## Sticky Sessions (Session Affinity)

- It is possible to implement stickiness so that the same client is always redirected to the same instance behind a load balancer
- This works for: ALB, NLB
- How it works: A 'cookie' is used for stickiness with an expiration date you control **(Note: NLB works without cookies)**
- Stickiness has to be configured at the target group attributes
- Note: enabling stickiness may bring imbalance to the load over the backend EC2 instances
- Note: Cookie duration is to choose between 1 sec and 7 days

### Cookie Types

- Application-based cookies
  - Don't use the names: **AWSALB**, **AWSALBAPP**, or **AWSALBTG** (reserved by the ELB)
- Load balancer generated cookie

## Deregistration Delay (Connection Draining)

- Time to complete "in-flight-requests" while the instance is de-registering or unhealthy
- Stops sending new requests to the EC2 instance which is de-registering
- If new users are connecting to our ELB, our ELB is smart enough to establish new connections with other EC2 instances
- Draining time: between 0 - 3600 seconds, so it can be disabled (default: 300 seconds)
- If your requests are short, then it's recommended to set the draining time to a low value so that the instance can drain fast and be replaced or so.
- If your requests are huge: like uploading files, it is recommended to set the draining time high
- Changing the draining time happens at the target group level

## Cross-Zone Load Balancing

- When Cross-Zone Load Balancing is enabled, ELB distributes traffic evenly across all registered EC2 instances in all AZs. (The instances will be seen as if they are in the same AZ)
- Application Load Balancer

  - Enabled by default and is always on (can be disabled at the Target Group level)
  - No charges for inter AZ data

- Network Load Balancer & Gateway Load Balancer
  - Disabled by default (can be enabled at the Load Balancer level)
  - You pay charges ($) for inter AZ data if enabled
