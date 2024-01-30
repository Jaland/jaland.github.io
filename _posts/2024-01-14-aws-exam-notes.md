---
layout: post
title:  "AWS AWS Certified Solutions Architect - Associate Exam Notes"
tags: AWS
category: aws certification
---

# Intro

Just wanted to capture the set of notes I took while going through the Cloud Guru course in attempts to get the AWS exam. I did not bother refining them but there is some decent high-level info I can refer to about the different AWS services in the future.

<!-- Copy and paste the converted output. -->

<!-----

You have some errors, warnings, or alerts. If you are using reckless mode, turn it off to see inline alerts.
* ERRORs: 0
* WARNINGs: 0
* ALERTS: 2

Conversion time: 9.43 seconds.

Using this Markdown file:

1. Paste this output into your source file.
2. See the notes and action items below regarding this conversion run.
3. Check the rendered output (headings, lists, code blocks, tables) for proper
   formatting and use a linkchecker before you publish this page.

Conversion notes:

* Docs to Markdown version 1.0β35
* Tue Jan 30 2024 06:38:22 GMT-0800 (PST)
* Source doc: AWS Notes
* This document has images: check for >>>>>  gd2md-html alert:  inline image link in generated source and store images to your server. NOTE: Images in exported zip file from Google Docs may not appear in  the same order as they do in your doc. Please check the images!

----->

## AWS Notes

### Services

#### Compute

##### EC2

* Virtual Machine hosted in the cloud
* Only pay for what you use
* **Types**
  * On-Demand
    * Pay by hour
    * Best For:
      * Flexible
      * Short Term
      * Testing the Waters
  * Reserved
    * Reserve Capacity
    * Saves the most money
    * Best For:
      * Predictable Usage
      * Specific Capacity Requirements
      * Pay Up Front
      * Up to 72% off on demand
      * _Convertible RLS -_allows you to change to greater or equal instances (but a little more expensive than standard reserved)
      * _Scheduled Rls_- Like a reserved instance but you can schedule it for specific parts of the day
  * Spot
    * Up to 90% discount
      * Decide Max Spot Price
      * If above your spot price have 2 minutes to figure out to stop or terminate
    * Popular question and kind of a weird one
      * If you have a spot request with multiple desired number of instances and is persistent (not one-time) you can end up in a weird state. Say you want 100 instances but only 50 exist it will just keep reprovisioning instances
      * If you end up with a question like this just need to know that you need cancel the request and **also** delete all the instances (canceling the request won’t do that automatically)
    * Spot Fleets
      * Collection of Spot instances (and optionally on demand instances) that will try to meet a target capacity you set
    * Prices fluctuate
    * Flexible  start and end times
    * Used for
      * High Performance Computing
      * Image Rendering
      * Genomic Sequencing
      * Algorithmic Trading engines
      * CI/CD
  * Dedicated
    * Physical Server dedicated to you
    * Most expensive
    * Used For:
      * Compliance
      * Licensing
        * Legacy garbage (i.e. they don’t have good pricing for cloud)
    * Can be purchased On-Demand or Reserved (same deal as above)
  * **Networking**
    * **ENI (Elastic Network Interface)**
      * Basic Day to Day
    * **EN (Enhanced Networking)**
      * High Speeds
        * 10-100 gigbits per second
      * VF (Virtual Function Interface) is just an older crapier version should always choose EN if an exam question comes up with that
    * **EFA (Elastic Fabric Adapter)**
      * Machine Learning
      * High Performance Computing
      * OS-Bypass
  * **Placement Groups**
    * **Cluster Placement Group**
      * Group in a single AZ
      * Recommended for apps that need low network latency
    * **Spread Placement Group**
      * Instances that are placed in distinct underlying hardware
      * Small Critical applications that should be kept separate from each other
    * **Partition Placement Group**
      * Each Partion Group has its own set of racks with its own network and power sources
      * HDFS, HBase, Cassandra
      * Isolate hardware failures
    * Should always have homogenous (the same) instance within a cluster placement group
    * Groups can’t be merged
    * Instance can be moved in to group only once stopped
  * **User Data/Bootstrap Script**
    * Used to run some script on startup
  * **Metadata**
    * Data about your instance
    * Can be queried by User Data
  * **VMware on AWS**
    * Used by orgs for on-prem cloud deployments
    * Use Cases
      * Hybrid Cloud
      * Cloud Migrations
      * DR
      * Lets you use other AWS services
    * Runed on dedicated servers
  * **Outpost**
    * Bringis AWS data center to you on-premise
      * Lets you use AWS services on prem
    * Reasons:
      * Hybrid Cloud inside your data center
      * Fully managed by AWS
      * Brings Consistency to you
    * **Family Members (common question)**
      * **Outpost Rack**
        * Gives the same AWS Infrastructure services and API in your own data center
        * Entire Rack so need a data center for this to make sense
      * **Outpost Servers**
        * Provides local compute and networking services
        * Individual servers
        * Small space requirements
  * AWS Calculator can be used to figure out cost
  * Bootstrap Scripts
    * Automatically run some script on bootup
  * **EC2 Hibernation**
    * Saves contents from ram into disk
    * When Started up the data is loaded back to ram
    * Boots a lot faster than stopping the disk
    * On-Demand and Reserved Instances only
    * 60 day limit
    * 150 GB of ram limit
    * C/M/R class families (not free tier)
* **Launch Templates**
  * Always use over Launch Configuration
* **Auto Scaling Groups**
  * **Only for EC2 (**not dbs or other services)
  * Bake stuff into your AMI (don’t install a bunch of crap after)
    * Quicker boot times
  * Define a template
  * Use multiple AZ for HA
  * ELB Configuration
    * Autoscaling groups can be set up to respect help checks
  * Restrictions (# of instances)
    * Max
    * Minimum
    * Desired (How many you want to start with)
  * Lifecycle Hooks
    * Lets you keep an instance in `PENDING` state for up to 2 hours waiting from something to complete
    * Scaling Out (on start) hooks
      * Good for installing software
      * Good for setting up licensensing
    * Scaling in (on termination) hook
      * Good for storing logs before you die
* **Scaling**
  * **Cloud watch is going to be the go-to tool for knowing when to scale**
  * **Step Scaling (Basic idea of scaling)**
    * When Memory is between some percentage (i.e. 60/80%) add some number of instances. Or Drop some number of instances
  * **Scaling Policies**
    * **Simple Scaling**
      * Uses cloudwatch alarm  using basic metric to trigger when over/under a specific number
    * **Target Tracking**
      * Will try to **maintain** a specific value or range (i.e. I want my instances around 70% utalized)
  * **Scaling Type**
    * **Reactive**
      * Normal/Scale as you need it
    * **Scheduled**
      * Scale based on time of day/year
        * Think shopping
    * **Predictive**
      * Uses AI to predict when you will need scaling based on past usage
  * **Warm-up/Cool-down**
    * **Warmup**
      * Gives instances time to boot up before failing health checks and being terminated
    * **Cooldown**
      * Pauses the auto scaling for a set amount of time to avoid run away scaling events
    * Used to avoid thrashing

##### AMI

* Amazon Machine Image
* Storage by:
  * EBS
    * Can be stopped
  * Instance Store
    * Ephemeral storage
    * Can’t be stopped

##### Simple Queue Service (SQS)

* Messaging queue allow asynchronous processing
* Heavily feature on exam apparently
* **Setting**
  * **Delivery Delay**
    * Default is 0
    * Set up to 15 minutes
    * Delay before delivery
  * **Message Size**
    * **256 KB** in a single message
  * **Encryption**
    * Encrypted in transit by default
    * **NOT**encrypted at rest by default (but can be set)
  * **Message Retention**
    * **Default is 4** days
    * Can be set between 1 minute and **14 days**
  * **Long vs Short**
    * Long Polling isn’t default (but it should be)
      * Probably what we want on the exam
  * **Queue Depth**
    * This can be a trigger for autoscaling
    * More crap on the queue increase the number of readers
  * **Visibility Timeout**
    * When someone pulls a message the message is locked for VTO
      * Other readers can’t see the message
      * Waits for OG reader to say I am done so it can be purged
      * If time out happens the message is unlocked
        * Could cause double reads if too short
    * Default 30 seconds
  * Dead Letter Queues
    * Where you send messages that can’t be processed
    * Also works with SNS
    * Isolate and debug unreadable messages
    * **Redrive Capability**- move back to main queue
    * Technically SQS queues (so 14 day max I guess??))
    * Can configure alarms
* **FIFO Queues**
  * Ordering
  * No Duplications
  * 300 transactions per second
    * Batching can up it to 3000
    * **FIFO High Throughput** ups it to 9000 or 90,000 with batching
  * DLQs must also be FIFO
* **Standard Queue**
  * Near Unlimited TPS

##### Simple Notification Service (SNS)

* Pushed based messaging (not polling)
* Used mostly for alerting
* Only supports retry policies for HTTP(S) endpoints
* **256 KB** in a single message
  * **Large Message Payloads**
    * Extended library that allows you to publish up to 2 GB object to S3 and send reference to the object
* Subscribers
  * Kinesis Data Firehose, SQS, Lambda, Email, HTTPS, SMS, App endpoints
* DLQ Support
* FIFO/Standard
* Encryption (transit by default, at-rest needs to be enabled
* **SNS Fanout**
  * Publish topics replicated to multiple subscriptions
  * **Architectures:**
    * SNS -> Multiple SQS queue that process message in parallel
    * SNS -> Kinesis Data Firehose (huge amount of data sent and/or transformed) -> S3 Bucket/Amazon OpenSearch
      * S3 stores info
      * Amazon OpenSearch - Visualizing info about the data
    * Use filtering to send message to correct SQS queue
* Message Filtering
  * By default every topic sent to every subscriber
  * Can be filtered to be sent to specific subscribers

##### Amazon MQ

* Message broker service
* Lets you migrate existing MQ apps to the AWS cloud
* Ovvers HA
* **SNS with SQS** or**Amazon MQ**
  * **SNS with SQS**
    * New messaging system
    * Simpipler
    * Globally Accesssible
  * **Amazon MQ**
    * Migrating existing systems
    * REQUIRES VPC or Direct Connect
      * I.E. if a specific protocol is listed JMS, AMQP, Openwire etc…

##### Kinesis

* Real-Time Data streaming
* **Data Streams vs Firehose**
  * **Data Streams**
    * Real Time streaming
    * Responsible for creating consumer and scaling stream (more difficult)
  * **Firehose**
    * Near Real Time (within 60 seconds)
    * Plug and play with AWS Architecture
    * Handles Scaling
    * Elastic Search/S3/Redshift
* Kinesis Data Analytics
  * Processes information
* **Why Kinesis**
  * Real Time!
  * Near Real Time (firehose)

##### AWS Step Functions

* Serverless Orchestration Function that combine different AWS services (mostly lambda) to create business application
* Has Graphical Console
* **Components**
  * **State Machine**
    * Workflow with event-driven steps
    * Each step is a **State**
    *
  * **Task**
    * A single unit of work in a state machine
    * **State** would be the running version of a task
* **Workflow types**
  * **Standard**
    * Exactly one execution
    * Can run up to one year
    * Useful for long running auditable history
    * Rates up to **2000 executions per second**
    * Pricing based on per state transition (not length of time run)
  * **Express**
    * At least once workflow execution
    * Up to **5 minutes**
    * High event rate workloads
    * Example: IoT data streaming and injection
    * Pricing based on number of executions, duration, and resource usage
* **State Types**
  * **Pass:**Passing input directly to output
  * **Task:**Single unit of work (lambda, batch, sns)
  * **Choice:**Adds branching logic (forks)
  * **Wait:**Waits for a specific amount of time
  * **Succeed:**Stops execution successfully
  * **Fail:**Stops execution as failure
  * **Parallel:**Runs parallel branches of execution
  * **Map:**Runs a set of steps based on an array of elements

##### Lambda

* Serverless Compute service
* Need IAM role to do stuff
* **Features:**
  * Automated Scaling
  * **Pricing**
    * Free Teir 1,000,00 request and 400,000 GBs of compute per month
    * Pay per request after
  * Integrates with anumorus AWS services
  * Built-In Monitoring
  * Easy to configure
  * 15 minutes(900 second) execution limit
  * Supports a bunch of runtimes
* **Configuration**
  * **Runtime**
  * **Permissions-** Attach IAM role
  * **Networking:**Optionally define the VPC subnet and security group
    * If not defined runs in AWS owned VPC with internet access
  * **Resources**- The amount of memory allocated
  * **Trigger:**What starts your event
* **Quota/Limitations**
  * 1000 concurrent executions
  * 512-10 gb disk storage (/tmp)
    * Can integrate with EFS if needed
  * 4 KB for all environment variables
  * 128 MB - 20 GB memory allocation
  * 15 minute run time
  * Code Size
    * Compressed deployment package must be &lt;= 50 MB
    * Uncompressed Deployment package must be &lt;= 250 MB
  * Request Payload up to 6 MB
  * Streamed response up to 20 MB
* **Architectures**
  * Event Bridge(cron) -> Function -> Shuts down EC2 instances

##### AWS Serverless Application Repository

* Find/Deploy/Publish serverless applications
* Defined based on “AWS SAM Templates”
* **Publish vs Deploy**
  * Publish - Publish the application for other to deploy
    * Private by default
  * Deploy - Find and deploy public applications
  *

##### Fargate

* Serverless compute engine for docker containers
* AWS owns and managed infrastructure
* **Requires** the use of ECS or EKS (does not do orchestration)
* **Fargate vs Lambda**
  * **Fargate**
    * Consistent workloads
    * Docker
  * **Lambda**
    * Unpredictable or inconsistent workloads
    * Single stateless function applications

##### AWS AppSync

* Scalable GraphQL Interface
  * Data language that enables apps to fetch data from servers
  * Seamless integration with React/Reactive Native/iOS/Android
*

##### ECS/EKS/ECR

* **ECS - Elastic Container Service**
  * Easier than Kube
  * Proprietary
* **EKS - Amazon Kubernetes**
  * Complex
  * Hybrid/Opensource
  * EKS-D (same thing but you manage it)
    * Self managed EKS
* **EKS/ECS anyware**
  * Same deal as above but you run it on prem
  * No direct access of cluster
  * **ECS Anyware**
    * Must have SSM/ECS agent/Docker installed
* …no mention of Openshift, AWS!? LAME!!!
* **ECS Launch Types:**
  * **EC2**
    * Responsible for underlying OS
    * EC2 Pricing
    * Better for long-running containers
    * Capable of mounting EFS file system for persistent, shared storage
  * **Fargate**
    * Better for short running task
    * Pay based on allocated resource run time (cheaper)
    * No OS access
    * Capable of mounting EFS file system for persistent, shared storage
* **ECR -**Elastic Container Registry
  * Contains Docer/OCI artifacts/OCI Artifacts
  * **Features:**
    * Lifecycle Policy
    * Image scanning
    * Cross-Region/Cross-Account support
    * Cacheing Public Repos
    * Tag Immutable
    *
*

##### EventBridge

* Serverless Event Bus
* **Concepts**
  * **Events**
    * Recorded change in AWS environment
  * **Rules**
    * Criteria used for mapping incoming events to targets
  * **Event Bus**
    * A router that receives events and delivers them to targets
* **Rule Triggers**
  * **Event Pattern**
    * Event source and event pattern
      * I.e. EC2 instance terminate
      * Can also detect things like root login events and alert teams
  * **Scheduled**
    * Rate-based
      * Do something every hour
    * Cron-based

##### AWS Batch

* Batch Workloads
* Automatically Provision and Scale
* No Install Required
* **Components**
  * Job
    * Unit of work
  * Job Definition
    * Defines your jobs
  * Job Queues
    * Queue for running jobs
  * Compute Environment
    * Underlying Compute
    * Fargate vs EC2
      * Fargate:
        * Most Workloads
      * EC2
        * GPUS
        * Elastic Fabric Adapter
        * Custom AMIs
* BATCH vs LAMBDA
  * Time Limits
    * **Lambda** has **15 minute execution limit**
  * Runtime Limitations
    * Lambda is FULLY SERVERLESS but has limited runtimes
  * Disk Space
    * Lambda has limited disk space and EFS requires functions live in a VPC
  * Batch Runtimes
    * Use docker so can use any runtime
* Managed vs Unmanaged
  * **Managed**
    * AWS manages instance types
    * Can use your own AMI
    * Easiest
  * **Unmanaged**
    * Manage everything
    * Less common but good for extremely specific requirements

##### Amazon AppFlow

* Managed service for exchange data between SaaS apps and AWS services
* Used to Ingest Data
* **Terms**
  * Flow
    * Transfer of data between sources and destinations
  * Data Mapping
    * How the data is stored within destinations
  * Filter
    * Control which data is transferred
  * Trigger
    * How the flow is started
* **Use Cases**
  * Transfer salesfoce records into Amazon Redshift
  * Ingesting and analyzing Slack conversations in S3
  * Migrating Zendesk and other help desk support tickets
  * Transfer aggregate data to S3
* Transfer of data to/from **SaaS**applications

#### Storage

##### S3 - Simple Storage Service

* **S3 Versions**
  * S3 Standard
    * Default option
    * Perfect for static websites/videos/most static files that need to be accessed
  * S3 Standard - IA (Infrequent Access)
    * Rapid Access but less request access
    * Low per GB cost but a per GB Retrieval Fee
    * Great for longterm storage, backups, data store for DR
  * S3 Standard - IA (Infrequent Access) - 1 AZ
    * 20% cheaper but only use for non-critical data
  * S3 Intelligent Tiering
    * For data that is frequently and infrequently accessed
    * Moves data between S3 tiers to be most cost effective
  * Glacier
    * Longterm Data Storage (archive)
    * Glacier Instant Retrieval
      * Long term but may need instant
      * Database Backup
    * Glacier Flexible Retrieval
      * Can take 12 hours to retrieve
      * Non-Criticle backup
    * Glacier Deep Archive
      * Audit Data for compliance
      * 12-48 hour retrieval time
* Lifecycle Management
  * Lets you move between the tiers above for minimizing cost
  * Basically you will want to move files not used frequently to AI then Glacer
* **S3 Object Lock**
  * Uses WORM (Write once, Read Many) model to prevent objects from being deleted or modified
  * Can be used for Regulatory reasons
  * **Modes:**
    * Governance Mode:
      * Some users can alter the object but most users can’t
    * Compliance Mode
      * NO USER (included root) can change the object
  * Retention Periods
    * How long the modes above are applied for
  * Legal Hold
    * Like a retention period with no time limit
    * Remains in effect until removed
    * s3:PutObjectLeaglHold permission
  * Glacier Vault Lock
    * Apply the WORM model to a glacier vault
* **Encryption**
  * Encryption in transit
    * SSL/TLS
    * HTTPS
  * Encryption at Rest: Server -Side encryption (enabled by default)
    * SS2-S3: S3- managed keys using AES 256
    * SSE-KMS;: Uses AWS KMS
    * SSE-C: Customer Provided Keys
  * Encryption at Rest: Client Side Encryption
    * You encrypt the files before uploading them
  * Can enforce the header `x-amx-server-side-encryption` to make sure all uploaded files are encrypted
* S3 Prefix
  * Are just the subdirectories(folders) in the bucket
  * Reads are per prefix (weird) so spreading reads across prefixes gives you better performance
* **Performance**
  * **Limits**
    * If using SSE-KMS to encrypt there are KMIS limits to the upload/download
      * Region specific
    * If you are getting an issue uploading/downloading question could be due to KMS
    * limits
    * May want to just use builtin encryption to solve some issues
  * Multipart Uploads
    * Recommended for files over 100 mb (performance)
  * S3 Byte Range Fetches
    * Allows for us to parallelize downloads
* **Replication**
  * Way of replicating from one bucket to another
  * Versioning must be enabled
  * Existing Objects are **NOT** replicated
* Object(file) based storage
* Up to 5 sb in size
* Universal Namepace (not just your account a bucket takes that name for ALL ACCOUNTS EVERYWHERE)
* `[https://bucket-name.s3.region.amazonaws.com/key-name](https://bucket-name.s3.region.amazonaws.com/key-name)`
  * [https://jland-bucket.s3.useast-1.amazonaws.com/image.jpg](https://jland-bucket.s3.useast-1.amazonaws.com/image.jpg)
* **When an upload is successful you will get a 200 code**
* Key Value
  * Key
  * Value
  * Version ID
  * Metadata (tags,content type, etc..)
* Spread across multiple devices and facilities (3 azs)
  * **HA and High Durable**
    * 99.95%-99.99% availability
    * 99.999999999 durability
* Basic Info
  * Default storage
  * Used for Frequent Access
* Versioning
  * **Protect objects from being deleted**
  * Can Enable MFA for deleting an object (to prevent issues)
  * Stored objects can be retrieved including deleted ones
  * Can’t be disabled only suspended
  * Previous Versions are not publicly accessible
  * You can restore versioning by “deleting the delete marker”
    * Delete markers are how you “delete” an object
  *
* Offers
  * Tiered Storage (different prices/types
  * Lifecycle Management
    * Rules to move data to cheap data sources
* Server-Side Encrypting
  * All objects on the bucket are encrypted on upload
* Access Control List (ACLs) - **Individual Level**
  * Define which aws accounts or groups are grated access and type of access.
  * Individual Objects
* Bucket Policies - **Bucket Level**
  * Actions allowed on the bucket
* Strong Read-After-Write Consistency
  * I.e. After you write an object the next read will have that object
* **Pre Signed URLs**
  * By default all objects in S3 are private
  * Can create a “presigned url” to share a specific file with others
    * I.e. share a url to a video
  *
* **Pre Cookies**
  * Can be used to access to multiple restricted files
    * Example question: Have a site that you want to allow users to access your bucket once they sign up for your site

##### EBS (Elastic Block Storage)

* **gp2** (General Purpose SSD) - **gp3** (Just the newer better version
  * Good for boot volumes
  * Gp3 is 4 times faster than gp2 (but probably not being asked
  * 3 IOPS for each GiB (practice test question)
* **Io1/** (Provisied IOPS SSD) - **io2**
  * Used if you need more than 64,000-256,000 IOPS
  * Designed for I/O-Intensive Applications/Databases
* **Storage Optimized EC2 instance**
  * Ephemeral data
  * Provide 365,000 IOPS
* **St1** (Throughput optimized HDD)
  * Low-Cost HDD Volume
  * Desiged for frequently accessed, throughput intensive workloads
  * Big data/Data warehouses/etl/logproceessing
  * Can’t be a boot volume
* **SC1** (Cold HDD)
  * Lowest Cost Option
  * Performance does not matter
* **Iops vs Throughput**
  * **Iops**
    * Numbers of **operations**read/writes per second
    * Wanted for do reads and writes, low-latency
  * **Throughput**
    * Number of **bits** read/write per second
    * Ability to deal with large datasets
* **Volumes vs Snapshots**
  * **Volumes-**Virtual Hard Disk
    * Minimum on one volume per EC2 instance (where os is installed)
    * Exist on EBS
  * **Snapshots-**Snapshot in time
    * Incremental only the data that changes since last snapshot are stored
    * Exist in S3
    * Only captures data written to data
      * Stop instance then take a snap
    * Snapshots for encrypted EBS volumes are encrypted
    * You can **ONLY** share snapshots in the region in which they were created
      * Need to copy them to dest region to share them outside
      * Copy an EC2 instance to another region by:
        * Take Snapshot
        * Copy Snapshot to new region
        * Restore snapshot in new region
* **Encryption**
  * Includes
    * Data at rest is encrypted
    * Data in flight is encrypted
    * Snapshots are encrypted
    * Volumes created from snapshots are encrypted
    * (I.E. everything)
  * Minimal impact on latency
  * **Copying an unencrypted snapshot allows encryption**
    * Create a snapshot of unencrypted volume
    * **Copy the snapshot and select encrypt option**
    * Create AMI(Amazon Machine Image) from encrypted Snapshot
    * Use that AMI to launch new encrypted instance
* EBSVolumes always ins the same AZ as the EC2 instance
* Virtual Disk in the cloud
* Highly Available (automatically replicated)
* Dynamically increase capacity
* Change volume type on the fly (no need to restart neat)
* If EC2 is stopped the data is kept if it is terminated the data is deleted

##### EFS

* Managed NFS (Network File System) that can be mounted on many EC2
  * Used to share files between instances
* HA/Scalable but expensive
* Use cases:
  * **Linux based application where HA is important**
* Windows not supported
* Scales Automatically
* Control Performance
  * General Performance
    * Web Servers
  * Max I/O
    * Big Data
* **Storage Tiers**
  * Standard
  * IA (infrequently Accessed)
*

##### FSx

* FSx for Windows
  * Fully managed native Microsoft Windows Files Systems
  * **Sharepoint or Active Directory**migration
* FSx for Luster
  * **Machine Learning**
  * **Artificial Intelligence**
  * Can store directly on S3

##### AWS Backups

* Centralized place to backup stuff

##### Storage Gateway

* place to backup stuff

#### Databases

##### RDS (Relation Database)

* **Read Replica**
  * Read only copy of your db
  * This can be cross region or cross az
    * This **is**used for performance unlock the multi-az you get by default which is only for fail over
  * Buckups must be enabled
* SQL Server/Oracle/MySQL/Arurora/MariaDB/Postgre
* Up and Running in minutes
* Multi-AZ
  * ONLY for failover
* Fialover builtin
* Automated Backups
* **Online Transaction Processing (OLTP) &lt;- RDS Is GOOD for this**
  * Process data from transaction in real time
* **Online Analytical Processing (OLAP) -RDS Is BAD for this**
  * Processes complex queries to analyze historical data (i.e. net profits from the past 3 years)
  * Large amounts of data
* **Scaling**
  * **Types of scaling**
    * **Vertical Scaling**
      * Can change the size of the DB
      * Note you can refactor to NO-SQL but probably don’t want too
    * **Scaling Storage**
      * Can only go up
    * **Read Replicas**
      * Read-only
      * Not HA
    * **Aurora Serverles**
      * Offload scaling to AWS
      * Works great for **Unpredictable Workloads**

##### Aurora

* Amazon Proritory DB
* Compatible twitch Postgres and MySQL
* 5 times more performant
* Can do replicas but MySQL is a little bit slower
* Backups enabled does not impact performance
* **Amazon Aurora Serverless**
  * Same performance but only pay for when you use it
  * You need a database but you have spikes in usage
  * **Aurora Capacity Units (ACU)**Measurement on how your clusters scale
  * Can swap back/forth between serverless
  * Uses:
    * Variable Workloads
    * Multi-tenant apps (small dbs)
    * New Apps
    * Dev/Test
    * Mixed-Useage apps with spikes
    * Capacity Planning
*

##### DynamoDB

* No-SQL database
* SSD Storage
* Spread across 3 geographically distinct data centers
* Reads
  * **Default**Eventual consistent reads
    * Can take up to a second to update all dbs
  * Can opt into Strongly Consistent Reads
    * All reads work after a 200 write is returned
* DynomoDB Accelerator (DAX)
  * Fully-managed in-memory cache
  * Application -> DAX -> DynamoDB
  * Good for new product launches
  * Key-value (apparently code for SQL)
* Encryption at rest using KMS
* Site to site VPN
* CloudWatch/CloudTrail integration
* **DynamoDB Transactions** (gives ACID compliance)
* **Backups**
  * Full backups at any time
  * No performance impact
  * Operates within the same region as source table
* **Point-in-Time Recovery**
  * Not enabled by default
  * Restore to any point within the last 35 days
  * Latest Restorable time 5 minutes in the past
* DynamoDB Stream
  * Time ordered sequence of item-level changes
  * Split the different changes with Shards
* **Global Tables**
  * Moving between regions
  * Needs DynamoDB Streams turned on to work with this
  * Replication Latency normally under 1 second (crazy)
* **Scaling**
  * **Provisioned Capacity**
    * Used for predictable workloads
    * Most cost effective model if you know usage
  * **On-Demand**
    * Used for sporadic workloads
    * Pay a small amount for read/write
  * **Capacity Unit (CU)**
    * So you can actually define these when creating your table and it determines how quickly you can read/write to your db and how much the db cost per month
    * **Read Capacity Unit (RCU)**
      * Measurement of reads per seconds RPS for an item up to 4 kb in size
    * **Write Capacity Unit (WCU)**
      * Measurement of writes per second WPS for an item up to 3 KB in size
    *

##### Amazon DocumentDB

* MongoDB on Amazon
* Used for migration from MongoDB on-prem to AWS
*

##### Cassandra/Keyspaces

* **Cassandra-**Distributed database that uses NoSQL
  * Primarily big data solutions
* **Amazon Keyspaces-**Amazon’s Cassandra
  * **Red Herring** in exam
  * Only for migration of on-prem Cassandra

##### Graph Database/Neptune

* Stores graphs instead of traditional tables
* **Neptune** - Amazon’s graph database
  * Normally a **Red Herring**

##### Amazon Quantum Ledge Database (QLDB)

* **Red Herring**
* Immutable
  * Can’t delete a record only create new records
* Crypto / Shipping

##### Time Series Data

* **Time Series Data**
* IoT/Analytics/DevOps

##### Redshift

* Big Fully managed petabyte-scale data warehouse service in the cloud
* Big Data 3 Vs
  * **Volume**
    * Large Volumes
  * **Variety**
    * Wide range of sources in different formats
  * **Velocity**
    * Lots of data coming in at the same time and need to keep up
* 16 PB of data
* Relational Database built on PostgresSQL
* Not intended as a replacement for for standard RDS
* NEW! Multi AZs but only spans 2 AZs
* **Snapshots**
  * Point in time recovery or restoration to other regions
  * Stored in S3
    * **Can not manage underlying bucket directly**
* Leverage Large Batch Inserts
* **Redshift Spectrum**
  * Allows query and retrieval of data from Amazon S3
  * VERY fast against large data sets
* **Enhanced VPC Routing**
  * All COPY and UNLOAD traffic is forced through VPC

##### Elastic MapReduce (EMR)

* ETL
  * Extract
  * Transform
  * Load
* Big Data platform that allows you to process vast amount of data
* Supported Tools (just need to know they are supported not what they do)
  * Spark
  * Hive
  * HBase
  * Flink
  * Hudi
  * Presto
* Also Keep an eye out for
  * Apache Hadoop
  * Apache Spark
* **Storage Option**
  * Hadoop Distributed File System
    * Distributed scalable file system for Hadoop
  * EMR File System (EMRFS)
    * Extends Hadoop to add ability to store in Amazon S3
    * Good for input/output data
    * Bad for inflight data
  * Local File System
    * Locally Connected disk
    * Data goes away when running instance goes away
* **Clusters** of groups of EC2 instances, each instance is a **node**
* **Node Types**
  * **Primary Node**
    * Manages the cluster
    * Distributes data
    * Tracks health and status
  * **Core Node**
    * Runs task
    * Stores Data in HDFS
    * **Long Running**
  * **Task Node**
    * Task with no storage of data
    * Optional

##### Athena

* Interactive query service that makes easy to analyze S3 data using SQL
* Serverless SQL data option

##### Glue

* Serverless data integration (Replaces ETR)
* Serverless ETL data option
* **Architecture**
  * S3 -> Glue Crawlers -> Glue Data Catalog -> Athena -> Amazon QuickSight
  * S3 -> Glue Crawlers -> Glue Data Catalog -> Redshift Spectrum

##### Amazon QuickSight

* Fully managed Business Intelligence(BI) data visualization service
* Easily Create dashboards and share them
* Integration with RDS,Aurora, Athena, S3
* **SPICE**- Robus in memory engine used to perform advance calculations
* **Column-Level Security (CLS)-**Enterprise Security
* Pricing per-session and per-user basis
* Can create Users/Groups that have access to dashboards
  * These exist inside the QuickSight ecosystem (NOT IAM ROLES)
  * Users/Groups can see the underlying data of the dashboard

<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image1.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>

![alt_text](images/image1.png "image_tooltip")

##### AWS Pipeline

* ETL service used for automating movement of data
* **Features**:
  * Create Data-Driven Workflows
  * Defined Parameters
  * HA
  * Handles Failures
  * Integrates with AWS Storage Services and Compute
* **Components:**
  * Pipeline Definition
  * Managed Compute
  * Task Runners
  * Data Nodes
* **Use Cases**
  * Process Data in EMR using Hadoop Streaming
  * Import or export DynamoDB Data
  * Copying CSV files or data between S3 buckets
  * Exporting RDS data to S3
  * Copy to RedShift
* Keywords: **managed ETL services,**and **Automatic Retries**

##### Amazon Managed Streaming for Kafka (MSK)

* Fully managed services that use Apache Kafka
* Good for existing applications that leverate Kafka
* YOU manage your dataplane
* **Features**
  * Automation Recovery
  * Detection of failures
  * Reuses Data from older brokers
  * Times to recovery
  * Security:
    * Integrates with KMS
    * Ecrypts data at rest
    * TSL encryption
    * Logs to Cloudtrail

##### Amazon OpenSearch

* Successor to Amazon Elasticsearch
* Features
  * Quick Analysis
  * Scalable
  * Integrates with IAM
  * Multi-az
  * Integration with a bunch of services
* OpenSearch loves Logs
  * Visualization of Logs think Opensearch
*

#### Networking

##### VPCs - Virtual Data Centers in the cloud

* **VPC Creation:**
  * **Initial**
    * **Default on Creation**
      * Main Route Table
      * Network Network ACL
      * Security Group
  * **Subnet**
    * Subnet not created by default (without all-in option)
    * 5 IP addresses taken by default
      * 10.0.0.0
      * 10.0.0.1
      * 10.0.0.2
      * 10.0.0.3
      * 10.0.0.255
    * **Public**
      * Turn on `Auto Assign Public IPv4 addresses`
      * Create **Internet Gateway**
        * Attach it to the subnet’s VPC
        * Can’t attach multiple gateways to a VPC
      * Create **Route Table**
        * Default root table automatically associates with new subnets
          * Don’t make default public or everything is public
        * Edit the routes (Ingress/Egress Route to Internet):
          * **Destination:**0.0.0.0/0 (internet)
          * **Target:**Internet Gateway
        * Add Explicit subnet association to subnet
    * **Private**
      * **Network Address Translation (NAT Gateway)**
        * Enable instances in a private subnet to connect to the internet or other AWS services while preventing the internet from initiating a connection with those instances
        * NAT Gateway lives in the same AZ as your public subnet
        * 5 gbs  to 45 gbs per second
        * Not associated with security groups
        * Needs an Elastic IP Address
  * **Security Groups**
    * **Rules are stateful**
      * Ok Confusing so follow me here: \
Security Groups the request have a “state” I.E. it kidna tracks the request through it’s lifecycle \
 \
Meaning that if you have a request to a Subnet where inbound port 80/443 is not blocked then you will get a response (even though that response is technically outbound traffic coming for the web server)
      * This is important because ACL are **not stateful**, meaning that if you allow inbound port 80 but not outbound port 80 your request will be serviced but the outbound response would be blocked
  * **Network ACL**
    * First line of defense
    * Works as a firewall for your VPC in/out of one or more of your subnets
    * Default ACL Allows all inbound/outbound traffic by default
    * Custom ACL Denys all inbound/outbound traffic by default
    * Can block IP addresses (security group does not)
    * Stateless
    * Rules evaluated in order starting with the lowest
    * Separate inbound/outbound rules that can allow/deny traffic
    * May need to open “ephemeral ports” (i.e. the random ports that certain things use instead of port 80
    * To block off of an ip address it needs to end in /32
  * **VPC Endpoint**
    * Endpoints between VPC and other services
    * HA/Redundant/Horizontally Scaled
    * Traffic internal to AWS
    * Does not interfere with bandwidth
    * **Options**
      * **Interface Endpoint**
        * Elastic Network Interface with private IP
        * Entry point for traffic headed to a supported service
      * **Gateway Endpoint**
        * Similar to NAT it is Virtual Device you Provision
        * Supports S3 and DynamoDB
  * **VPC Peering**
    * Every VPC must be peered with every other vpc
      * I.E. you can’t do like a central vpc pass through
    * CIDR ranges must be different
  * **Private Link**
    * Uses a LB to connect VPCs
      * Don’t have to connect every vpc to every other VPC
    * Connects VPC to hundreds or thousands of other VPCs
  * **VPN Cloud Hub**
    * Aggregates multiple sites VPNs into a single connection
  * **Direct Connect**
    * Private connection too your data center or office
    * **Dedicated Connection**
      * Physical Ethernet connection associated with a single customer
    * **Hosted Connection**
      * Physical Ethernet connection that is provisited bye a partner (verizion, hp, etc..)
    * Weird… it is like an actual physical location that has their DX servers that they connect to the customer machines in that location run a wire to your office… so interesting
    * Fast/Secure/Reliable/HUGE throughput
  * **Transit Gateway**
    * Connects VPCs and on-premise network through a central hub
    * Removes complex peering relationship
    * Hub-an-Spoke model
    * Regional basis (but you can do it across multiple regions
    * Can do it across multiple AWS accounts
* Virtual Data Center in the Cloud
* Logically isolated part of the AWS Cloud where you can define your own network
* **1 subnet is in 1 AZ (can’t span multi az)**
* Complete control of virtual networking
  * IP Address Range
  * Subnets
  * Routes
  * Network Gateways
* Common IP Adress Ranges
  * **16** - 65,536
  * **24 -**256
  *
* **VPN**
* **Network Access Control List (NACLs)**
  * Can be used to block specific IP addresses
* Common Configuration
  * 10.0.0.0/16
* **AWS Wavelength**
  * Anything mobile wave length
*

##### Direct Connect - Directly connecting on-prem Dataceneters to cloud

##### Route 53 -  DNS

* DNS coverts a Domain Name into IP address
* IPv4 address are running out
* Domain’s must be registered
* **Records**
  * **SOA**
    * Name of server that supplied the data for the zone
    * Admin of the zone
    * Current version of the data file
    * Default seconds for time-to-live file
  * **NS (Nameservers)**
    * Top-level domain servers to direct the traffic to contain the DNS server
    * Google.com example

1. Go to Top Level Domain `.com`
2. Find NS Record `google,com`
3. SOA (start of authority)
    1. Contains our other records
    * **A Record**
        * Address or name of the domain
        * So the A record will look like
            * **Key:**[http://www.acloud.guru](http://www.acloud.guru)
            * **Value:**[http://123.10.10.80](http://123.10.10.80)
        * **TTL**(Time to live)
            * Lower time to live the faster the DNS records change
    * **CNAME**
        * Used to map one domain name to another
            * Used for prefixes
            * `m.cloud.guru` could point to `mobile.cloud.guru`
        * Can not be used for “naked” domain names
            * I.E. no subdomain i.e. no `www.`
    * **Alias Records**
        * Not a DNS record inside the AWS ecosystem
        * Maps resource to a load balancer/CloudFront/S3 bucket
        * Can be used for “naked” domain names (top level can map directly to a s3 bucket or whatever)
            * **In cert always choose Alias record over CNAME**
    * **Routing**
        * **Simple Routing**
            * One record with multiple IP addresses returned in a random ordedr
        * **Failover Routing**
            * Active/Passive
        * **Weighted Routing**
            * Split traffic by percent
            * Health checks can be created
                * If a health check fails that A record is removed from Route 53
        * **Geolocation Routing**
        * **Multi Valued AnswerRouting**
            * Simple routing with health checks built in
        * **Latency-Based Routing**
            * Latency may change from hour to hour
        * **Geo Proximity Routing**
            * Complex routing require traffic flow
            * Probably not on exam
    *

##### Elastic Load Balancers (ELB)

* Automatically distribute traffic across multiple targets (EC2 instance etc)
* Types of LBs
  * **Application Load Balancer**
    * Layer 7 (application aware)
            *
    * Best suited for HTTP/HTTPs traffic
    * Intelligent Load Balancing
      * Can route based on url’s path
    * **Listeners**
      * Listens to traffic from a LB
      * To do HTTPS Load Balancing listeners need SSL/TLS server certificate (to decrypt the data)
      * **Rules**
        * Define rules to determine routes
        * if/then statements then
      * **Target Groups**
        * Where you want to route your traffic too
  * **Network Load Balancer**
    * Layer 4 (connection Level)
    * Performance Load balancer
    * Can handle millions of request per second
    * Can do TLS decryption
      * You must deploy **EXACTLY ONE** SSL server certificate on the listeneter
  * **Gateway Load Balancer**
    * Layer 3
    * Used when deploying inline virtual appliances where traffic is not destined for gateway loadbalancer itself???
  * **Classic Load Balancer**
    * Legacy
    * Can use Layer-7 stuff
    * `X-Forwarded-For` header is used for finding the origin IP address of your users
* Can be configured with Health Checks
* **Sticky Sessions**
  * Stick to the instance
  * If the load balancer is removed from the pool traffic may continue to be directed there
    * Disable sticky sessions to fix this
* **Deregistration Delay/Connection Draining**
  * Allows Load Balancers to keep existing connections open if the EC2 instances are de-registered or become unhealthy
  * **Enabled keeps connections open even if EC2 is unhealthy**
  * **Disable close connection to deregistered instances immediately**
* **Health Checks**
  * **NOT**on by default, must be manually included

##### API Gateway - Serverless replacement for services

* Fully managed services for publishing services
* Lots of integration points
* Supports versioning
* **Features**
  * **Security**
    * Allow you to easily attach Web Application Firewall (WAF)
  * **Stop Abuse**
    * Rate limiting and DDoS proteection
  * **Ease of use**
    * Easy to use (in theory)
    * Integration with other services
* **API Options**
  * **Rest API**
    * API Keys
    * Per Client Throttles
    * WAF integration
  * **HTTP API**
    * Cheaper/Easier
    * Less features
  * **WebSocket API**
    * Websockets
    * Integrates with Functions
* **Endpoint Types**
  * **Edge-Optimized**
    * Default option
    * Sent through CloudFront edge locations
    * Best for global
  * **Regional**
    * Great if clients all in a specific region
  * **Private**
    * Only accessible via VPCs using VPC endpoints
* **Security**
  * **User Authentication**
    * IAM/Amazon Cognito/Custom Lambda
  * **Edge Optimized**
    * Requires ACM cert in **us-east-1**
  * Regional Endpoints
    * Requires cert in same region
  * Can leverage AWS WAF

##### AWS Global Accelerator (GA)

* Improve performance by networking traffic through AWS global network infrastructure
* Primary TCP or UDP traffic
  * Different than CloudFront which functions at HTTP layers
*

#### Monitoring

##### CloudWatch

* Monitoring and Observability
* **Features**
  * **System Metrics**
    * Metrics you get “out of the box”
    * More managed the service the more you get
  * **Application Metrics**
    * Installing agent gives you info
  * **Alarms**
    * Alert us or another team of issues
* **Metric Types**
  * Default Metrics
    * CPU
    * Network Throughput
  * Custom Metrics
    * EC2 Memory Utilization
      * NOT A DEFAULT METRIC, so need to setup a custom metric
    * EBS Storage Capacity
* **Terms**
  * **Log Event**
    * The record of what happened and the timestamp
  * **Log Stream**
    * Collection of log events normally from a single instance
  * **Log Group**
    * A collection of related log streams
* **Log Features**
  * **Filter patterns**
    * Find specific terms
  * **Cloudwatch Insights**
    * **SQL** Like query
  * **Alarms**
    * Alarms can trigger some events such as
      * Creating/Terminating/Rebooting/Recovering EC2 instance
* **Detailed monitoring (more expensive) delivered every 1 minute, standard monitoring delivered every 5 minutes**
* **Logs should go to CloudWatch Logs**
  * **Exceptions:**
    * **You don’t need to process them then they can be sent to S3**
    * **Real Time required may need to use datastream**
    * **AWS Standards should be monitored by AWS Config**

##### AWS X-Ray

* Appsites about applications Request/Response
* Application Insites/Response time/HTTP analysis
* Tracing headers
  * `X-Amzn-Trace-Id` header containing trace info
* **Integrations**
  * EC2 agent
  * ECS
  * Lambda
  * Elastic Beanstalk
  * API Gateway
  * SNS/SQS

### Security

##### IAM

* **Root User:**
  * Root account should be secured by MFA
* **Permissions with IAM:**
*
* **IAM  Policy Documents**
  * IAM does **not** work at a regional level it works a global one
  * Policies can be connected to users but generally they are connected to Groups or Roles
* **Permanent IAM Credentials**
  * Principle of least privilege
  * Users -> Groups
  * Roles are for internal usage (service accounts)
  * One user should be one person
  * _If you create a user with no group or permissions they ONLY have access to change their password_
  * For pragmatic access you need to create an access key (ONLY used for cli)
  * Identity Provider connects auth to your stuff
  * You should always set up password rotation
  * Deny Overrides any allow policies (by default everything is denied)
* **IAM Role**
  * Identity you can create that you can assign permissions too
  * Intended “assumable” by anyone who needs it
  * Roles credentials are Temporary
    * When you assume into the role you get access to do what you need then lose it
  * Allows cross account access
* **Instance Profile**

##### DDoS

* Denial of service
* Not sure if this is on the test but it is neat
* Layer 4 DDoS attack
  * SYN flood attack
    * Happens at the “transport layer”
    * TCP 3-way handshake takes place
      * Client sends SYN packet -> Server replies with SYN-ACK(acknowledgment) -> Client response to ACK
      * After which data is sent to layer 7
    * How does it work?
      * Sends a ton of of SYN packets but just ignores the ACK(acknowledgments) forcing the client to wait for the ACK timeouts
  * Amplification Attack
    * Send a 3rd party server such as [NTP (network time protocol)](https://en.wikipedia.org/wiki/Network_Time_Protocol) a request using a spoofed IP address  
    * So the basic idea if I (and a bunch of my friends) pretend our IP address is the IP address of my victim when hitting the NTP server so the NTP server responds back to the victim’s IP address
* Layer 7 Attack
  * Just sending a bunch of GET/POST request

##### Cloud Trail

* Records AWS Management Console actions
* Records the users/accounts that made the calls
* **What is Logged about the api call**
  * Metadata
  * Identity of the caller
  * Time of the call
  * Source IP Address
  * Request parameters
  * Response elements returned
* Stores in S3
* CloudTrail is for monitoring what is happening NOT a logging solution

##### AWS Shield

* Basic Version is free and gives DDoS Protection against Layer 3/4 attacks
* **AWS Shield Advanced (not free)**:
  * Enhanced protections for applications
  * Always-on, flow based monitoring
    * Near real-time notifications
  * 24/7 access to DDoS Response Team (DRT) to manage and mitigate
  * Protects AWS bill
  * **Cost $3000 a month**

##### AWS WAF

* Web application firewall
* Monitors request forwarded to Amazon CloudFront and Application Load Balancer
* **Block Conditions**
  * IP addresses
  * Country of origin
  * Values in headers
  * Presence of SQL code
  * Presence of a script
  * Specific Strings
* **Layer 7 protection**

##### AWS Network Firewall

* **Physical Firewall**
* **Firewall in front of Internet Gateway**
* Managed by AWS
* **Benefits:**
  * Works with AWS Firewall Manager for central management
  * Provides **Intrusion Prevention System (IPS)**
* Use Cases:
  * Filter Internet Traffic
  * Filter Outbound Traffic
  * Inspect VPC to VPC traffic

##### Macie

* Automated Analysis of Data to search for PII
  * Personally Identifiable Information (PII)
* Will alert you if it finds PII
* Can be sent to EventBridge/Security Hub and other services

##### Key Management Service (KMS)

* Managed service for controlling encryption keys
* Can control who can manage keys separately from who can use them
* **Customer Master Key (CMK)**
  * Contains the key material used to encrypt or decrypt data
* **Hardware Security Module (HSM)**
  * Physical computing device that safeguards and manages digital keys and performs the encryption/decryption
  * Contains one or more secure cryptoprocessor chips
  * **CloudHSM**
    * Cloud-Based HSM that lets you generate and use your own encryption
    * Normal generation works the same way except:
      * No Auto-Rotation
      * You have a dedicated HSM to you not shared underlying hardware
      * Full control of users/groups/keys/etc…
* **Ways to Generating CMK**
  * AWS creates it for you
  * Import material from your own Key Management infra
  * Generate using CloudHSM cluster as part of custom key store feature
* **Key Rotation**
  * If AWS created the key you can have CMK Key rotated every year
* **Policies**
  * Primary way to manage access to your AWS KMS CMK
  * Done with a `resource-based-policy`
    * Called “key-policy” when used with KMS
  * IAM can still prevent users from accessing KMS
  * Can use `grants` in combo with key policy

##### AWS Secrets Manager

* Encryption of secrets
* Automatic rotation
* Fine-grained control using IAM policies
* Reduce risk of creds being compromised
* When Rotation is enabled Secrets manager will rotate secret once to test
  * Could be a question that involves embedded credentials

##### AWS Parameter Store

* Storage of parameters or secrets
* **Free**
* 10,000 limit to the parameters that can be stored
* No secret rotation

##### AWS Centralized Firewall Manager

* Firewall Security management in a single pane of glass
* Benefits:
  * Simplifies Firewall rule management across multiple accounts
  * Ensures compliance of existing AND new applications
*

##### AWS GuardDuty

* Uses machine learning(ML/AI) to monitor for malicious behavior
* **Features:**
  * Centralized threat detection
  * Automated response using CloudWatchEvents and Lambda
  * Machine learning and anomaly detection
*

##### AWS Certificate Manager

* Create/Manage SSL certificates
* Certificates are free (still pay for resources to use it)
* Auto-Renew certs
*

##### AWS Audit Manager

* Continually Audit AWS account
* Automate Audit reporting
* Continuous Auditing (not having to manually do it everr X months)
* Create or use existing Internal Compliance Framework

##### Amazon Inspector

* Automated Security Assessment service to **improve security and compliance**
  * Looks for vulnerabilities (basically the same as our ACS)
* Assessment type:
  * Network - Finds ports that should not be open stuff like that
  * Host - looks for CVEs, agent is required

##### AWS Detective

* Analyze investigate and quickly identity security issues
  * Uses machine learning, statistical analysis, and graph theory to link data sets
* Uses VPC FlowLogs/CloudTrail logs/EKS audit logs/GuardDuty findings over time
* Create visualizations of security issues
* **Root Cause**is the key word to remember

##### AWS Artifact

* Red Herring (mostly a destractor)
* Allows you to download**AWS’s** Compliance reports
  * This is not YOUR companies compliance report

##### AWS Security Hub

* Centralized place for all security alerts across multiple accounts

##### Amazon Cognito

* Provides Authentication/Authorization/User Management without the need for custom code
* **Features**
  * Sign-up/Sign-In options for your app
  * Guest Users
  * Identity Broker between your app and web ID providers (no custom code)
  * Sync user data across multiple devices
* Recommended for all mobile devices
* Allows acces to Server-Side resources and AppSync resources
* **User Pool**- Provides sign in options
* **Identity Pool**- gives access to AWS services
* Oauth2 Flow
* Architecture:
  * Device Connects to `User Pool` to authenticate and get token
  * Exchange that token against an `Identity Pool` to get AWS credentials
  * Use AWS credentials to access AWS services

##### IAM

* Every resource has an Amazon Resource Name: ARN
*

<p id="gdcalert2" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image2.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert3">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>

![alt_text](images/image2.png "image_tooltip")

    * `::` is an omitted value meaning there is no value. We do that for IAM above cause IAM is a global resource so there is no `region`

* IAM Policies
  * JSON document that defines permission
  * **Types**
    * Identity Policy
      * Connected to a role or user
    * Resource Policy
      * Connected directly to a resource
* Permissions Boundaries
  * Controls maximum permission an IAM can grant
  * Prevent overly permissive boundaries
*

##### Security Groups

* Security Groups are virtual Firewalls for EC2 Instances
* To let EVERYTHING in open up `0.0.0.0/0`
  * Should only be done for web traffic (i.e. 80/443)
* Default on create all inbound is blocked all outbound is allowed

### Automation

#### Cloudformation

* Infrastructure as Code
* Create Templates in JSON or YAML formats
* Most (but not all) AWS resources are supported
* **Steps:**
  * Write your code
  * Deploy as a stack or stack set
* Stack are Regional
  * Easily deploy into multiple regions
* Change Sets - Let you preview your stacks
* **Template Sections**
  * **AWSTemplate FormatVersion -**OPTIONAL
  * **Parameters** - OPTIONAL
    * Inputs
  * **Mapping** - OPTIONAL
    * Lookup values such as looking up region information
  * **Resources** - REQUIRED
    * Resources you are creating
  * **Outputs** - OPTIONAL
    * Output of the template (like urls and stuff)
    * Can be referenced from other stacks
  * **Transforms** - OPTIONAL
    * Basically templating
    * Lets you make changes to the template before it is proccessed

#### Elastic Beanstalk

* PaaS - single-sto application model.
  * You deploy your code and the provider builds all the architecture for you
* Elastic Beanstalk is a PaaS in aws for developers
* Benefits:
  * Automation - Create a template of how you want your env to look
  * Handles the deployments for you allowing developer to focus on code
  * Handles the creation of underlying architecture such as EC2
* Supported Most popular langauges

#### SystemManager

* Suite of tools designed to let you view/control/automate bothy your managed  instances in AWS and on-premis
* Important that you are comfortable with System Manager Agent SSM
  * Installed on managed instances
* **Capabilities**
  * Predefined Playbooks(documents) for resource management
  * Run remote commands
  * Automate Patching
  * Parameter Store secures secrets and application configuration
  * Maintenance Windows can be defined
  * **Session Manager** securely connect to managed commute without needing SSH access
* **Concepts**
  * **Logging**
    * Allows you to log all usage during sessions
      * Both to CloudWatch and CloudTrail
  * **Agent Supports both Linux and Windows**
    * No need for SSH or RDP
    * No need to open ports
* **SSM Agent**
  * Allows Amazon to make updates and manage configuration
  * **Supported services**
    * EC2
    * Edge Devices
    * On-Prem servers
    * Custom VMs
  * IAM permissions need to be configured correctly
  * It is possible to install SSM agent on your own on-prem devices

### Caching

##### CloudFront

* Content Delivery Network (CDN)
  * Allows us to take our static content and distribute them ot to edge locations
* Speed/Images take too long to load
* **Features**
  * Security
    * Defaults to HTTPS connection
    * Can use custom SSL
    * Puts secure connection on S3 sites
    * Can restrict access to your content using cookies
  * Global Distribution
    * Can’t pick specific countries just general areas
  * Endpoint Support
    * Supports non-AWS endpoints (on-prem)
  * Expiring Content
    * Force an expiration of cache if TTL is too long
*

##### ElastiCache

* AWS managed version of **Memcached** and **Redis**
* **Memcache**
  * Simple Database Caching Solution
  * Not a database
  * No failover/Multi-AZ support
  * No Backups
* **Redis**
  * Supported acaching solution
  * Functions as a standalone database
  * Failover and Multi-AZ support
  * Supports backups

##### DynamoDB Accelerator (DAX)

* In-Memory Caching solution
* Lives inside the VPC you specify
  * Location is important
* **Dax**vs **Elasticache**
  * **DAX**
    * ONLY for dynamoDB
  * **Elasticache**
    * More flexibility and excels in front of RDS

##### Global Accelerator

### Governance

##### Organizations

* Create and manage multiple AWS accounts fron a centralized location
* **Account Types**
  * **Management Account**
    * Payer Account
    * Primary account that host and manages Org
  * **Member Account**
    * All other accounts
* **Features**
  * **Consolidated Billing**
    * One bill for everyone
  * **Usage Discount**
    * Discount for resources spread across multiple accounts
  * **Shared Saving**
    * Any saving effective for every account in the org
* **Concepts**
  * **Multi-Account**
    * Best Practice
    * Multiple accounts with central management
  * **Tag Enforcement**
    * Require all AWS resources to use specific tag
  * **Organizational Unit (OU)**
    * Logic grouping of multiple accounts
  * **Service Control Policies (SCPs)**
    * JSON policies to apply to OUs or specific accounts to restrict actions
  * **Management Account**
    * SCPs don’t effect management accounts
  * **Account Best Practices**
    * Centralized logging account for Cloud-Trail
* Use Cross Account Role access to access stuff between accounts

##### AWS Resource Access Manager (RAM)

* Free service that allows you to share AWS resources with other accounts inside or outside your organization
* Shared resources mean you don’t have to recreate them each time
* You do have to pay for the shared resources
* **Resources that can be shared:**
  * Transit Gateways
  * VPC Subnets
  * License Manager
  * Route 53 Resolver
  * Dedicated Host
  * Many More
* **Owners/Participant**
  * **Owners**
    * Create and manage VPC resources that get shared
    * **CANNOT**delete or modify resources deployed by participant accounts
  * **Participant Accounts**
    * Able to provision services into shared VPC subnets
    * **CANNOT**modify or delete the shared resources (I.E. VPCs)

##### AWS Config

* Inventory management and control tool
  * Enforcement
* Historical record of your configuration history
* Create rules so that configs conform to your requirements
* Per Region Configuration
* Results can be aggregated across Region/Account
* **AWS-Managed Config Rules**
  * A set of rules that follow AWS best practices
* **Custom Config Rules**
* Rules can be on a schedule or triggered by a config change
* NOT PREVENTATIVE - Will alert you but it won’t prevent config changes
* Not Free - Billed based on number of configs you are looking at
* **Remediation**
  * Can be done through SSM Automation Documents
    * AWS provides some of these
    * Config can appy retry
  * Alerts can be sent to SNS
  * **EventBridge** can be used to kick of SQS or Lambda functions based on config changes

##### AWS Directory Service

* AWS’s fully managed version of Active Directory
* **Types**
  * **Managed Microsoft AD**
    * Entirely Managed by AWS
  * **AD Connector**
    * Tunnel connection to On-Prem
  * **Simple AD**
    * Similar version (generally not on exam)

##### AWS Trusted Advisor

* Fully managed best-practice auditing tool
  * Auditing only
* Uses constantly evolving Industry/Customer best practices to check AWS accounts
* Works at an _Account Level_recommendation for whole account not specific services
* Integrates with Event Bridge
* **Categories**
  * Cost Optimization
  * Performance
  * Security
  * Fault Tolerance
  * Service Limits

##### AWS Control Tower

* Setup and govern multiple accounts
  * Automate account creation and security controls
* Best way to manage multiple account environments
* **Features:**
  * **Landing Zone**
    * Well-architected, multi-account env based on compliance and security best practices
    * Basically it is your entire setup
  * **Guardrails**
    * High-level rules for continuous governance
    * **Types:**
      * **Preventative**
        * Prevents folks from actually apply a config
      * **Detective**
        * Alerts on misconfigured resources but still allows them
        * Uses **AWS Config Rules**
  * **Account Factory**
    * Templates for new accounts
  * **CloudFormation Stackset**
    * Templates for common resources
  * **Shared Accounts**
    * Three accounts used by control tower
    * **Management**
      * Puts SCPs/GaurdRails in team accounts
    * **Log Archive**
      * Logs forwarded to S3 bucket in this account
    * **Audit**
      * Security and Drift notifications sent to SNS on this account

##### AWS Licenses manager

* Simplifies managing software licensing
  * Information Only
* Centralize licenses across AWS accounts and on-premises
* AWS-hosted license management/Prevent License Abuse/ Hybrid Env Licenses

##### AWS [Personal] Health Dashboard

* Visibility of resource performance
  * Audit Only
* Can automate actions via Amazon EventBridge
* **Concepts:**
  * **AWS Health Event**
    * Notification on behalf of AWS services or AWS
  * **Account-Specific Event**
    * Events specific to your AWS account or organization
  * **Public Event**
    * Events on services that are public or not in an account
  * **AWS Health Dashboard**
    * **Dashboard showing above events**
  * **Event Type Code**
  * **Event Type Category**
  * **Event Status**
  * **Affected Entities**

##### AWS Service Catalog

* Allows Organizations to to create and managed pre-approved set of services/CloudFormation templates

##### AWS Proton

* Creates and manages infra and deployment tolingfor users and serverless container applications
* Auto provisions resources, CI/CD, and deploys the code
* Supports Terraform

##### AWS Well-Architected Framework Tool

* Tool that measures your account against the established Best Practices

#### Cost

##### Cost Explorer

* Cost Explorer lets you visualize and analyze cloud cost
* Has building forecasting up to 12 months
* Use **Tags** best way to track your spends

##### AWS Budgets

* Plan and set expectations around cloud cost
* Set alerts for exceeding spend

##### AWS Cost and Usage Reports (**CUR**)

* Most comprehensive set of cost and usage data available
* Publish billing reports to an Amazon S3 centralize collection bucket
  * Once a day update
  * Integrates with RedShift/Quickshift/etc…
* Detailed cost breakdowns / Delivery of daily usage reports / Saving Plans Utilizations

##### AWS Compute Optimizer

* Analyzes resources and prints reports for how you can optimize those resources
* **Works With**
  * EC2
  * Autoscaling Group
  * EBS \
Lambda
* **Supported Accounts**
  * Standalone Account
  * Member Account
  * Management Account
* Disabled by default

##### Saving Plans

* Flexible/Lower prices
* Require Commitments
* **Saving Plans**
  * Compute Saving
    * Flexible
    * Up to 66% saving
  * EC2 Instance Savings
    * Stricter
    * Up to 72% saving
  * SageMaker Saving
    * Only SageMaker instances
    * Up to 64%

### Migration

##### Ways to migrate Data

* Internet
  * Existing Connection
  * Potentially very slow
  * Security Risk
* Direct Connect
  * Faster and more secure
  * Expensive if not needed after migration
* Physical
  * Great for periodic big data migration

##### Snow Family

* Hard drives that Amazon ships to you
* A cost effective/secure way that AWS physically ships you your data from S3 buckets to load onto your on-prem environments

###### Snowcone

* Smallest snow (physically very small)
* 8 TB storage 4 GB of memory and 2 CPUs
* Easily Migrate data after you processed it
* IoT sensor integration
* Perfect for edge computing

###### Snow Edge

* 48 to 81 TB
* Various cpu/gpu
* Perfect for a boat/one time migration
* Can use the **Schema Conversion Tool** from DMS for migration to/from AWS
  * Change Data Capture (CDC) compatible (somehow?)

###### Snow Mobile

* Largest (also don’t need to know for exam)
* 100PPB of storage
* Designed for exabyte-scale data
* A cost effective/secure way that AWS physically ships you your data from S3 buckets to load onto your on-prem environments

##### Storage Gateway

* Hybrid cloud storage service that allows us to merge on-prem resource with the cloud
* **File Gateway**
  * Most likely on test
  * **NFS**/SMB mount
  * Backs up data from on-prem into S3
  * Exam Question users don’t have enough on-prem storage (set up cached file gateway)
  * Extend on-prem storage into cloud
  * Helps with migration
* **Volume Gateway**
  * **iSCSI** mount
  * Cached or stored mode
  * Create EBS snapshots then restore them inside cloud
  * Easy way to migrate from on-prem
* **Tape Gateway**
  * Replace **physical tapes**
    * (what the hell are physical tapes??? Like Elvis or MJ?)

##### AWS Datasync

* Migration tool for moving data NFS for **one time migration**
* Agent Based
* S3 / EFS / FSx are supported

##### AWS Transfer Family

* Move files in/out S3/EFS using SFTP/**FTPS**/FTP
* Legacy

##### AWS Migration Hub

* Central place to track Server Migration Service(SMS) and Database Migration Tool(DMS)
* Discovery Agent/Discovery hub are how AWS figures out how hard migration is

##### Service Migration Service(SMS)

* Schedule the movement of a VM into AWS
* VM -> AMI -> EC2 Instance

##### Database Migration Service(DMS)

* Schedule Migration of Oracle/MySQL db to Aurora Database
* One time or continuously replicate ongoing changes
* **AWS Schema Conversion Tool**
  * Convert one engine type to another
* **Migration Types**
  * Full Load
    * All existing data
  * Full Load and Change Data Capture (Change Data Capture)
    * All Existing Data
    * AND changes to source tables during migration
    * Only one that guarantees transactional integrity (full may lose data that was loaded during migration)
  * CDC Only
    * Only changes from the source database

##### AWS Application Discovery Storage

* Helps you plan migration to AWS after analyzing on prem servers
* **Discovery Type**
  * **Agentless**
    * OVA File to identify host and VMs
    * Gets resource allocation and Utilization data
  * **Agent Based**
    * Installed discovery agent
    * Gets more information

##### AWS Application Migration Service

* Lift and Shift service
* Replicates source servers into AWS for quickie cutover
* **RTO -** Minutes spending on boot time
* **RPO**- sub second range

### Frontend Web and Mobile

##### AWS Amplify

* Tools for frontend web and mobile developers
* **Amplify Hosting**
  * Supports for common frameworks (react, angular, vue)
  * Allows for production/staging environments
  * Server Side rendering(SSR) support
    * Can’t be done in S3
* **Amplify Studio**
  * Easy Authentication Implementation
  * Simplified Development Environment (ide?)
  * Ready to use

##### AWS Device Farm

* Application testing service for Android/iOS/web apps
  * This uses actual phones/tables apparently
* Features:
  * Automated
  * Remote Access

##### Amazon Pinpoint

* Lets you engage with customer through a variety of different channels
* **Features**
  * **Projects**
    * Collections of the other stuff in this list
  * **Chanels**
    * How you want to message (SMS, Email, etc..)
  * **Segments**
    * Dynamic or imported collection of users
  * **Campaigns**
    * Specific initiatives tailored to specific audiences
  * **Journeys**
    * Multistep engagement
  * **Message Templates**
  * **Machine Learning**
* **Useage**
  * **Marking**
  * **Transaction Notification**
  * **Bulk Messages**

### Machine Learning

##### SageMaker

* Load your data in to Build/Train AI models
* Access to Jupyter Notebook
* **Elastic Inference (EI)**
  * CPU-Based Instances
    * Cheaper than GPU

##### Amazon Rekognition

* Uses Deep Learning to recognize image data
* **Usage:**
  * **Content Moderation**
  * **Face Detection**
  * **Streaming VIdeo Detection**
    * Like Ring camera

##### Currently not on exam

###### Amazon Comprehend

* Natural-Language Processing
  * Are customers positive/negative about your brand

###### Amazon Kendra

* Create intelligent search service

###### Amazon Textract

* Extract text/handwriting/tables from physical documents

###### Amazon Forecast

* Uses previous time-series data to forecast future data

###### Amazon Fraud Detector

* AI service to detect fraud
* Can be used for loyalty or “free trial” abuse

###### Amazon Polly

* Turns text into speech with language and accents
* This + Lex + Transcribe is how Alexa

###### Amazon Transcribe

* Speech to text

###### Amazon Lex

* Build conversational models into your applications (like virtual assistants)

###### Amazon Translate

* Translates content into different languages

### Media

###### Amazon Elastic Transcoder

* Transcode media files from their OG source to the optimized format for specific devices

###### Amazon Kinesis Video Streams

* Stream content from a large number devices to AWS (Ring Camera)

### High Level Concepts

##### Disaster Recovery

* **Recovery Point Objective (RPO)**
  * If shit fails how long back in time can you afford to lose?
  * The lower the time the greater the cost
* **Recovery Time Objective (RTO)**
  * How quickly after a failure can you afford to have your site down
  * The lower the time the greater the cost
* **Backup and Restore**
  * If your site goes dow you have a way to restore it (i.e. create new EC2)
* **Pilot Light**
  * So you have your stateful stuff replicated (i.e. your DB) that won’t cost as much as Active/Active.
    * If your crap goes down then you replicate the stateles part (i.e. your web server)
* **Warm Standby**
  * Scaled down version of your architecture in a different region
* **Active/Active Failover**
  * Server traffic to both sites

##### Well-Architected Framework

* **Pillars**
  * **Operational Excellence**
  * **Reliability**
  * **Security**
  * **Performance Efficiency**
  * **Const Optimization**
  * **Sustainability**
