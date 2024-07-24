[Back](./AWS.md)

# Auto Scaling Group (ASG)

## General

- In real-life, the load on your websites and application can change
- The goal of an Auto Scaling Group (ASG) is to:
  - Scale out (add EC2 instances) to match an increased load
  - Scale in (remove EC2 instances) to match a decreased load
  - Ensure we have a minimun, desired and a maximum number of EC2 instances running
  - EC2 Instances are made based on a launch template
  - Automatically register new instances to a load balancer
  - When an EC2 instance fails the ALB Health Checks, its marked unhealthy and will be terminated while the ASG launches a new EC2 instance.
- ASGs are free (you only pay for the underlying EC2 instances)

## Auto Scaling Group Attributes

- Launch Template that contains (AMI, EC2 User Data, EBS volumes, Security Groups, etc...)
- Min size / Max size / Initial Capacity
- Scaling Policies

## Auto Scaling Groups - Scaling Policies

- Dynamic Scaling
  - Target Tracking Scaling: adjusts capacity to maintain a specific metric at a value in the ASG (Based on a CloudWatch metrics)
    - Example: I want the average ASG CPU usage to stay around 40%
  - Simple Scaling: Simple scaling responds directly to the state of a CloudWatch alarm.
  - Step Scaling: Step scaling allows you to define a series of scaling adjustments (steps) based on different CloudWatch alarm thresholds
- Scheduled Scaling
  - Anticipate a scaling based on known usage patterns
  - Example: increase the min capacity to 10 at 5pm on Fridays
- Predictive Scaling: continuously forecast load and schedule scaling ahead

### Good metrics to scale on

- CPUUtilization: Average CPU utilization across your instances
- RequestCountPerTarget: to make sure the number of requests per EC2 instances is stable (like if I know that each instance can handle 1000 request per second)
- Average Network In / Out: If your application is network bound

### Scaling Cooldowns

- During the cooldown period, the ASG will not lanuch or terminate additional instances to allow for metrics to stabilize (default 300 seconds)
- Advice: Use a ready-to-use AMI to reduce configuration time in order to be serving request faster (so the cooldown period can be lowered)

## Auto Scaling Groups - Instance Refresh

- Goal: update launch template and then re-creating all EC2 instances
- The ASG will not terminate all the instances and launch new ones, but it will look at the **min. Healthy Percentage** (ex: 60%) and start terminating and launch new instances, over time the old instances are all terminated and new ones have came up
- On the top of **min. Healthy Percentage** we can specify a **warm up time** (how long we assume the new instance is ready to use)
