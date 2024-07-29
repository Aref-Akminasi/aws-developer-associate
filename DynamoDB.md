[Back](./AWS.md)

# DynamoDB

## NoSQL Databases

- NoSQL databases are non-relational databases
- NoSQL databases include MongoDB, DynamoDB...
- NoSQL databases do not support query joins
- NoSQL databases do not support aggregation such as "SUM", "AVG"
- All the data that is needed for a query is present in one row
- RDS databases are limited for horizontal scaling (we scale reads by adding read replicas, we can't scale writes), while NoSQL databases scale horizontally

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
- If Burst Capacity has been consumed, you'll get a **ProvisionedThroughputExceededException**
- Reasons for throttling:
  - Hot Keys, one partition key is being read too many times
  - Hot Partitions, remember WCUs and RCUs are spread evenly across partitions (this throttling might happen even if you didn't exceed the total number of WCU/RCU)
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
  - Can be used to implement **Atomic Counters** - a numeric attribute that is unconditionally incremented
- Conditional Writes: Accept a **write/update/delete** only if a condition is met (attribute_exists, attribute_not_exists, categories, prices, etc...)
  - Note: it is also used for deletes
  - We can pass a json file as a map for values
  - Use case: change price of an item if it's price falls in the 'discount' category
  - Use case: Delete an item if it has no price

### Reading Data

- GetItem: read **one item** based on primary key (HASH or HASH + RANGE)

  - ProjectionExpression: can be specified to retrieve only certain attributes

- Query: returns items based on Partition key and (optionaly) Sort Key

  - Returns up to 1MB of data (before the filter is applied) - use pagination to keep on reading
  - You need to specifiy KeyConditionExpression: Partition key value (must be = operator)
  - FilterExpression (optional): A filter expression is applied after a Query finishes, but before the results are returned. Therefore, a Query consumes the same amount of read capacity, regardless of whether a filter expression is present, used only with non-key attributes

- Scan: scan the entire table and then filter out data client side
  - Returns up to 1MB of data - use pagination to keep on reading
  - Consumes a lot of RCU
  - Limit the impact using **Limit**
  - For faster performance, use **Parallel Scan**

### Delete Data

- DeleteItem
- DeleteTable
- The best way to erase all data in a DynamoDB table is to DeleteTable and then create the table again **it is not recommended to use scan and delete items then**

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

## PartiQL

- SQL-compatible query language for DynamoDB

## DynamoDB - Local Secondary Index (LSI)

- Alternative Sort Key for your table (same partition key as the sort table)
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
