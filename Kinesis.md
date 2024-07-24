[Back](./AWS.md)

# Kinesis Data Streams

## General

- Retention time between 1 day to 365 days
- Data can not be deleted (immutability)
- Messages in Kinesis Data Streams are called **records**
- Each record has a **partition key**
- Record Size: up to 1MB
- Processing speed: Real time
- Kinesis Data Streams has **replay capabilities**, you will be able to **reprocess**, or **reconsume** data from a stream

## Kinesis Data Streams - Ordering

- Records at the same shard are proccesed in the order they were added
- Records that shares the same partition goes to the same shard (ordering)
- the consumer for a specific shard, and for records with the same partition key, will be the same.

## Kinesis Data Streams - ProvisionedThroughputExceeded Error

- ProvisionedThroughputExceeded error occurs when the throughput of a shard is reached

### Solutions

- Retry with exponential backoff
- Increase shards (scaling via shard splitting)
- Use highly distributed partition key (in order to split records on different shards)

## Kinesis Data Streams - Capacity Modes

### Provisioned Mode

- Choose it if you know the capacity in advance
- You choose the number of shards provisioned, scale manually
- You pay per shard per hour

#### Writing speeds

- 1MB/sec per shard **and** 1000 msg/sec per shard

#### Reading speeds

- Shared mode:
  - 2MB/s per shard across all consumers
  - Max 5 Get API calls/sec per shard
  - Lower cost $
- Enhanced (fan-out) mode:
  - 2MB/s per shard per consumer
  - Higher cost $$$

### On-demand Mode

- Choose it if you don't know the capacity in advance
- No need to provision or manage the capacity
- Default speed: 4MB/s or 4000 records per second
- Speed scales automatically based on last 30 days usage
- Pay per stream per hour **and** data in/out per GB

## Kinesis Data Streams - Producers

- AWS SDK
- Kinesis Producer Library (KPL)
- Kinesis Agent
- and more...

- Producers use **PutRecord API** to put data in Kinesis Data Streams

## Kinesis Data Streams - Consumers

### Self managed

- Kinesis Client Library (KCL)
  - Each KCL has to read from at least one Kinesis Shard, reading from multiple shards is ok
  - Each shard can be read by only one KCL instance at a time
  - You can not have more KCLs than shards, having less KCLs than shards is ok
  - Progress is checkpointed into **DynamoDB** (IAM Access required)
- AWS SDK

### AWS Managed

- AWS Lambda
- Kinesis Data Firehose
- Kinesis Data Analytics

## Kinesis Data Streams - Security

- Encryption in flight using HTTPS
- Encryption at rest using KMS
- Supports client side encyrption/decryption
- Control Access / authorization using IAM policies
- Monitor API Calls using **CloudTrail**
- **VPC endpoints** avaliable for Kinesis to access within a VPC not and across the internet

## Kinesis Data Streams - Scaling

### Shard Splitting (Scale up)

- Used to increase stream capacity
- Used to divide a (hot shards) into two shards
- Can't split into more than **two** shards in a single operation
- The old shard is closed and will be **deleted** once the data is **expired** (1 day to 365 days)

### Merging Shards (Scale down)

- Decrease the stream capacity and save costs
- Can be used to group **two** shards with low traffic (cold shards)
- Old shards are closed and will be **deleted** once the data is **expired** (1 day to 365 days)
- Can't merge more than two shards in a single operation

# Kinesis Data Firehose

## General

- Fully managed Service, no administration, automatic scaling, serverless
- Speed: **Near real-time**
- No data storage
- Ability to transform data using a lambda function (optional)
- Ability to convert a record format (optional)
- Can send failed data (data that firehose could not deliver to the destination) to a S3 bucket
- Size up to 1 MB for each record
- Needs IAM role for read/write to S3 and other services

## Kinesis Data Firehose - Buffer

- Kinesis Data Firehose buffer size is used to accumulate records before sending them to the target
  - Example: wait until there is 5 MB of data before sending them to the target
- Buffer interval is how fast the data will be sent to the target if the buffer doesn't fill up
  - Example: wait for 30 seconds before sending the data, even if the buffer size is not reached
- The data will be sent if the **buffer size** is reached or the **buffer interval** is reached

## Kinesis Data Firehose - Consumers

- S3
- Redshift
- OpenSearch
- HTTP(s) endpoint
- 3rd party parnter: MongoDB, DataDog...

- Note: Lambda is **not** a consumer

# Kinesis Data Analytics

Two types:

- Kineses Data Analytics for SQL applications
- Kinesis Data Analytics for Apache Flink

## Kinesis Data Analytics for SQL applications

- Real time analytics on Kinesis Data Streams & Firehose using SQL
- Fully managed, no servers to provision
- Add reference data from **S3** to enrich streaming data

### Producers

- Kinesis Data Streams
- Kinesis Data Firehose

### Consumers

- Kinesis Data Stream
- Kinesis Data Firehose

## Kinesis Data Analytics for Apache Flink

### Producers

- Kinesis Data Streams
- Amazon MSK

# Kinesis vs SQS

- Use Kinesis when you need:
  - real-time processing
  - replay capabilities
  - Message retention for more than 14 days (because SQS max retention period is 14 days)
  - Records are not deleted after they are processed and might be processed from other consumers
  - Millions of data is streamed
- Use SQS for:
  - Decoupling applications
  - When real-time processing is not important
  - Messages are deleted after they are processed
