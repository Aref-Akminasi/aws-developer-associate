[Back](./AWS.md)

# EC2

## What is EC2?

- EC2 = Elastic Compute Cloud = Infrastructure as a Service

## EC2 sizing & configuration options

- Operating System (OS): Linux, Windows or MacOS
- How much compute power & cores (CPU)
- How much random-access memory (RAM)
- How much storage space:
  - network-attached (EBS & EFS)
  - hardware (EC2 Instance Store)
- Network card: speed of the card, Public IP address
- The public IP address changes upon stopping and starting the machine
- Firewall rules: security group
- An EC2 is bound to an AZ, you cannot change the AZ of an EC2 directly
- Bootstrap script (configure at first launch): EC2 User Data
- If you want to keep the instance root EBS, set the `DeleteOnTermination` attribute to `False` using the command line, works also if the instance is running.

## EC2 User Data

- It is possible to bootstrap our instances using an **EC2 User data** script.
- **bootstrapping** means launching commands when a machine starts.
- That script is **only run once** at the instance **first start**
- EC2 user data is used to automate boot tasks such as:
  - installing updates
  - installing software
  - downloading common files from the internet
- The EC2 User Data Script runs with the root user (the root user of the machine itself: sudo)

## AWS has the following naming convention:

- m5.2xlarge
  - m: instance class
  - 5: generation
  - 2xlarge: size within the instance class

## EC2 instance types

### General Purpose

- Great for a diversity of workloads (Balance between Compute, Memory and Networking) such as
  - web servers
  - code repositories
- Starts mostly with letters: T / M

### Compute Optimized

- Great for compute-intensive tasks that require high performance processors
  - Batch processing workloads
  - Media transcoding
  - High performance web servers
  - High Performance Computing (HPC)
  - Scientific modeling & machine learning
  - Dedicated gaming servers
- Starts mostly with letter: C

### Memory Optimized

- Fast performance for workloads that process large data sets in memory
- Use cases:
  - High performance in memory databases
  - Distributed web scale cache stores
  - In-memory databases optimized for BI (Business Intelligence)
  - Applications performing real-time processing of big unstructured data
- Starts mostly with letters: R / X / High Memory / Z

### Storage Optimized

- Great for storage intensive tasks that require high, sequential read and write access to large data sets on local storage
- Use cases:
  - High frequency online transaction processing (OLTP) systems
  - Relational & NoSQL databases
  - Data warehousing applications
  - Distributed file systems
- Starts mostly with letters: D / I / H

## Security Groups

- Security groups regulate:

  - Access to ports
  - Authorised IP ranges - IPv4 and IPv6
  - Control of inbound network (from other to the instance)
  - Control of outbound network (from the instance to others)
  - Security groups **don't** manage access to AWS resources like S3 buckets or other services; this is handled by IAM roles and policies which define permissions for AWS resources.
  - Security groups handles access to RDS database
  - Security groups handles access to EFS

---

- Security groups only contain **ALLOW** rules
- Security groups rules can reference by IP or by security group (security groups can reference each other)
- Security groups are acting as a **firewall** on Elastic Network Interface (ENI)/EC2 instances

---

- Security groups can be attached to multiple instances
- An EC2 instance could have multiple Security Groups attached to it, add the rules will add on to each other

---

- Security groups are locked down to a **region / VPC combination**
- Security groups does live **outside** the EC2 - if traffic is blocked the EC2 instance won't see it
- It's good practice to maintain one separate security group for SSH access
- If your application is not accessible **time out**, then it's a security group issue or a firewall (corporate firewall or a personal firewall)
- If your application gives a **connection refused** error, then it's an application error, or it's not launched

---

- All inbound traffic is blocked by default, inbound rules control the incoming traffic that's allowed to reach the instance.
- All outbound traffic is authorised by default, outbound rules control the outgoing traffic that's allowed to leave the instance.

## Classic Ports to know

- 22 = SSH (Secure Shell) - log into a Linux instance
- 21 = FTP (File Transfer Protocol) - upload files into a file share
- 22 = SFTP (Secure File Transfer Protocol) - upload files using SSH
- 80 = HTTP - access unsecured websites
- 443 = HTTPS - access secured websites
- 3389 = RDP (Remote Desktop Protocol) - log into a Windows instance

## SSH

- SSH is one of the most important functions. It allows you to control a remote machine, all using the command line
- connect with SSH using the terminal:

  `ssh -i <EC2 SSH key file path here> ec2-user@<EC2 public IP address here>`

- Typing `exit` in the terminal or pressing `ctrl+d` will close the connection

- You can also connect using with SSH with EC2 Instance Connect
- While using EC2 Instance Connect, you might want to do some action like 'getting all the users of IAM', never enter your own access key and secret access key while using SSH in the `aws configure` of an EC2, because someone can connect to the EC2 using EC2 Instance connect and retrieve that data. What you can do is assigning an IAM role to the EC2 instance

## EC2 Instances Purchasing Options

### On-Demand Instances:

- short workload, predictable pricing, **pay by second**

### Reserved Instances (1 or 3 years)

- Reserved Instances: long workloads, you reserve a specific instance attributes (Instance type, Region, Tenancy, OS) (Discount ~ 72%)
- Convertible Reserved Instances: long workloads with flexible instance attributes (Instance type, Region, Tenancy, OS) (Discount ~ 66%)

### Savings Plans (1 or 3 years):

- commitment to an amount of usage, long workload (Discount ~ 72%)
- Locked to a specific instance family & AWS region (ex: M5 in us-east-1)
- Usage beyond EC2 Savings Plans is billed at the On-Demand price

### Spot Instances:

- short workload, cheap, can lose instances (less reliable) discount (~ 90%)
- Instances that you can **lose** at any point of time if your max price is less than the current spot price
- Useful for workloads that are resilient to failure
- Not suitable for critical jobs or databases

### Dedicated Hosts:

- book an entire physical server, with EC2 instance capacity fully dedicated to your use
- Allows you to address compliance requirements and use your existing server bound software licenses
- Purchasing options:
  - On-demand: pay per second
  - Reserved: 1 or 3 years
- The most expensive option
- Dedicated Hosts on AWS do provide control over the underlying hardware.
- Useful for software that have complicated licensing model or for companies that have strong regulatory or compliance needs

### Dedicated Instances:

- No other customers will share your hardware
- Instances run on hardware that's dedicated to you
- May share hardware with other instances in same account
- No control over instance placement

### Capacity Reservations:

- reserve capacity in a specific AZ for **any duration**
- You always have access to EC2 capacity when you need it
- You are charged at On-Demand rate whether you run instances or not
- No time commitment, **no billing discounts**
- Combine with regional reserved instances and savings plans to benefit from billing discounts

\*Note: when buying Savings plans, Dedicated Hosts, Capacity Reservations, you are not having instances yet

\*Note: you can buy or sell your reserved instances if you don't need them anymore

\*Dedicated Hosts are more suited for specific compliance requirements and licensing models that require control over physical hardware. Dedicated Instances are easier to manage and are suited for workloads requiring physical isolation but not detailed hardware control.

## EC2 Instance Metadata (IMDS)

- It allows AWS EC2 instances to "learn about themselves" without using an **IAM Role** for that purpose
- The URL is `http://169.254.169.254/latest/meta-data`
- You can retrieve the IAM role name attached to your EC2 instance using the Instance Metadata service, but you can not retrieve the IAM policies themselves.

# EC2 Storage

## What's an EBS Volume?

- An EBS (Elastic Block Store) Volume is a network drive you can attach to your instances while they run
- It allows your instances to persist data, even after their termination
- Mostly they can only be mounted to one instance at a time, however some EBS has "multi-attach" feature
- EC2 Instance can have multiple EBS volumes attached to it
- They are bound to a specific Availability Zone (created in us-east-1a cannot be attached to use in us-east-1b)
- Free tier: 30 GB of free EBS storage of type General Purpose (SSD) or Magnetic per month
- Have a provisioned capacity (size in GBs, and Input/Output Operations per Second (IOPS))
- EBS volumes may also stay unattached to an EC2 instance
- EBS volumes can be attached to EC2 instances while the instance is running
- EBS volumes support both in-flight encryption and encryption at rest using KMS

## Delete on Termination Attribute

- By default, the root EBS volume is deleted when the instance is deleted (attribute enabled)
- By default, any other attached EBS volume is not deleted (attribute disabled), it's retained
- You can also deselect the `Delete on Termination` attribute to save the data when an instance is deleted

## EBS Snapshots

- Make a backup (snapshot) of your EBS volume at a point in time
- Not necessary to detach volume from an EC2 instance to do snapshot, but recommended
- You can copy snapshots across AZ or Region
- Amazon EBS Snapshots are incremental, storing only the changes since the last snapshot

### EBS Snapshots Features

- EBS Snapshot Archive
  - Move a Snapshot to an "archive tier" that is 75% cheaper
  - Takes within **24 to 72 hours** for restoring the archive
- Recycle Bin for EBS Snapshots
  - Setup rules to retain deleted snapshots so you can recover them after an accidental deletion
  - Specify retention (from 1 day to 1 year)
- Fast Snapshots Restore (FSR)
  - Force full initialization of snapshot to have no latency on the first use ($$$)

### EBS Volume Types

### gp2/gp3 - General purpose SSD

- Usage:

  - virtual desktops
  - development
  - test environments

- Volume for both: 1 GiB - 16 TiB

- gp2:

  - Max throughput: 250 MiB/s (max speed is depending on the volume size)
  - IOPS: Max 16000 depending on volume size
  - 3 IOPS per GB, means at 5334 GB we are at the max IOPS

- gp3:
  - Max throughput: 1000 MiB/s (max speed is **not** depending on the volume size)
  - Can increase IOPS up to 16000 and throughput up to 1000 MiB/s independently (there is a still a ratio between the volume size and the IOPS, but it has a bigger margin than gp2)

### io1/io2 Block Express - Provisioned IOPS (PIOPS) SSD:

- Usage:

  - mission-critical
  - low-latency
  - high-throughput workloads
  - Great for **databases workloads**

- Supports EBS Multi-attach

- io1:

  - Volume: 4 GiB - 16 TiB
  - Max throughput: 1000 MiB/s
  - Max PIOPS 64000 for Nitro EC2 instances & 32000 PIOPS for others
  - Can increase PIOPS independently of volume size (up to 50 PIOS per 1 GiB)

- io2 Block Express
  - Volume: 4 GiB - 64 TiB
  - Max throughput: 4000 MiB/s
  - Max PIOPS 256000 with an IOPS: GiB ratio of 1000:1

### st1/sc1 Cold HDD - (HDD)

- Cannot be a boot volume
- 125 GiB to 16 TiB

- st1:

  - Usage:
    - designed for frequent accessed, high-throughput workloads
    - Big Data
    - Data Warehouses
    - Log Processing
  - Max throughput 500 MiB/s - max IOPS 500

- sc1 Cold HDD:
  - Usage:
    - For data that is infrequently accessed
    - Scenarios where the lowest cost is important
  - Max throughput 250 MiB/s - max IOPS 250

## EBS Multi-Attach - io1/io2 family

- Works only with io1/io2
- Attach the same EBS volume to multiple EC2 instances in the same AZ
- Up to **16** EC2 instances at a time
- Must use a file system that's cluster-aware (**not** XFS, EXT4, etc...)
- Use case:
  - Achieve higher application availability in clustered Linux applications (ex: Teradata)
  - Applications must manage concurrent write operations

## Local EC2 Instance Store

- EBS volumes are network drives with good but "limited" performance, if you need a **high-performance hardware** disk, use EC2 instance store
- Better I/O performance (even faster than io2 type)
- EC2 instance store lose their storage if they're stopped (ephemeral)
- Good for buffer / cache / scratch data / temporary content
- For long term storage EBS is a great use case
- Risk of data loss if hardware fails
- Backups and replication are your responsibility (You can run a database on an EC2 instance that uses an Instance Store)

## EFS

- EFS is a managed NFS (network file system) that can be mounted on many EC2 instances
- EFS works with EC2 instances in **multi-AZ** (You have one zone EFS option too)
- Highly available, scalable, expensive (3x gp2), pay per use
- Compatible with Linux based AMIs **(not Windows)**
- **Uses security group to control access to EFS**
- File system scales automatically, pay-per-use, no capacity planning

### EFS Settings

#### Setting 1: Throughput Mode (MB/s)

- Bursting: Provides throughput that scales with the amount of **data stored**, for workloads with basic performance requirements
- Elastic: automatically scales throughput up or down based on your **workloads**, for workloads with unpredictable I/O
- Provisioned: set your throughput (MB/s) manually

#### Setting 2: Performance Mode (IOPS)

- General Purpose
- Max I/O (works with bursting & provisioned throughput)

### EFS Storage classes

- Storage Tiers:
  - Standard: for frequently accessed files
  - Infrequent Access (EFS-IA): cost to retrieve files, lower price to store
  - Archive: rarely accessed data (few times each year), 50% cheaper
  - Implement lifecycle policies to move files between storage tiers (ex: move a file if it has had no access for 60 days)

## AMI

- AMI = Amazon Machine Image
- AMI are a customization of an EC2 instance
  - You add your own software, configuration, operating system, monitoring...
  - Faster boot / configuration time because all your software is pre-packaged
- AMI are built for a specific region (and can be copied across regions)
- You can launch EC2 instances from:
  - A public AMI: AWS provided
  - Your own AMI: you make and maintain them yourself
  - An AWS Marketplace AMI: an AMI someone else made (and potentially sells)

\*Note: Creating an AMI from an instance that has a user data set to it, if the instance has been launched before creating the AMI, it means the programs/tools specified in the User Data of the instance will be available out of the box to the AMI when an instance is created from the AMI and launched.
