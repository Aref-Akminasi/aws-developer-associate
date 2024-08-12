[Back](./AWS.md)

# Step Functions

## Step Functions - Basics

- Model your workflows as **State Machines**, one state machine per workflow
- Written in JSON
- Visualization of the workflow and execution of the workflow
- Step Functions are used for:
  - Order fulfillment, Data processing
  - Web applications, any workflow
- Start workflow via:
  - SDK call
  - API Gateway
  - EventBridge
  - AWS Console

## Step Functions - Tasks

- Step functions do some work in your state machine:
  - Invoke one AWS service (invoke lambda funcions, insert an item into DynamoDB, etc...)
  - Run Activity Tasks (EC2, ECS, on-premises, etc...). Activites poll the Step functions for work, activities send results back to Step Functions

## Step Functions - States

- Choice state: Test for condition to send to a branch
- Fail or succeed state: Stop execution with failure or success
- Pass State: Simply pass its input to its output or inject some fixed data, without performing work.
- Wait State: Provide a delay for a certain amount of time or until a specified time/date
- Map State: Dynamically iterate steps
- Parallel State: Begin parallel branches of execution

![States](./assets/72.png)

## Step Functions - Error Handling

- Error handling has to happen in step functions **not** in the application code (ex: not in the lambda function)
- Use **Retry** and **Catch** (transition to failure path) in the State Machine
- When max attempts are reached, the **Catch** kicks in

### Predefined error codes

- States.ALL: matches any error
- States.Timeout: Task time out
- States.TaskFailed: execution failure
- States.Permissions: insufficient premissions

\*Note: The task may report it's own custom errors

### Retry

- Use 'ErrorEquals' in the JSON to match the error, ex: `"ErrorEquals": ["States.ALL"]`
- IntervlSeconds: delay before next retry
- MaxAttempts: max attempts
- BackoffRate: multiply the delay after each retry

```json
  "Retry": [
    {
      "ErrorEquals": ["States.ALL"],
      "IntervalSeconds": 5,
      "MaxAttempts": 5,
      "BackoffRate": 2.0
    }
  ]
```

### Catch

- Use 'ErrorEquals' in the JSON to match the error, ex: `"ErrorEquals": ["States.ALL"]`
- Next: Next state to send to
- ResultPath: Is the payload of the error passed into the next state

```json
"Catch": [
    {
        "ErrorEquals": ["CustomError"],
        "Next": "CustomErrorFallback", //references the state at the end
        "ResultPath": "$.error" // pass the error into the next task
    }
]

"CustomErrorFallback": {
    "Type": "Pass",
    "Result": "This is a fallback",
    "End": true
}
```

## Step Functions - Wait For Task Token

- Allows you to pause Step Functions during a Task until a Task Token is returned
- Task might wait for other AWS service, 3rd party API call, human approval, etc...
- Append **.waitForTaskToken** to the **Resource** field to tell Step Functions to wait for the Task Token to be returned
- Ex: `"Resource": "arn:aws:states::sqs:sendMessage.waitForTaskToken"`
- Task will pause until it receives that Task Token back with a **SendTaskSuccess** or **SendTaskFailure** API call

## Step Functions - Activity Worker

- Enables you to have the Task work performed by an **Activity Worker**
- Activity Worker can be running on EC2, On-premises, lambda, etc...
- Activity Worker **polls** for a task using **GetActivityTask** API
- After Activity Worker completes its work, it sends a response of its success/failure using **SendTaskSuccess** or **SendTaskFailure**
- To keep the task active:
  - Configure how long a task can wait by setting **TimeoutSeconds**
  - Periodically send a heartbeat from your Activity worker using **SendTaskHeartBeat** within the time you set in **HeartBeatSeconds** configuration that you set on your step function, which define how long is the maximum time to wait for a heart beat

![Activity Worker](./assets/73.png)

## Step Functions - Standard vs Express

|                  | Standard Workflow                | Express Workflow: (sync or async)                                               |
| ---------------- | -------------------------------- | ------------------------------------------------------------------------------- |
| Maximum duration | 1 year                           | 5 minutes                                                                       |
| Delivery         | Exactly-once workflow execution. | sync: At-most-once workflow execution. async: At-least-once workflow execution. |
| Execution Rate   | Over 2000/sec                    | Over 100.000/sec                                                                |

- Note: Express Async, doesn't return the response, see CloudWatch for logs. while for Express Sync the response is returned
