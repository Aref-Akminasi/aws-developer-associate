[Back](./AWS.md)

# DynamoDB

## NoSQL Databases

- NoSQL databases are non-relational databases
- NoSQL databases include MongoDB, DynamoDB...
- NoSQL databases do not support query joins
- NoSQL databases do not support aggregation such as "SUM", "AVG"
- All the data that is needed for a query is present in one row
- RDS databases are limited for horizontal scaling (we scale reads by adding read replicas, we can't scale writes), while NoSQL databases scale horizontally by adding RCUs, WCUs

## DynamoDB - Basics

- Standard & Standard-IA Table Classes
- DynamoDB is made of Tables
- Each table has a primary key, must be decided at creation time
- Each table can have an infinite number of items (= rows)
- Each item can have attributes (can be added over time - can be null)
- Maximum size of an item is **400KB**

### Supported Data types

- Scalar Types: String, Boolean, Binary, Number...
- Document Types: List, Map
- Set Types: String Set, Number Set

## DynamoDB - Primary Key

- Has two options:

### Option 1: Partition Key (HASH)

- Partiton key must be unique for each item
- Partition key might not be null
- Partition key must be "diverse" so that the data is distributed (ex: user ID)

### Option 2: Parititon Key + Sort Key (HASH + RANGE)

- The combination must be unique for each item
- Parition Key or Sort Key might not be null
- Data is grouped by parititon key for searching
- While the same partition key or sort key may appear multiple times in the table, the same combination of both cannot be duplicated.

## DynamoDB - Read/Write Capacity Modes

- You can switch between modes once every 24 hours
- Has two options:

### Provisioned Mode (Default)

- You specify the number of read/writes per second
- You need to plan capacity beforehand
- Support for **auto scaling** to dynamically adjusts the provisioned throughput capacity (define min, max capacity units)

### On-Demand Mode

- Read/writes automatically scale up/down with your workloads
- No capacity planning needed
- Unlimited WCU & RCU, no throtteling
- Use case: unknown workloads, unpredictable application traffic
- Pay for reads/writes, more expensive ($$$)

## DynamoDB - Read/Write Capacity Units

- WCU and RCU are decoupled you don't need to increase both if you just need to increase one of them

### Write Capacity Units (WCU)

- One Write Capacity Unit (WCU) represents one write per second for an item up to 1KB in size
- If the item is larger than 1KB, more WCUs are consumed

- Example 1: we write 10 items per second, with item size 2KB: we need 20WCUs
- Example 2: we write 6 items per second, with item size 4.5KB: we need 30WCUs

### Read Capacity Units (RCU)

- One Read Capacity Unit (RCU) represents **one Strongly Consistent Read** per second or **two Eventually Consistent Reads** per second, for an items up to 4KB in size
- If the items are larger than 4KB, more RCUs are consumed

### Read/Write Modes

#### Read modes

- Eventual Consistency
- Strong Consistency
- Transactional

#### Write modes

- Standard
- Trasactional

#### Eventually Consistent Read VS Strongly Consistent Read

- Eventually consistent Read (default): If we read just after a write, it's possible we'll get some stale data
- Strongly Consitent Read: If we read just after a write, we will get the correct data
- Set **ConsistentRead** parameter to **True** in API calls
- Consumes twice the RCU

### Partitions

- Data is stored in partitions
- Partition Keys go through a hashing algorithm to know to which partition they go to
- Data with same partition key goes to the same partition
- WCUs and RCUs are spread evenly across partitions

### Throttling

- Throughput can be exceeded temporarily using **Burst Capacity**
- If Burst Capacity has been consumed, you'll get a **ProvisionedThroughputExceeded**
- Reasons for throttling:
  - Hot Keys, one partition key is being read too many times
  - Hot Partitions, Remember RCUs and WCUs are spread across all the table's partitions.
    (throttling might happen even if you didn't exceed the total number of WCU/RCU)
  - Very Large Items, remember RCU and WCU depends on size of items
- Solutions:
  - Exponential backoff
  - Use distribute parition keys
  - If it is RCU issue, we can use **DynamoDB Accelerator (DAX)**

## DynamoDB - API Calls

- Note: Filter Expressions filters the results of **read** queries, while condition expressions are for **write** operations

### Writing Data

- PutItem: Creates a new item or fully replace an old item (same Primary Key)
- UpdateItem: Edits an existing item's attributes or adds a new item if it doesn't exist
- Concurrent Writes: the second write overwrites the first write (ex: update value to 1, then update value to 2. value will be 2)
- Conditional Writes: Accept a **write/update/delete** only if a condition is met (attribute_exists, attribute_not_exists, categories, prices, etc...)
  - Note: it is also used for deletes
  - We can pass a json file as a map for values
  - Use case: change price of an item if it's price falls in the 'discount' category
  - Use case: Delete an item if it has no price
  - Use case: Prevent from writing simultanously with others, (ex: update value = 1 only if value = 0), if two writes from the same type are sent, the first write is accepted, the second write fails
- Optimistic Locking: each item has an attribute that acts as a version number, a strategy to ensure an item hasn't changed before you update/delete it, it is handy for scenarios where two users are updating the same item at the same time

### Reading Data

- GetItem: read **one item** based on primary key (HASH or HASH + RANGE)

  - Use ProjectionExpression: can be specified to retrieve only certain attributes

- Query: returns items based on Partition key and (optionaly) Sort Key

  - Use the KeyConditionExpression parameter to provide a specific value for the partition key.
  - Returns up to 1MB of data - use pagination to keep on reading

- Scan: scan the entire table and then filter out data client side

  - Returns up to 1MB of data - use pagination to keep on reading
  - Consumes a lot of RCU
  - You can have a limit parameter
  - You can have parralel scans to speed up the scan

- FilterExpression (optional): A filter expression is applied after a Query/Scan finishes, used only with non-key attributes.
- Use pagination to keep on reading for both Query and Scan

### Delete Data

- DeleteItem
- DeleteTable
- The best way to erase all data in a DynamoDB table is to DeleteTable and then create the table again **it is not recommended to use scan and delete items there after**

## Batch Operations

- Allows you to save in latency by reducing the number of API calls
- BatchWriteItem
  - Up to 25 PutItem and/or DeleteItem in one call
  - Up to 16MB of data
  - Can't use (UpdateItem)
  - If some operations fails (ex: we can't write because lack of capacity) we will get back the **UnprocessedItems** and thereafter we can use exponential backoff or add WCU
- BatchGetItem
  - Return items from one or more tables
  - Up to 100 items, up to 16MB of data
  - **UnprocessedKeys** for failed read operations (Exponential backoff or add RCU)

## DynamoDB - Local Secondary Index (LSI)

- Alternative Sort Key for your table
- Has same partition key as the sort table
- Up to 5 LSI's per table
- Must be defined at table creation time
- Use a Local Secondary Index (LSI) when you need to query with a different sort key but share the same partition key as the primary key.
  ![LSI](./assets/48.png)

## DynamoDB - Global Secondary Index (GSI)

- Alternative Primary Key (HASH or HASH+RANGE)
- Can be added/modified after table creation
- You can see it as if you are creating a new table
- If the GSI is throttled, the main table will be throttled
- Use a Global Secondary Index (GSI) when you need to query a DynamoDB table using an attribute that is not part of the primary key.
  ![GSI](./assets/47.png)

## DynamoDB - PartiQL

- SQL-compatible query language for DynamoDB

## DynamoDB - DAX

- DAX = Fully Managed, highly avaliable, seamless in memory cache nodes for DynamoDB
- Create DAX nodes
- Microseconds latency for cached reads & queries
- Solves too many reads problem
- Has TTL, (default 5 minutes = 300 sec)
- Support for Multi-AZ (3 nodes required for high availability)
- DAX can be combined with ElastiCache in an architecture
- Has encryption capabilites (at rest, in transit)
- IAM Service role for DynamoDB access

## DynamoDB - Streams

- Ordered stream of item-level modifications (create/update/delete) in a table
- You don't provision shards, this is automated by AWS
- Only changes after creating the stream will appear in the stream
- DynamoDB Streams are made of shards, just like Kinesis Data Streams (This is way Kinesis Client Library can be used)
- Streams records can be sent to:
  - Kinesis Data Streams
  - Kinesis Client Library (KCL)
  - AWS Lambda
- Data retention for up to 24 hours
- Use cases:
  - React to changes in real-time (welcome email to users)
  - Implement cross-region replication
  - Analytics
  - You can have Kinesis Data Streams as a consumer and then send data to Kinesis Data Firehose and then to RedShift, OpenSearch, S3
    ![DynamoDB Streams](./assets/49.png)

### Ability to choose the information that will be written to the stream

- Key attributes only: only the key attribute of the modified item
- New Image: the entire item, as it appears after it is modified
- Old image: the entire item, as it appeared before it was modified
- New and old images: both the new and the old images of the item

## DynamoDB - TTL

- Automatically delete items after an expiry timestamp (Unix time)
- you have to add an attribute as type **Number** with a value of **Unix time**
- When enabling TTL you have to add a custom name for the expire attribute (ex:expires_on)
- Doesn't consumer any WCUs
- Expired items deleted within 48 hours of expiration (it is not immediately)
- Expired items are deleted from both LSIs and GSIs
- A delete operation for each expired item enters the DynamoDB streams (can help recover expired items)

## DynamoDB - Transactions

- DynamoDB Transactions: You can group multiple actions together and submit them as a single all-or-nothing **TransactWriteItems** or **TransactGetItems** operation.
- Example: do an **UpdateItem** in an **AccountBalance** table, and **PutItem** in **BankTransactions** table
- Consumes 2x WCUs & RCUs
- Example 1: 3 Transactional writes per second, with item size 5KB = 30 WCUs
- Example 2: 5 Transactional reads per second, with item size 5KB = 20 RCUs
- Use cases:
  - Financial transactions
  - Managing orders
  - Multiplayer games

## DynamoDB - Session State Cache

- It's common to use DynamoDB to store session state
- vs ElastiCache: ElastiCache is in-memory, but DynamoDB is serverless
- vs EFS: EFS must be attached to EC2 instances as a network drive
- vs EBS & Instance store: EBS & Instance store can only be used for local caching, not shared caching
- vs S3: S3 is higher latency, and not meant for small objects

## DynamoDB - Write Sharding

- Imagine we have a voting application with two candidates, candidate A and candidate B
- If Partition Key is Candidate ID this results into two partitions, which will generate issues (ex: Hot Partition)
- A strategy that allows better distribution of items evenly across parititons: add a suffix to a partition key value (ex: candidate_A-11, candidate_A-20)

## DynamoDB - Working with S3

- Large Objects Pattern: store a reference to S3 bucket (ex: Image URL), the client will get the item from DynamoDB and download from S3
- Indexing S3 Objects Metadata: store object's metadata through a lambda function in a DynamoDB Table

## DynamoDB - Operations

- Table Cleanup:
  - Option 1: Scan + DeleteItem
    - Very slow, consumes RCU & WCU, expensive
  - Option 2: Drop Table + Recreate Table
    - Fast, efficient, cheap
- Copying a DynamoDB Table
  - Option 1: Using AWS Data Pipeline
  - Option 2: Backup and restore into a new Table
  - Option 3: Scan + PutItem or BatchWriteItem
    - Write your own code

## DynamoDB - Securiy & Other Features

### Security

- VPC Endpoints available to access DynamoDB without using the public internet
- Access Fully controlled by IAM
- Encryption at rest using AWS KMS and in-transit using SSL/TLS

### Backup and restore feature available

- Point-in-time recovery

### Global Tables

- Multi-region, high performance

### DynamoDB Local

- Develop and test apps locally without accessing the DynamoDB web service (without internet)

### Migrate to DynamoDB

- AWS Database Migration Service (AWS DMS) can be used to migrate to DynamoDB (from Oracle, MySQL, etc...)

## DynamoDB - Users interact with DynamoDB Directly

1. User Login: Users authenticate using identity providers like Amazon Cognito User Pool.
2. Temporary AWS Credentials: After authentication, users receive temporary AWS credentials associated with an IAM role.
3. IAM Role Conditions: The IAM role includes conditions, such as LeadingKey, to restrict users' access to data based on the primary key, ensuring users can only access their own data.
4. Attribute-Level Access Control: Additional conditions can be set on attributes to limit which specific attributes a user can view or modify.

## DynamoDB - CLI

- `--projection-expression` one or more attributes to retrieve
- `--filter-expression` filter items
- `--page-size: 10` specify that AWS CLI retrieves the **full list** of items but with a larger number of API calls instead of one API call
- `--max-items: 100` max number of items to show in the CLI (returns NextToken) **max-items** is not the max total items, it will not keep making the calls like --page-size
- `--starting-token: eyJFeGNsdXN...` specify the last NextToken to retrieve the next set of items
- Note: you can use max-items without using page size, just to scan items in one API call without pagination
