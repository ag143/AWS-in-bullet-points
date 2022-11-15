# Introduction

## AWS Regions

- AWS has regions all around the world, it is a separate geographic area. e.g. `us-east-1` `eu-west-1` `ap-northeast-1`
- **Pricing**
  - ❗ Costs of products may vary from region to region.
  - There's a [charge for data transfers](#data-transfer-charges) between regions
- Each region has at least two availability zones
  - E.g. `us-east-1a`, `us-east-1b`
  - **Availability zone** is one or more physical data centers.
    - They're separate from one another. They're isolated deliberately to survive any unforseen circumstances like disasters.
  - Name of AZ can correspond to different data centers in one account than in other.
    - E.g. same data center can be `us-east-1a` for one account while `us-east-1b` for another, this is purposefully done by aws to distribute resources across AZ.
- Each AWS console are region scoped (except IAM, Route53 and S3{Anyhow buckets inside are regional})
  - I.e. changes, actions are specific to a region
- [See where the regions are and how many AZ's within them](https://aws.amazon.com/about-aws/global-infrastructure)
- There are also many ***Edge Location***s for caching content for CloudFront (CDN).

## Basic Terminology

- **Availability**
  - Percentage uptime
    - Downtime can be due to faults or other such as planned maintenance.
  - E.g. the storage system is operational and can deliver data upon request
  - 📝High availability in AWS = Multiple AZ
  - **Resiliency**
    - Being able to adapt under stress or faults in order to avoid failure
      - Self-recover & remain functional to customers
    - Often provided by redundancies and automatic re-routing of operations within a system
  - **Durability**
    - Long-term data protection
    - E.g. the stored data does not suffer from bit rot, degradation or other corruption
  - **Redundancy**
    - The inclusion of extra components in a system so it will continue to function in case of a component failure.
    - E.g. automatic back-ups to Azure in case of AWS failure
  - **Throughput**
    - Measures of how many units of information a system can process in a given amount of time
- **Consistency**
  - Application is expected to remain stable
  - **Reliability**
    - Percentage uptime, considering the downtime due only to faults
    - Measures consistency
    - Ability of a system to recover from infrastructure or service disruptions.
    - 💡 Kill unhealthy & spin up new => Quicker than human debugging
  - **Fault-tolerance**
    - App ability to self-detect and correct all types of problems in its *environment*
    - Requires extra redundancy to work in inconsistent environments.
    - 📝 Elastic Load Balancer, Route 53 with Auto Scaling Groups
  - **Robustness**
    - App ability to self-detect and correct all types of problems from its *inputs*.
    - Can work with inconsistent data.
- **🤗Trivia**: In distributed data store, CAP Theorem means you cannot reach more than two of: Consistency & Availability & Partition tolerance (packages being dropping/delayed between nodes).
- **Billing**: You can see your bills in -> My billing dashboard -> bills

## Basic Services

- **AWS Health**: Provides ongoing visibility into the state of your AWS resources, services, and accounts.
  - **Health events**
    - Maintainance events that'll affect your services
    - Can set-up CloudWatch alarms.

## AWS Support

- Support types

  |   | Basic | Developer | Business | Enterprise |
  | - |:-----:|:---------:|:--------:|:----------:|
  | Cost | Free | $29/mo | $100/mo | $15.000/mo |
  | Use case | Account and billing questions only | Experimenting | Production use | Mission-critical use |
  | Tech support | *NO* | Business hour via e-mail | 24x7 via email, chat & phone | 24x7 via email, chat & phone |
  | SLA | ✕ | 12-24 hrs at local business hours | 1 hr response to urgent support cases | 15 min to critical support cases w/ priority |
  | TAM & Support Concierge |  ✕ | ✕ | ✕ | ✓ |
  | Support cases | ✕ | 1 Person, unlimited cases | Unlimited contacts / cases | Unlimited contacts / cases |

- **AWS Trusted Advisor**
  - High level AWS account assessment, very useful to know whether our account is sticking to AWS' best practices.
  - Serverless global service
  - **Pricing**: Most checks requires different support plans (developer, only 7).
  - Analyzes your AWS accounts and provides AWS-defined recommendations based on core checks:
    - **Cost Optimization**: e.g. Low utilization Amazon EC2 instances, Idle Load Balancers, unassociated Elastic IP address...
    - **Performance**: e.g. High Utilization Amazon EC2 Instances, Large Number of Rules in an EC2 security group
    - **Security**: e.g. "specific ports unrestricted", "Amazon EBS public snapshots"
    - **Fault Tolerance**: e.g. "VPN Tunnel Redundancy", "Age of EBS snapshots", "EC2 AZ Balance".
    - **Service Limits**
      - **Free** to 📝check if you're getting close to limits
      - E.g. "EC2 On-Demand instances", "Cloud formation stacks"
  - Can enable weekly email notification from the console

## AWS Pricing

- More information about AWS service pricing, see [Cloud Services Pricing](https://aws.amazon.com/pricing/services/).

## AWS Pricing Calculator

- Provides an estimate of AWS fees and charges for different cases/services.
- ❗ Doesn't include any taxes
- Uses [AWS Price List API](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/price-changes.html)
- **Quick estimates**
  - Ballpark estimate while requiring minimal information and parameters
- **Advanced estimates**
  - Allows you to fine-tune the estimate
- See [calculator.aws](https://calculator.aws/)

## Data transfer charges

- Applies globally to all AWS services
- Ingress: Free
- Egress
  - Free within same region, costs extra for Internet and other regions.
  - 💡 Always double check, not so cheap
# Interacting with services - AWS CLI, SDK

## Interacting with services

- All services can be called using APIs and you can talk to APIs using:
  - Console (web), SDK's and CLI that make API calls.

## AWS CLI

- Interact with AWS Proprietary services (S3, DynamoDB etc.) over www.
- Can be installed on Linux/Windows/Mac OS
  - Python & pip is bundled in Windows but require separate installation in Mac OS / Linux
  - `aws --version` get the version of CLI, Python, OS, and *botocore*
- Python based using *botocore* / *boto3* (AWS SDK for python)
- Requires access credentials
  1. Can be found at *IAM -> Users -> Security credentials*
  2. Create your **access key** (can download as CSV)
     - ❗ You cannot restore them, they're generated & shown once
     - To revoke it: disable or delete your key.
  3. Run `aws configure` & paste *Access Key Id* and *Secret Access Key* when prompted
     - You can optionally set default region & output format
       - Otherwise the region is `us-east-1` by default
     - You can run `aws configure` to check if they're saved & modify them.
  4. Credentials are saved in `~/.aws` in Mac OS & Linux
     - In `config` file you have region and output settings.
     - In `credentials` file your access key id and secret key are saved
  5. You can run `help` to get more information about command e.g. `aws s3 ls help`
- **AWS CLI on EC2**
  - ❗ Bad & insecure way:
    - Run `aws configure` and save your credentials
    - Your personal credentials are personal and should only be on your personal computer (not on EC2)
    - Risks:
      - If EC2 is compromised -> Your account will also be compromised
      - If EC2 is shared -> other people may perform actions impersonating you
  - 💡 Better way: AWS IAM Roles
    - IAM Roles can be attached to EC2 instances.
      - ❗ One EC2 instance can have one IAM role at a time
        - But same role can be attached to many other EC2 instances
      - 📝 Also, Roles are not directly attached to EC2 instance, but are attached to instance profile. Instance profile is a container, where roles are attached.
    - IAM Roles can come with a policy authorizing exactly what the EC2 instance should be able to do e.g. AdminAccess or List buckets in S3.
    - EC2 instances can use these profiles automatically without any additional configurations
  - 📝If EC2 need access to other aws resources, never put credentials in it, always use IAM roles
  - Flow:
    - Create IAM role for the resource EC2 will access
    - EC2 portal -> Right click on instance -> Settings -> Attach IAM role
- **S3 CLI**
  - `aws s3 ls`: Lists all buckets available
  - `aws s3 ls s3://bucketname`: Lists contents of the bucket
  - `aws s3 cp s3://bucketname/pic.jpg pic.jpg`: Copy a local file, or S3 objects to your computer or from your computer.
  - `aws s3 mb s3://bucketname`: create a new bucket
  - `aws s3 rb s3://bucketname`: delete existing bucket
  - `aws configure --profile <profile-name>`: Allows you to name a profile and work with multiple profiles. Future commands should have `--profile <profile-name>` parameter to use the profile.
  - Use appropriate region to the end of the command in all the above, remember S3 buckets are regional.

## AWS SDK

- Official SDK's are: Java, .NET, Node.js, PHP, Python (named boto3/botocore), Go, Ruby, C++ ...
- AWS CLI uses the Python SDK
- Recommended to use the **default credential provider chain**
- It works seamlessly with:
  - AWS credentials at `~/.aws/credentials`
  - Use only on your computers or on premise
    - Even better to create a user specifically for that one on-premise server
  - Instance Profile Credentials using IAM Roles
  - Use 100% within AWS services e.g. EC2 machines.
  - You can use environment variables (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`)
    - ❗ Less recommended
- **Exponential Backoff**
  - E.g. request 1 fails -> wait 2 sec -> request 2 fails -> wait 4 sec -> request 3 fails -> wait 8 sec ...
  - Any API that fails because of too many calls needs to be retried with Exponential Backoff
    - Applies also to rate limited API
  - Retry mechanism included in SDK API calls
  - Ensures you don't overload API

## Penetration testing

- You can carry out security assessments or penetration tests against own AWS infrastructure without prior approval for 8 services: 1.EC2 Instances, NAT Gateways & ELB 2.RDS 3.CloudFront 4.Aurora 5.API Gateways 6.Lambda & Lambda Edge functions 7.Lightsail 8.Elastic Beanstalk.
  - You can request for inclusion of additional services.
- ❗ Prohibited Activities: DDoS, DoS, DNS zone walking, port/protocol/request flooding
# Queues

## Patterns of communication

- Two patterns of communication:
  - **Synchronous** (application to application)
    - Can be problematic if there's sudden spikes of traffic
      - E.g. if you need to suddenly encode 1000 videos but it's 10?
    - Better to decouple your application with queues for scalability and resiliency.
  - **Asynchronous / Event based** (application to queue to application)
    - Decouples applications
    - Services can scale independently from applications
    - 💡 **Exactly-once delivery**
      - One of the hardest things to achieve in distributed systems.
      - Better to design your applications to be **idempotent**: not to be affected adversely when processing the same message more than once.

## Comparison

- **SQS vs SNS**
  - Choose SNS for if e.g. someone uploads picture and listeners need to different stuff (send thank you email, crop thumbnail & save in S3).
  - Choose SQS for application to application communication.
  - **Fan out**: SNS to SQS to listeners = good of both worlds.
- **SQS** vs **SNS** vs **Kinesis Streams** vs **Amazon MQ**

  | Attribute | **SQS Standard** | **SQS FIFO** | **SNS** | **Kinesis Streams** | **Amazon MQ** |
  | --------- |:----------------:|:------------:|:-------:|:-------------------:|:-------------:|
  | **Type** | Direct queue | Direct queue | Pub & Sub (with topics) | Data ingestion | Direct queue + topic |
  | **Consumption** | Pull | Pull | Push | Pull | Pull + Push |
  | **Latency** | ⭐⭐⭐ | ⭐⭐☆ | ⭐☆☆ | ⭐⭐⭐  | ⭐⭐⭐ |
  | **FIFO** | 👎 Best effort | 👍 FIFO | 👎 Best effort | 👍 Per partition key | 👍 Supports FIFO |
  | **Delivery** | 👎 At-least-once | 👍 Exactly-Once Processing | 👎 At-least-once | 👍 Per partition key | 👍 Supports FIFO |
  | **Retention** | 14 days, default: 4 | 14 days, default: 4 | None | 1 day (default), up to 7 days | Persisted in file until configured max file size reached |
  | **Throughput / limits** | None | 300 transactions per second per API | Max topics / subscribers | Write/read per shard, can scale out shards. | Requires provisioning eg `mq.t2.micro`. Users, brokers, configurations and data storage is limited. API is throttled. |
  | **Can delay** | ✓ | ✓ | ✕ | ✕ | ✕ Only on redelivery |
  | **Out of box dead letter queue** | ✓ | ✓ | ✕ | ✕ | ✓ |
  | **AWS integrations** | 👍 Many | 👎 Missing many e.g. S3/CloudWatch/SNS/Lambda DLQ... | 👍 Many | 👌 Some: Data Firehose, S3, Kinesis Data Analytics, Lambda, SDK uses DynamoDB for checkpoints | 👎 Almost none, requires code. |
  | **Consumer type** | Same type | Same type | Different consumers can be different stuff with the event from the topic | Can be different per partition, as same consumer can listen to same partition | Can be same (direct queue) or different (topics) |
  | **Use cases** | Fast, decouple/integrate apps, visibility time out, message level ack/fail, message delay. | SQS + strict ordering, deduplication, decouple/integrate apps | Pub & sub (1 event to different apps), emails/SMS/.., push notifications, fan out (SNS to SQS -> good of both) | Analytics, logs, IoT.. Related records to same processor (partitioning), ordering, multiple apps to consume same stream concurrently, consume records a few hours later. | Lift & shift |

## Other services

- **Amazon MSK**: Amazon Managed Streaming for Apache Kafka (Amazon MSK)
- **AWS IoT**: Bi-directional communication between Internet-connected devices such as sensors, actuators, embedded micro-controllers, or smart appliances and the AWS Cloud.
# Amazon SQS (Simple Queue Service)

- Fully managed queue service
- 🤗 Oldest offering (over 10 years)
- Message retention period - between 1 minute to 14 days - default: 4 days
- No limit to how many messages can be in the queue
  - ❗ You need to process those within the time limit
- Flow
  - One or many ***Producers*** send ***messages*** to ***SQS Queue***
  - One or many ***Consumers*** poll ***messages*** from ***SQS Queue***
- **Scaling**
  - Scales automatically to 10.000 messages per second.
  - Horizontal scaling in terms of number of consumers.
- **Security**: You can use Server-Side Encryption using KMS.
- You can send messages & simulate a consumer with polling on the console.

## Producing messages

- ❗ Maximum message size - between 1 and 256 KB - default: 256 KB
- *(Optional)* Add message attributes (key & value metadata)
- *(Optional)* Provide delay delivery
  - **Delay Queue**
    - Delay a message up to 15 minutes e.g. consumers don't see it immediately.
    - ***At queue level:*** Can set a default (default is 0 seconds -> visible right away)
    - ***At message level:*** Can override the default using the `DelaySeconds` parameter.
- Get back:
  - Message identifier
  - MD5 hash of the body

## Consuming messages

- Polls SQS for messages
  - ❗ Up to 10 messages at a time
- Consumer needs to ***delete messages*** from queue to prevent the message from being received.
  - Use API `DeleteMessage` using the message ID & receipt handle after successful processing.
- Receive message wait time - between 0 and 20 seconds - default: 0 seconds

### Visibility Timeout

- When a consumer polls a message from a queue, the message is "invisible" to other consumers for a defined period, the **VisibilityTimeout**
  - Messages remain in queue while being processed i.e. they're not returned to subsequent receive requests for duration of ***visibility timeout***
- Set between 0 seconds and 12 hours (default: 30 seconds)
  - If too high (e.g. 15 min): If consumer fails you must wait a long time before processing again.
  - If too low (e.g. 30 seconds): Message can be processed more than once before one consumer is done.
- 📝 Use API `ChangeMessageVisibility`: to change the visibility while processing a message.

### Dead Latter Queue (DLQ)

- If consumer cannot handle the message, the message goes back to the queue after visibility timeout.
- If it happens many time then the message can be put into ***dead letter queue***.
  - Threshold of how many times is called **redrive policy**.
- DLQ is another SQS queue.
  - Create a DLQ first then designate it dead letter queue
  - 💡 Make sure to process the messages in the DLQ before they expire

### Pulling types

- **Short Pulling**: Queue responds right away to *"Do you have messages?"*
- **Long pulling**
  - Eliminate empty responses by allowing SQS to wait until a message is available in a queue before sending a response.
    - The response to the `ReceiveMessage` request contains at least one of the available messages or unless the connection times out.
      - ❗ Up to the maximum number of messages specified in the `ReceiveMessage` action.
  - Recommended as it decreases the number of API calls
    - Increases latency and efficiency of your application.
  - The wait time can be between 1 sec to 20 sec (20 sec preferable)
  - Can be enabled at the queue level or at the message level using `WaitTimeSeconds` API.

## Queue types

- **Standard Queue**
  - At least once delivery is guaranteed
    - 📝Can have ***duplicate messages*** occasionally
  - By design as it' very high throughput, AWS calls it unlimited throughput.
  - Best effort ordering: 📝Can have ***out of order messages***
  - Usually integrated with S3, CloudWatch Events, SNS, Auto Scaling Hooks, IoT rule, lambda DLQs..
  - ❗ Limitation of 256KB per message sent
- **FIFO Queues**
  - Extends standard queue: have all capabilities.
  - Lower throughput: ❗ Up to 300 messages per seconds (for receive, delete & send)
    - If you batch messages to 10 then it'll be 3000 per second
    - Soft limit, you can request for higher limits
  - Ensures ***ordering*** and ***exactly-once processing***.
  - ❗ No per message delay (only per queue delay)
  - Ability to do content based **de-duplication**
    - Avoid duplicate messages during the deduplication interval
    - Can be
      - **Content based**: Uses hash of the message
      - **Deduplication ID**: 5-minute interval de-duplication using "Deduplication ID"
        - Messages with same deduplication ID are accepted but only one is delivered during 5-minute.
  - Name of the queue must end with `.fifo`
  - **Message Groups**
    - Possibility to group messages for FIFO ordering using `Message GroupID` tag on message.
    - Only one worker can be assigned per message group, so that messages are processed in order.
- Differences

  | Queue type | Delivery type | Ordering | Delay | Deduplication | Latency | AWS integrations |
  | ---------- | ------------- | -------- | ---- | -------------- | ------- | --------- |
  | **Standard** | At least once | Best effort | Per message and queue | NaN | <10 ms | Many |
  | **FIFO** | Exactly-once | FIFO, can also use custom `GroupID` tag to ensure one worker per group in order  | Only per queue | Content (hash) or custom ID based | <100 ms | Less, missing e.g. S3/CloudWatch/SNS/Lambda DLQ... |
# Amazon SNS (Simple Notification Service)

- SNS has pub/sub model
- Event producer only sends message to one topic
- As many event receivers (subscriptions) as we want to listen
- Different from SQS where you have direct integration between listeners & senders.
- **Retention**: ❗ You have to process the data right away or it's lost
- Flow
- ***Event producer*** publish messages to a ***SNS topic***
  - ❗ Up to 100.000 topics (soft limit)
- ***Subscriptions*** listen a specific ***SNS topic***
  - ❗ Up to 10.000.000 subscriptions per topic (soft limit)
  - Each subscriber get all the messages but can filter.
- **Subscribers** can be
  - SQS
  - HTTP/HTTPS (with delivery retries)
  - Lambda
  - Emails, SMS messages, Mobile Notifications
- High availability with managed replication to 3 AZ.
- Pay as you go pricing
- **Integrations**: SNS integrates with a lot of Amazon Services
- Some services can send data directly to SNS for notifications
- E.g. CloudWatch for alarms, Auto Scaling Groups notifications, Amazon S3 on bucket events CloudFormation (upon state changes => failed to build etc.) ...
- Publish types
  - **Topic Publish** (within your AWS server - using the SDK)
    1. Create a topic
    2. Create a subscription (or many)
    3. Publish to the topic
  - **Direct Publish** (for mobile apps SDK)
    1. Create a platform application
    2. Create a platform endpoint
    3. Publish to the platform endpoint
       - Works with Google GCM, Apple APNS, Amazon ADM
- 📝**Fan out (SNS -> SQS)**
  - Push once in SNS, receive in many SQS
    - E.g. *Buying Service -> SNS Topic ->
      1. SQS Queue 1 (-> Fraud service)
      2. SQS Queue 2 (-> Shipping service)
  - Good because:
    - 💡 No data loss (in SNS you need to handle message right away)
    - Ability to add receivers later
    - SQS allows for delayed processing and retries of work
    - May have many workers on one queue and one worker on the other queue
    - In only SQS a message is not guaranteed to be delivered to all consumers
    - Ability to add receivers later
# AWS Kinesis

- Managed alternative to Apache Kafka
- Use-cases
  - "Real-time" big data e.g. application logs, metrics, IoT, clickstreams.
  - Streaming processing frameworks (Spark, NIFI, etc...)
- High availability through data replication to 3 AZ.
- Has 4 different products:
  - **Kinesis Streams** (data stream): Ingest & process streaming data
  - **Kinesis Firehouse** (delivery stream): load streams into S3, Redshift, ElasticSearch
  - **Kinesis Analytics**: perform real-time analytics on streams using SQL
  - **Kinesis video streams**: Process & analyze streaming media in real-time.
- 💡 Common integration: Feed *Kinesis Streams* -> Analyze data in real time using *Kinesis Analytics* -> Load streams into *Kinesis Firehose* -> S3/Redshift for long time retention.
- **Security**
  - Control access / authorization using IAM policies
  - Encryption in flight using HTTPS endpoints
  - Encryption at rest using KMS
  - Possibility to encrypt / decrypt data client side (harder)
  - VPC Endpoints available for Kinesis to access within VPC
  
## Comparison

| Attribute | Data Streams | Firehouse | Analytics | Video streams |
| -------- | -------- | --------- | --------- | ------------- |
| **Description** | Blob (max 1 MB) ingestion | Convert & load blob | Query engine | Video ingestion |
| **Throughput/Limits** | Per shard and message size/amount per read/write, max 1 MB blob | None: source data (max 1MB blob) | None: source stream | stream level (5TPS/H), connection-level (1/s), bandwidth MB/s, fragment-level |
| **Latency** | Real time | Near real time (60 secs latency) | Real time | Rendering + encryption (if stored) |
| **Data retention** | 1 day (default), up to 7 days | Retries during 24-hours then fails | Can create streams based on queries | Min 1 hour, max 10 years (in S3) |
| **Scaling** | Add shards | Auto-scales | Auto-scales | Auto-scales |
| **Concepts** | *Shards*, *Producers*, *Consumers* |  *Delivery stream*, *Record*, *Destination* | *Input*, *Code*, *Application* | *Video stream*, *Fragment*, *Producer*, *Consumer*, *Chunk* |
| **Use-cases** | Streams | Convert & load streaming data into e.g. Redshift, S3, ElasticSearch, Splunk | Real-time analytics using SQL | Machine learning & analytics |

## Kinesis Data Streams

- Low latency streaming ingest at scale like Kafka.
- Streams are divided in ordered Shards / Partitions
- Data goes to any shard & consumers consume from any shard
  - ***Producers*** -> **Stream** (***Shard*** 1 / ***Shard*** 2 / ***Shard*** 3) -> **Consumers**
  - Pub/sub through streams (topic)
  - Multiple applications can consume the same stream
  - You get sequence number (checkpoint offset, should be saved) back for each data added
- **To scale**: Add shards -> Increased throughput
- Pricing is per shard provisioned, can have as many shard as possible
- ***Data retention*** is 1 day by default, can go up to 7 days
- **Data**
  - The message is base64-encoded blob
  - ❗ Data (before encoding) cannot exceed 1 MB
  - Ability reprocess / replay data as data is not removed after message is handled.
  - Immutable: Once data is inserted in Kinesis, it cannot be deleted
  - Batching available or per message calls
    - 💡 Use batching with `PutRecords` API to reduce costs and increase throughput
- **Security**: You can enable server-side encryption with an AWS KMS master key
- **Monitoring**
  - You can capture ***shard level metrics*** with CloudWatch at additional cost
    - For e.g. *`IncomingBytes`*, *`IncomingRecords`*, *`OutgoingBytes`*, *`WriteProvisionedThroughputExceeded`*, *`ReadProvisionedThroughputExceeded`*.
- **Shards**
  - One stream is made of many different shards
  - The number of shards can evolve over time (re-shard / merge)
  - Records are ordered per shard
  - **Partition key**
    - Partition key gets hashed to determine the shard ID
    - Ensures ordering in a shard and same key always goes to same shard.
      - 💡 Choose a partition key that's highly distributed
        - 📝 Helps preventing **hot partition** or **hot shard**
  - Throughput
    - ❗ 1 MB/s or 1000 messages/s at write PER SHARD
    - ❗ 2 MB/s at read PER SHARD
    - `ProvisionedThroughputExceeded` if you go over the limits
    - 💡 Solution
      - Use retries with exponential back-off
      - Increase shards (scaling)
      - Ensure your partition key is a good one e.g. you don't have a hot shard
- **SDKs**
  - Normal consumer (CLI, SDK, etc...)
  - Kinesis Client Library (in Java, Node, Python, Ruby, .NET)
    - Uses DynamoDB to checkpoint offsets
    - KCL uses DynamoDB to track other workers and share the work amongst shards

## Kinesis Firehose

- Fully managed service, no administration, automatic scaling
- Near real time (60 seconds latency)
- Load data into Redshift / Amazon S3 / ElasticSearch / Splunk
- Support many data format (pay for conversion)
- Pay for the amount of data going through Firehose
- Concepts
  - **Delivery stream**: Create & send data into it
  - **Record**: Data of interest your data producer sends to a delivery stream
    - ❗ Max size before base-64 encoding is 1000KB
  - **Destination**: data storage, S3, Amazon Redshift, Amazon Elasticsearch Service, Splunk.

## Kinesis Data Analytics

- Perform real-time analytics on Kinesis Streams using SQL
- Fully managed with auto scaling to match source stream throughput.
- Pay for actual consumption rate
- Can create streams out of the real-time queries

## Kinesis Video Streams

- Ingest video streams for e.g. analytics and machine learning.
- ***HTTP Live Streaming (HLS)*** enables you to playback video for live and on-demand viewing
- Uses Amazon S3 as the underlying data store.
- Data at rest using KMS, IAM roles, and data in transit using TLS.
- Concepts
  - **Video stream**: AWS resource that encrypts & time-stamps & stores.
  - **Fragment**: self-contained sequence of frames
  - **Source**: e.g. video-generating device, such as a security camera, a body-worn camera
  - **Consumer**: consume and process data in Kinesis video streams e.g. EC2 / AWS AI services / 3rd party.
  - **Chunk**: Stored data, consists of the actual media fragment
# Amazon MQ

- Managed Apache ActiveMQ
- 💡 For lift & shift migrations:
  - Traditional applications running from on-premises may use open protocols such as MQTT, AMQP, STOMP
  - Instead of re-engineering the application to use SQS and SNS, we can use Amazon MQ
- It supports MQTT, AMQP, STOMP, Openwire, WSS, NMS, and WebSocket protocols
- Amazon MQ doesn't scale as much as SQS / SNS
  - Have to provision it
  - Broker's are deployed into e.g. `mq.m5-xlarge`, `mq.t2.micro` etc.
- Amazon MQ runs on a dedicated machine, can run in HA with failover
- Supports
  - Both queue feature (SQS) and topic features (SNS)
  - Both pull (SQS, Kinesis) and push based (SNS) messaging.
- Broker types
  - **Single-instance broker**: In single AZ
  - **Active/standby broker:** In two AZ's with automatic failover
- Network and security:
  - You deploy into VPC and subnet(s)
    - You need to allow access from security groups to reach the protocols
  - You can make it publicly accessible outside VPC
- You can set maintenance window and allow minor version updates.
# API Gateway

- AWS API Management platform
- Supports stateful (WebSocket) and stateless (HTTP) APIs.
- Allows you to build completely serverless applications
  - 💡Common architecture: Client *<-- REST API -->* API Gateway *<-- Proxy requests -->* AWS Lambda *<-- CRUD -->* Amazon DynamoDB
- **Some features**
  - Transform and validate requests and responses
  - API versioning (v1, v2...)
  - Different environments (dev, test, prod...)
  - **Monitoring**
    - Using AWS X-Ray to trace messages as they travel through the APIs to backend services.
    - CloudTrail logs.
  - Auto-documentation
    - Swagger / Open API import to quickly define APIs
    - Generate SDK and API specifications
  - Cache API responses
- **Metering** – define plans that meter and restrict third-party developer access to APIs.
  - Define a set of plans, configure throttling, and quota limits on a per API key basis.
  - Automatically meters traffic to your APIs and lets you extract utilization data for each API key.
- You create different **APIs** (containers)
  - Made of ***methods*** and ***resources***.
  - **API endpoint types**
    - **Regional**: Default, deployed to the specified region.
    - **Edge-optimized**: API requests are routed to the nearest CloudFront Point of Presence (POP)
    - **Private**: Exposed through interface VPC endpoints to allow clients to securely access private API resources inside a VPC.
  - **Methods**
    - Has HTTP verb (`ANY`, `DELETE`, `GET`, `HEAD`, `OPTIONS`, `PATCH`, `POST`, `PUT`)
    - Encapsulates frontend by method requests and method responses
      - During execution, you can modify data in each phase called modules:
        - Method request -> Integration Request -> Integration -> Integration Response -> Method response
      - In integration response you can set *Output Passthrough*
        - Passthrough: pass the client-supplied request payload through the integration request to the backend without transformation.
      - You can also use data transformations with  Velocity Template Language (VTL) templating scripts.
    - Allows ***Mock integrations*** without any back-end.
    - **Endpoint**
      - 💡 Public endpoints are always HTTPS!
      - Types:
        - **Regional**
          - For clients in the same region.
          - Reduces connection overhead
        - **Edge optimized**
          - For geographically distributed clients
          - API requests are routed to the nearest CloudFront Point of Presence (POP).
        - **Private**: Can only be accessed from your VPC using an interface VPC endpoint
    - **Integration type**: Lambda, HTTP, MOCK, AWS Service, VPC Link (for internal endpoints)
      - **Proxy** integrations (can be HTTP or lambda) sends & passes entire payload without modifications (passthrough)
        - **Lambda** allows **Lambda proxy integration**: Requests will be proxied with request details available in the `event` of the handler function.
    - Can use default timeout (29 seconds)
  - **Resources**
    - Resources groups **methods** or other nested resources.
    - Each resource has name (e.g. `Houses`), path (e.g. `/resources`) and option to enable CORS.
  - Are deployed to ***Stage***s (e.g. dev / test / prod)
    - You get an URL for stages
    - Stage options:
      - ***Settings***: Cache, Method throttling (with rate & burst), Client certificate
      - ***Logs/Tracing***: Enable CloudWatch logs / metrics, enable custom access logging, enable X-Ray tracing.
      - ***Stage variables*** that are key value pairs like environment variables
      - ***SDK Generation*** to Android, JavaScript, iOS, Java SDK, Ruby etc.
      - ***Export documentation*** as Swagger / OpenAPI 3 with optionally API Gateway or Postman extensions
      - ***Deployment history*** with ability to roll back
      - ***Documentation history*** with ability to roll back
      - ***Canary*** for testing with percentage of API traffic e.g. 95% of users will go to one API version, 5% go to another API version
- **Throttling**
  - **Unlimited scaling**: API Gateway can scale to any level of traffic received by an API
  - Can scale up to the default throttling limit of 10,000 requests per second, and can burst past that up to 5,000 RPS.
    - Throttling is used to protect back-end instances from traffic spikes
  - Throttling can be configured at multiple levels including Global and Service Call.

## Security

- Track and control usage using API keys
  - Can also Lambda authorizers or usage plans to control access to your APIs.
- CORS
  - CORS is used by browsers against cross side scripting (XSS) attacks.
    - E.g. a malicious script in website requests to unknown origins.
  - ❗📝 You must enable CORS for API gateway to send right headers to `OPTIONS` requests.
- **Integrations with other VPC services**
  - Outside of VPC
    - Any AWS Service (AWS Lambda, Endpoints on EC2, Load Balancers)
    - External and publicly accessible HTTP endpoints
  - ❗ Inside of VPC: only AWS Lambda and EC2 endpoints in your VPC.
  
### Authorization and Authentication of backend

- **IAM Roles / Users - AWS Signature Version 4 Signing Process (Sig v4)**
  - Allows you to- You sign all requests with signed using an access key (derivation of access key ID + secret access key) in header
- **IAM policies with Lambda Authorizer** (formerly Custom Authorizers)
  - Uses AWS Lambda to validate the token in header being passed
    - Lambda must return an IAM policy for the user to gateway
    - IAM policy decides whether to gateway can call the back-end
  - Option to cache result of authentication
  - 💡 Helps to use OAuth / SAML / 3rd party type of authentication
- **Cognito User Pools**
  - Cognito fully manages user lifecycle without custom implementation
  - API gateway verifies identity automatically from AWS Cognito
  - ❗ Cognito only helps with authentication, not authorization
  - You can enable MFA for your help users.
  - Flow
    1. Client authenticates & gets token from Cognito User Pools
    2. Client sends request to Gateway with a token
    3. Gateway validates the token
  - 💡 Good for Google & Facebook etc. authentication
- ***IAM vs Custom Authorizer vs Cognito User Pools***

  | Attribute | IAM | Custom authorizer | Cognito User Pool |
  | --------- | --- | ----------------- | ----------------- |
  | **Use case** | Great for users / roles already within your AWS account | Great for third party tokens | You manage your own user pool (can be backed by Facebook, Google, login etc..) |
  | **Authorization** | Handles authentication & authorization | Handles authentication & authorization | Handles only authentication, authorization must be implemented in the back-end |
  | **Implementation** | Leverages Sig v4 | Very flexible in terms of what IAM policy is returned | No need to write any custom code for authentication |
  | **Pricing** | Free (transfer charges) | Pay per Lambda invocation | Free (transfer charges) |
# Serverless workflows

## AWS SWF - Simple Workflow Service

- Coordinate work amongst applications
- Code runs on EC2 (not serverless)
- 1 year max runtime
- Requires you to write application code
- **One time only completion** is key feature that ensures that a task is assigned only once and is never duplicated.
- 💡❗ **Step functions is recommended** to be used for new applications, except if you need:
  - *external signals* to intervene in the processes
  - *child processes* that return values to parent processes
  - *long running* up to 1 year
- **Actors**
  - **Workflow starters**: Any application that initiates workflow
  - **Deciders**: control flow activity tasks
  - **Activity workers**: carry out the activity tasks
- **Steps**: Activity, Decision, Human intervention
- **Tasks**
  - **Activity task**: Tell ***activity worker*** to execute its task.
  - **Decision task**: It tells the decider the state of the workflow execution.
  - **Lambda task**: Executes lambda
- 💡 Use case e.g. order fulfilment from web to warehouse to delivery.
- 🤗 AWS uses SWF to run their warehouses.

## Step Functions

- Build serverless visual workflow to orchestrate your Lambda functions
  - E.g. Start => Submit Job => Wait X seconds => Get Job Status => Job Compete ? next : Go back to Wait X seconds -> End
- Represent flow as a JSON 📝state machine
- Features: sequence, parallel, conditions, timeouts, error handling ...
- The steps of your workflow can exist anywhere, including in AWS Lambda functions, on Amazon EC2, or on-premises.
- Integrated with & can coordinate many AWS services: *Amazon ECS*, *AWS Fargate*, *Amazon DynamoDB*, *Amazon SNS*, *Amazon SQS*, *AWS Batch*, *AWS Glue*, and *Amazon SageMaker*.
- ❗ Maximum execution time of 1 year
- Possibility to implement human approval feature
  - E.g. API gateway to send human approval accepted or not query.
- 💡 Use cases: any 📝workflow
- Monitoring
  - Each executed branch can be monitored and have different steps.
  - Each step has `Success`, `Failed`, `Cancelled`, `In Progress` states.
- Can use **Callback patterns**
  - Pauses execution of the workflow until your application returns a token.
  - Supports callback patterns with Amazon ECS, Amazon SNS, Amazon SQS, AWS Fargate, and AWS Lambda.
  - Can also integrate with on-premises
# Big Data & Data Analytics

## Amazon EMR - Elastic Map Reduce

- Helps creating Hadoop clusters (Big Data) to analyze and process vast amount of data.
- **Map Reduce**
  - Massive Parallel Processing technique
  - Steps
    1. **Map** with user defined mappers: Get data as key value
    2. *Sort* handled by framework
    3. **Reduce** with user defined reducers: Aggregates data
  - **Example**
    - Documents have words, **map** counts animal words, framework sort sorts, and **reduce** sums total occurrences of animal words.
    - Map => `cat:10, dog:3` & `cat:24, dog:5`
    - Reduce => `cat: 34 , dog: 8`
- The clusters can be made of hundreds of EC2 instances.
- EMR takes care of all the provisioning and configuration
- Also supports Apache Spark, HBase, Presto, Flink...
- Auto-scaling and integrated with Spot instances for lower price.
- Nodes
- **Master node**: One node that manages cluster e.g. track status & monitor health
  - 💡 You can SSH into master node and from there to task nodes.
- **Core nodes**: Data (HDFS), compute.
  - **Input splits**: Smaller data chunks of input data split by Hadoop
    - Controls control number of Mapper in Map/Reduce
  - **Blocks**: Physical splitting of data
- **Task nodes**: Optional, only compute
  - Usually runs on spot instances
  - Amazon handles if they get executed through running master operations in core nodes.
- How it works:
  1. Upload your data and processing application to S3
  2. Configure and create your cluster by specifying data inputs, outputs, cluster size, security settings, etc.
     - Launch modes:
       - Cluster: Long running
       - Step execution: Do analysis & shut-down cluster
     - Instance size & number of instances
     - Select EC2 key pair & IAM roles to EC2 instances
  3. Monitor the health and progress of your cluster. Retrieve the output in S3
- 💡 **Use cases**: data processing, machine learning, web indexing, big data

## Query Engines

- Comparison

  | Attribute | S3 / Glacier Select | Athena | Redshift Spectrum |
  | --------- | --------- | ------ | ----------------- |
  | Functionality | SQL `SELECT` on S3 objects | Query & extract data from S3 (ETL) | SQL queries on S3 using your Redshift cluster |
  | S3 Glacier Support | ✓ | ✖ | ✓ |
  | Read compressed files | | ✓ | ✓ |
  | Integrations | S3, Glacier | **AWS Glue**, QuickSight, VPC Flow logs, ELB logs, CloudFront & CloudTrail logs | S3, Redshift cluster |
  | Infrastructure | Serverless | Serverless | Serverless |
  | Pricing | Per query: Data Scanned + Data Returned + S3 *(data transfer + requests)* | Per query: Data Scanned + *(data transfer + requests)* | Data scanned + S3 transfer & request + Redshift cluster |
  | When to use | Simple `SELECT` | ETL, Logs, Complex queries (ANSI SQL Compliant e.g. *group by, having, window and geo functions*)  | When using Redshift cluster |
- **Athena vs Spectrum**
  - Athena for ad hoc data discovery and SQL querying
  - Redshift Spectrum for
    - More complex queries and scenarios
    - A large number of data lake users want to run concurrent BI and reporting workloads

## AWS Glue

- Fully managed 📝ETL (Extract, Transform & Load) service
  - Automates time consuming steps of data preparation for analytics.
  - E.g. sorting the data by client timestamp, compressing and storing in a read optimized format.
- Serverless, pay as you go, fully managed, provisions Apache Spark
- Crawls data sources and identifies data formats (schema inference)
- Discover, categorize, save in a catalog from connected sources
  - Sources: Amazon Aurora, RDS, Redshift & S3
- Automated Code Generation
  - It's a customizable Apache Spark code
- Sinks: S3, Redshift, etc..
- **Glue Data Catalog**
  - Metadata (definition & schema) of the Source Tables
  - Can be used by EMR
  - **Crawlers**: Programs that run through data to infer schemas and partitions from e.g. S3, Redshift, RDS.
- E.g. scenario =>
  - Kinesis (ingest & process) -> S3 (through Kinesis Data Firehose) -> Spark job on AWS glue (sort, optimize, crawl and catalog) -> Batch process catalog using Amazon MR (Elastic Map Reduce), query with Athena.

## AWS Data Pipeline

- Move data between different AWS compute and storage services, as well as on-premises data sources, at specified intervals.
- Output to Amazon S3, Amazon RDS, Amazon DynamoDB, and Amazon EMR.
- A pipeline consists of
  - Data nodes for source or destination storages (s3, mysql, dynamodb...)
  - E.g. import, copy, export.
- Differences from ETL
  - ETL pipelines are a subset of data pipelines.
  - ETL may modify data, data pipeline won't.
  - ETLs end in warehouses, built for warehousing.

## AWS Batch

- Managed HPC (high performance computing)
- Batch plans, schedules, and executes your batch computing workloads using Amazon EC2 and Spot Instances.
# Other services

- **Amazon Simple Email Service (SES)**: Bulk & secure  transactional email-sending service.
  - SNS vs SES

    | Attribute | SNS | SES |
    | --------- | --- | --- |
    | Target | For push notifications to AWS users | Third parties e.g. customers |
    | Flow | topic => e-mail opt-in confirmation => sends e-mails | Send rich e-mails through API calls. |
    | Custom headers, MIME types | Not supported | Supported |
- **IoT Core**: AWS service that helps you manage IoT devices & harvest data from them to e.g. Kinesis.
- **Amazon QuickSight**: BI visualization of data from/into RedShift, S3, Athena, Aurora, RDS, IAM.
- **AWS Amplify**: build serverless mobile applications quickly with e.g. authentication, lambda, analytics, offline data sync.
- **AWS X-Ray**: traces user requests as they travel through applications, helps to debug.
- **AWS Elastic Transcoder**
  - Convert media files (video + music) stored in S3 into various formats for tablets, PC, Smartphone, TV, etc.
  - Features: bit rate optimization, thumbnail, watermarks, captions, DRM, progressive download, encryption
  - Pay based on the minutes that you transcode an the resolution at which you transcode
  - 4 Components:
    - **Jobs**: what does the work of the transcoder
    - **Pipeline**: Queue that manages the transcoding job
    - **Presets**: Template for converting media from one format to another
      - E.g. size, quality, bitrate
    - **Notifications**: E.g. SNS when transcoding is done.
- **AWS WorkSpaces**
  - Managed, Secure Cloud Desktop
  - Eliminates management of on-premise VDI (Virtual Desktop Infrastructure)
  - On Demand, pay per by usage
  - Secure, Encrypted, Network Isolation
  - Integrated with Microsoft Active Directory
- **AppSync**
  - Store and sync data across mobile and web apps in real time
  - Makes use of GraphQL (mobile technology from Facebook)
  - Differences from **Cognito Sync**
    - 📝 Cognito Sync cannot do Graph QL
    - Offline data synchronization
  - Client code can be generated automatically
  - Integrations with DynamoDB / Lambda
  - Real-time subscriptions
- **AWS Certificate Manager**: Creating and managing public SSL/TLS certificates for your AWS based websites and applications
  - 📝 Allows wildcard certificate
    - Public key certificate which can be used with multiple sub-domains of a domain.
    - Protect multiple sub domain names e.g. `*.domainname.com`
    - 💡 For multiple root domain names you need `SNI` through CloudFront or ELB.
- **AWS Resource Groups**
  - Can be based on tags or CloudFormation stacks
  - Good for
    - Bulk options e.g. updates/security patches, upgrading applications, opening/closing ports
    - IAM permissions can be assigned to RG.
- **AWS Backup**: Centralize manage backups for AWS and on premises (using Storage Gateway) services.
- **AI**
  - **Amazon Lex**: Build Conversation Bots
  - **Amazon Polly**: Text to speech.
- **Amazon AppStream** is a fully managed application streaming service
- **AWS Device Farm** is an app testing service that lets you test and interact with your Android, iOS, and web apps on many devices at once, or reproduce issues on a device in real time

## Security services

- **AWS Shield**
  - Managed Distributed Denial of Service (DDoS) protection service
  - Seamless integration with Elastic IP, ELB, CloudFront, Global Accelerator and Route 53.
  - AWS WAF is included with AWS Shield Advanced at no extra cost
- **AWS WAF - Web Application Firewall**
  - Protects against common exploits like SQL injection and Cross-Site Scripting (XSS).
  - Block & allows & rate-limits traffic based on rules
    - Massively scaled: Your rules run in all AWS Edge Locations
    - Match rules are • Cross-site scripting, • IP match, • Geo match, • size constraint match • SQL injection match • string match, • regex match
  - **AWS Firewall Manager** applies WAF rules on across accounts.
  - Integrates with ALB, API Gateway and CloudFront but accepts also custom origin.
- **Amazon Macie**
  - Uses machine learning to automatically discover, classify, and protect sensitive data in AWS.
- **Amazon GuardDuty** is an intelligent threat detection service to protect your AWS accounts and workloads
- **AWS Inspector**
  - Security assessments service
  - Uses agent to check for unintended network accessibility of your EC2 instances
# Well Architecture Framework

## 5 pillars of Well Architected Framework

- Not something to balance, or trade-offs, they're a synergy
  - Help you track your performance and evolve your architecture.
- 5 pillars summary:
  1. **Operational Excellence**
     - **Description**: Run and monitor systems & continually improve supporting processes and procedures
     - **Design principles**
       - **Perform operations as code**: Infrastructure as Code
       - **Annotate documentation**: e.g. auto-document after every build)*
       - **Make frequent, small, reversible changes**
       - **Refine operations procedures frequently**: Ensure team members are familiar with it.
       - **Anticipate failure**: Learn from all operational failures
     - **Example AWS Services**
       - **Selection**: Auto Scaling, Lambda, EBS, S3, RDS
       - **Review**: AWS CloudFormation, [AWS News Blog](https://aws.amazon.com/blogs/aws/)
       - **Monitoring**: CloudWatch, Lambda
       - **Tradeoffs**: Amazon RDS (vs Aurora), ElastiCache (read performance but stale), Snowball (a lot of data but takes a week), CloudFront (cache but stale)
  2. **Security**
     - **Description**: Protect information, systems, and assets through risk assessments and mitigation strategies
     - **Design principles**
       - **Implement a strong identity foundation**: Least privilege, IAM, centralize privilege management, eliminate reliance on long-term credentials
       - **Enable traceability**: Automatically respond to logs and metrics.
       - **Apply security at all layers**: Every instance, OS, and app, e.g. VPC, subnet, LB.
       - **Automate security best practices**: Encryption, tokenization, and access control.
       - **Protect data in transit and at rest**
       - **Keep people away from data**: Reduce the need for direct access or manual data processing
       - **Prepare for security**: Run incident response simulations and use tools with automation to increase your speed for detection, investigation, and recovery
     - **Example AWS Services**
     - **Identity and access management**: IAM, AWS-STS, MFA token, AWS Organizations
     - **Detective controls**: AWS Config, AWS CloudTrail, Amazon CLoudWatch
     - **Infrastructure Protection**
       - **Amazon CloudFront**: Defense against DDoS attack
       - Amazon VPC
       - **AWS Shield**: DDoS protection of AWS account
       - **AWS WAF**: Web Application Firewall
       - **Amazon Inspector**: for security of EC2 instance
     - **Data protection**: KMS, S3 (SSE, SSE-KMS, SSE-C, bucket policies etc.)
       - And other managed services such as Elastic Load Balancing (e.g. only HTTPS), Amazon EBS & RDS (SSL Capability)
     - **Incident Response**
       - **IAM**: Delete account or give it zero privilege
       - **CloudFormation**: If someone deletes entire structure, how to get back?
       - Amazon CloudWatch Events
  3. **Reliability**
     - **Description**:
       - Ability of a system to recover from infrastructure or service disruptions
       - Dynamically acquire computing resources to meet demand
       - Mitigate disruptions such as misconfigurations or transient network issues.
     - **Design principles**
       - **Test recovery producers**: Use automation to simulate different failures or to recreate scenarios that led to failures before
       - **Automatically recover from failure**: Anticipate and remediate failures before they occur.
       - **Scale horizontally to increase aggregate system availability**: Distribute requests across multiple, smaller resources to ensure that they don't share a common point of failure
       - **Stop guessing capacity**: Maintain the optimal level to satisfy demand without over or under provisioning, use auto-scaling.
       - **Manage change in automation**: Use automation to make changes to infrastructure
     - **Example AWS Services**
       - **Foundations**
         - **IAM**: No one has too many rights to damage
         - Amazon VPC
         - **Service limits**: monitor limits so you don't get disruption.
         - **AWS Trusted Advisor**: e.g. look at service limits
       - **Change Management**: AWS Auto Scaling, Amazon CloudWatch, AWS CloudTrail, AWS Config
       - **Failure Management**: Backups, AWS CloudFormation (recreate whole infrastructure at once), Amazon S3, Amazon S3 Glacier, Amazon Route 53 (e.g. if application fails you redirect to another application)
  4. **Performance**
     - **Description**: Adopt & provide best performance
     - **Design principles**
       - Democratize advanced technologies *(track new services)*
       - Go global in minutes *(easy multi-region deployment)*
       - Use serverless architectures *(avoid burden of managing servers)*
       - Experiment more often *(easy to carry out comparative testing)*
       - Mechanical sympathy *(be aware of all AWS services)*
     - **Example AWS Services**
       - **Prepare**: AWS CloudFormation, AWS Config
       - **Operate**: AWS CloudFormation, AWS Config, AWS CloudTrail, Amazon CloudWatch, AWS X-Ray
       - **Evolve**: AWS CloudFormation, CI/CD: CodeBuild, CodeCommit, CodeDeploy, CodePipeline
  5. **Cost Optimization**
     - **Description**: Can run systems to deliver business value at the lowest price point.
     - **Design Principles**
       - **Adopt a consumption mode**: Pay only for what you use
       - **Measure overall efficiency**: Use CloudWatch
       - **Analyze and attribute expenditure**: Use tags, Accurate identification of system usage and costs -> helps measure return on investment (ROI)
       - **Use managed and application level services to reduce cost of ownership**: As managed services operate at cloud scale, they can offer a lower cost per transaction or service
     - **Example AWS Services**
       - **Expenditure awareness**: AWS Budgets, AWS Cost and Usage Report, AWS Cost Explorer, Reserved Instance Reporting
       - **Cost-effective resources**: Spot instance, Reserved instance, Amazon S3 Glacier
       - **Matching supply and demand**: AWS Auto Scaling, AWS Lambda
       - **Optimizing over time**: AWS Trusted Advisor, AWS Cost and Usage Report, [AWS News Blog](https://aws.amazon.com/blogs/aws/)

## Well Architected Tool

- [Tool](https://console.aws.amazon.com/wellarchitected)
- Flow:
  1. Define a workload
  2. Do a review
     - Answer questions for each pillar.
     - E.g. for operational excellence
       - Question: How do you determine what your priorities are?
       - Answers: customer needs / compliance requirements / ... / none
  3. Optionally generate reports, milestones, improvement plans (with risks, e.g. "understand business needs").
# Exam readiness

## Tips

- Most questions are scenario based
  - First rule out answers you know for sure are wrong
  - Remaining answers: Understand which one makes the most sense.
- No penalty for guessing
- Don't over-think it
  - If solution seems feasible but highly complicated, it's probably wrong.
- You can come back to questions with flag feature
- For learning services more, read each service's FAQ
  - Cover a lot of the questions asked in the exam
- If AWS provides a service/functionality within service => Use it instead of creating a bespoke service.
- 65 questions will be asked in 130 minutes
  - 2 minutes per question

## Axioms

- Reliable/resilient storage = EBS, EFS, S3
  - For EBS
    - Small, random = SSD
    - Large, sequential = HDD
- **Durable** = S3
- **Low latency access** = EBS
- **Reproducible** = Spot instance, S3 one zone IA
- Decoupling = SQS, SNS
  - Only once = SQS FIFO or SWF
- Multi-tier architecture: ELB, ASG, EC2
- High availability/fault tolerant =
  - Horizontal scaling, vertical scaling
  - ASG that span over many AZ (sent to ELB)
  - DynamoDB (HA by default)
  - RDS (multi-AZ needs to be enabled)
    - Availability = Sync replication, Scalability = async
- Performant storage and databases =
  - 💡GP2, IO1 for EBS
  - 💡EFS, S3
- Caching = ElastiCache, CloudFront, API Gateway, DAX for DynamoDB
- Multi-region=Stack Set
- Elasticity and scalability = ASG, Lambda, CloudWatch for alarms
- CloudWatch
  - **Enhanced** monitoring: Metrics within hyper-visor from RDS
  - **Detailed** monitoring: Send metrics in 1m interval instead of 5.
- Secure application tiers = Security groups
- Secure data = KMS, Encryption
- Cost optimized storage = S3, Pay for what you use, life-cycle rules to move data to cheaper, glacier for long time.
- Cost optimized compute = Lambda, pay for what you use, reserve long time instances, Spot Instances for short fault tolerant jobs
- Licensing problems = Microsoft + Oracle e.g. BYOB=Dedicated host
- Operational excellence = Serverless

## More links

- Exam:
  - [Exam page](https://aws.amazon.com/certification/certified-solutions-architect-associate/)
    - [Exam guide](https://d1.awsstatic.com/training-and-certification/docs-sa-assoc/AWS_Certified_Solutions_Architect_Associate-Exam_Guide_EN_1.8.pdf)
  - [30 minute extension for your AWS Certification exam](https://www.linkedin.com/pulse/30-minute-extension-your-aws-certification-exam-garcia-lozano)
- Read:
  - [Architecting for the Cloud - AWS Best Practices](https://d1.awsstatic.com/whitepapers/AWS_Cloud_Best_Practices.pdf)
  - [AWS Well Architected Framework](https://aws.amazon.com/architecture/well-architected/)
    - [Operational Excellence](https://d1.awsstatic.com/whitepapers/architecture/AWS-Operational-Excellence-Pillar.pdf)
    - [Security](https://d1.awsstatic.com/whitepapers/architecture/AWS-Security-Pillar.pdf)
    - [Reliability](https://d1.awsstatic.com/whitepapers/architecture/AWS-Reliability-Pillar.pdf)
    - [Performance Efficiency](https://d1.awsstatic.com/whitepapers/architecture/AWS-Performance-Efficiency-Pillar.pdf)
    - [Cost Optimization](https://d1.awsstatic.com/whitepapers/architecture/AWS-Cost-Optimization-Pillar.pdf)
  - [AWS Disaster Recovery](https://d1.awsstatic.com/asset-repository/products/CloudEndure/CloudEndure_Affordable_Enterprise-Grade_Disaster_Recovery_Using_AWS.pdf)
- Reference architectures resources for real-world
  - [AWS reference architectures](https://aws.amazon.com/architecture)
  - [AWS Solutions](https://aws.amazon.com/solutions)
  - [This is my architecture](https://aws.amazon.com/this-is-my-architecture/?tma.sort-by=item.additionalFields.airDate&tma.sort-order=desc)
  - [AWS Startups Blog](https://aws.amazon.com/blogs/startups/)
  - [Projects on AWS](https://aws.amazon.com/getting-started/projects/)
# IAM & Organization

## Account

- Registered with an e-mail address.
  - ❗ Not required for non-root accounts in AWS Organizations
  - *Owner* usually means the e-mail owner of the registered account.
- **Root Account**
  - User with e-mail address that created the account.
  - 💡 Create new IAM user with administrator privileges and lock away root account credentials. NEVER USE ROOT ACCOUNT FOR EVERY DAY AWS ACTIVITY. It allows unlimited access on your resources, hence, it is safe to proceed with IAM user. Also enable MFA on root account.

## IAM

- Centralized control of your AWS account.
  - Each API call to AWS is authorized using IAM.
- Shared Access to your AWS account
- Global. Does not apply to regions.
  - So you will use the same IAM configuration no matter if you use one of all regions.
- Identity Federation
  - Federating Users of Mobile/Web app using **Cognito**
    - Using Amazon Cognito will create identities (with the help of Mobile SDK) for users and sync across devices.
  - Federating Users with **OpenID Connect (OIDC)** or public identity service provider
    - Using third-party services like Facebook, Google or any identity providers (IdP) compatible with OIDC can federate users.
      - 💡 Amazon recommends against it, use Cognito or Web Identity Federation only for advanced scenarios.
  - Federating users with **SAML 2.0**
    - If companies managing users with identity store like Microsoft Active Directory and Active Directory Federation Service can federate users with single-sign on (SSO) to aws management console. Company can create trust between organization as an identity provider (IdP) and AWS as the service provider if it is compatible with SAML 2.0
  - Federating users by creating a **custom identity brokers**
    - If identity store is not compatible with SAML 2.0 then custom identity broker application can be built.
- Multifactor Authentication
  - 💡 If your MFA device is lost/damaged/notworking:
    - You can sign in using alternative methods of authentication.
      - E.g. sign in by verifying your identity using the email and phone that are registered with your account.
- Provide temporary access for users/devices and services where necessary
- Supports password policies:
  - Enforce strong password
  - Password rotation policy, e.g. expire after 90 days.
- Credential report: See which users are using what permissions.
- Integrates with many different AWS services
- Supports PCI DSS Compliance
- **IAM Certificate store**: Legacy way to upload certificates, use Certification Manager instead.
- **IAM Query API** to make direct calls to the IAM web service.

### IAM Entities

- ![IAM entities](./img/iam/iam-entities.png)
- **Principal** is an entity in AWS that can perform actions and access resources.
  - **IAM Users**
    - **Programmatic Access: ID + secret key**
      - Mostly for machines.
      - 💡 Best practice for services is to assume roles
      - Assigned an Access Key ID and Secret Access Key when first created.
      - Secrets are only shown once after created, then needs to be regenerated to reveal/download again.
      - ❗ IDs are permanent you need to manually rotate (make active/disable, delete or create new)
    - **Console Access: Password**
      - 💡 Ensure no one share same users by creating different users per person.
      - By default, newly created IAM users has no permissions.
  - **IAM Roles**
    - 💡 Use cases:
      - Create roles and can then assign them to AWS resources
      - Give cross-account access to users.
      - Instead of being uniquely associated with one person, a role is intended to be assumable by anyone who needs it.
        - 💡 However for unique access of one you can delegate a role through:
          - In account add *trust policy* that specifies which trusted account members are allowed to assume the role.
          - The *trust policy* specifies which trusted account members (*principal*s) are allowed to assume the role.
    - Credentials are rotated automatically -> Only temporary access.
      - Uses STS with expirations to generate secret id + secret key.
    - You can only assume one role a time
- **IAM Groups**
  - A collection of users under one set of permissions (policies)
  - 💡 Use groups to assign permissions instead of individual users, because users in group inherit the permissions from groups.
  - A group is not an identity and cannot be identified as a principal in an IAM policy,
- **IAM Policies**
  - JSON document that defines one (or more) permissions
  - **Resource-based**: To supported AWS resources, who can access them.
  - **Identity-based**: Assigned to roles, users, groups, who they can access.
  - Overlap => Deny overrides allow.
  - To define resources ARN's are used (Amazon Resource Names) to uniquely identify AWS resources
  - 💡 Principle of Least Privilege: Always adhere to it when creating IAM user, restrict access with only resources/actions to do the job.

#### IAM Authentication to Services

- Most of AWS services authenticates a user / role.
- User / role (IAM) credentials must be sent in header
  - It's called Sig v4 **AWS Signature Version 4 Signing Process**
  - You sign all requests with signed using an access key (derivation of access key ID + secret access key) in header
- All requests to AWS API's are signed automatically by SDK/CLI.

## AWS Organizations
  
- Consolidate multiple AWS accounts into an organization that you create and centrally manage
  - Enforce Service Control Policy (SCP) centrally on root or OU's.
    - Also SCP has precedence over individual's permission.
    - Best suitable for restricting access across multiple accounts in an organization.
- Grouping all of your AWS accounts into **Organizational Units** (OUs) as part of a hierarchy
  - Hierarchical based control over groups of IAM users and roles, within multiple Accounts.
- Allows **Consolidated billing**: Multiple standalone accounts are combined and may reduce your overall bill
  - They can still be tracked individually and the cost data can be downloaded in a separate file.
- Allows you to create accounts programmatically.
# Identity federation & SSO

- Federation lets users outside of AWS to assume temporary role (using STS) for accessing AWS resources without having to create a user in AWS.
- Federation assumes a form of 3rd party authentication e.g. LDAP, Microsoft Active Directory (=~ SAML), SSO, Open ID, Cognito
  - Single Sign On
  - Open ID
  - Cognito

## AWS STS - Security Token Service

- Allows to grant limited and temporary access (permissions) to AWS resources
- Token is valid for between 15 minutes to one hour (must be refreshed)
- Used mostly for:
  - Generates tokens when assuming roles.
  - ***Cross Account Access*** that allows users from one AWS account access resources in another
    - ***Flow***
      1. Define an IAM role for another account to access
      2. Define which accounts can access this IAM role
      3. Use AWS STS (Security Token Service) to retrieve credentials and impersonate the IAM role you have access to (`AssumeRole` API)
  - ***Federation (Active Directory)***
    - Provides a non-AWS user with temporary AWS access by linking users Active Directory credentials
    - Uses SAML (Security Assertion markup language)
    - Allows Single Sign On (SSO) which enables users to log in to AWS console without assigning IAM credentials
  - ***Federation with third party providers (Cognito & Web Identity Federation)***
    - Cognito: Used mainly in web and mobile applications
    - Web Identity Federation: Makes use of Facebook/Google/Amazon etc. to federate them

## Federation

- STS is common service to create credentials after identity validation
  - Identity validation can be  done through SAML, Cognito or Custom Identity Broker application.

### SAML based federation

- To integrate Active Directory / ADFS with AWS (or any SAML 2.0)
- An **IdP** authenticates on on-prem store and then exchanges **SAML assertion** that has roles with STS to get a redirect URL with token.
- ![SAML based federation](img/iam/federation/saml-based-federation.diagram.png)

### Custom Identity Broker Application

- 💡 Use only if identity provider is not compatible with SAML 2.0
- You have to program ***Identity Broker*** that must determine the appropriate IAM policy.
- ![Identity broker based federation](img/iam/federation/enterprise-authentication-with-identity-broker-application.diagram.png)

## AWS Cognito

- Managed identity broker for web identity federation
- **User pools**
  - Handles registration, authentication, account recovery.
  - Synchronizes user data for your users, username etc.
    - Uses Push Synchronization to & SNS to push updates to sync data across multiple devices.
  - Includes JWT tokens from third party authentication providers.
- **Identity Pools**
  - User directory for clients.
  - Allows users to obtain temporary AWS credentials to access AWS resources.
  - Flow
    - User logs into federated identity provider & gets *third party authentication token*.
    - Send *third party authentication* token to Cognito
      - Cognito returns Cognito ID with temporary AWS credentials
  - E.g. Provide (temporary) access to write to S3 bucket using Facebook Login
  - 💡 Good for authenticating B2B & B2C users for web + mobile applications.
- **Web Identity Federation**
  - An alternative to Cognito
  - ❗ AWS recommends Web Identity Federation only for advanced scenarios.
  - 💡 Mostly try to use Cognito, because it does most of the behind-the-scenes work with public identity provider services for you.

## AWS Single Sign On (SSO)

- Centrally Managed Single Sign On across multiple AWS Accounts and Business Applications (Office 365, SalesForce, Box)
- One login gets you access to management console securely
- Integrated with Microsoft Active Directory
- Only helpful for Web Browser, SAML 2.0 enabled applications.
  - E.g. IdP (Identity Provider) initiated single sign on

## Cognito vs SSO

- Amazon Cognito
  - Customer-targeted IAM and user directory solution.
  - For customers/developers building B2C or B2B apps for their customers
- Amazon SSO
  - Focused on SSO for employees accessing AWS and business apps

## AWS Directory Service

- **AWS managed Active Directory**
- 💡 Use cases
  - Can use it as an actual Microsoft Active Directory
  - Easily migrate directory-aware, on-premises workloads
  - Easily extend existing domains
  - 💡 Choose to integrate with on-prem ID if you don't want to setup federation / SAML infrastructure for SSO
- Integrations
  - SSO with corporate credentials to e.g. WorkSpaces, WorkDocs, or WorkMail.
  - AD-aware EC2 instances, RDS SQL etc.
- **AD Connector**
  - Directory gateway to redirect directory requests to on-premises AD without caching any information in the cloud.
  - Eliminates the need for directory synchronization and the cost and complexity of hosting a federation infrastructure.
  - Enforce on-prem policies in cloud.
  - Use existing MFA infrastructure for MFA.
- **Simple Active Directory**
  - Standalone managed directory.
  - Subset of features from Microsoft AD based Directory Service.
  - Easier to manage Linux
# Encryption in AWS

- ![Encryption in AWS](img/encryption/encryption-in-aws.png)

## Encryption key

- Two solutions currently exist for managing encryption keys:
  - **Hardware security modules (HSM)**
    - HSM = Hardware security module
    - Designed and certified to be tamper-evident and intrusion-resistant, provide the highest level of physical security, no hardware sharing.
    - **AWS CloudHSM**
      - Cloud-based hardware security module (HSM)
      - Single-tenant access to HSM, hardware maybe shared.
      - Enables you to easily generate and use your own encryption keys on the AWS Cloud.
      - Primarily intended to support customer-managed applications that are specifically designed to use HSMs
      - CloudHSM cluster contains at least two HSMs => min 1.000$ per month.
      - Can have automatic back-ups
      - Lose the keys => no way to recover
  - **Key management services (KMS)**
    - Also known as a cryptographic key management service (CKMS)
    - Enables clients to manage encryption keys without concerns about HSM appliance selection or provisioning
    - Usually come with more scalability, availability, native integration with other services such as databases, Bring Your Own Key (BYOK) that allows you use external HSM for master keys.
    - **AWS KMS** is AWS offering for a key service system.

### AWS KMS - Key Management Service

- An integrated & managed approach for generating, distributing and managing cryptographic keys for devices and applications
  - Managing a key includes maintaining their *key policies*, *IAM policies*, *enabling/disabling them*, *rotating*. *adding tags*. *creating aliases*, *scheduling for deletion*.
    - **Rotation policies** e.g. key must be changed ever year
- **Monitoring**: Audit key usage using CloudTrail
- Integrates with e.g. EBS (volumes), S3 (SSE), Redshift, RDS and SSM.
  - IAM for authorization: 💡 Can add external accounts that can use the key.
- 💡 Managed KMS key per service is created for AWS services e.g. one for CodeCommit, one for RDS, one for S3, one for Lambda...
- Has API's in CLI /SDK
- **Customer Master Keys (CMKs)**

  | Type | Can view | Can manage | Account-specific | Price |
  | ---- | ---- | ------ | ------------------- | -- |
  | Customer managed CMK | ✔️ | ✔️ | ✔️ |  Monthly fee key + usage |
  | AWS managed CMK | ✔️ | ❌ | ️✔️ | No monthly fee, just usage |
  | AWS owned CMK | ❌ | ❌ | ❌ | Free |
  - ❗ The value in KMS is that the *CMK (Customer Master Key)* used to encrypt data can never be retrieved by the user.
  - The CMK can be rotated for extra security.
- Encrypted secrets can be stored in the code / environment variables
  - E.g. **encryption helpers** in lambda environment variables.
  - No one can decrypt without having access through IAM
- KMS can only help in encrypting up to 4KB of data per call
- ❗ KMS can only encrypt up to 4KB of data per call
  - 📝 If data > 4 KB, use envelope encryption
  - Envelope encryption = Encrypt your key (data key) using master key.
- To give access to KMS to someone
  - Make sure the Key Policy allows the user
  - Make sure the IAM Policy allows the API calls
- ***Flow***
  - ***Encrypt API***: *KMS* checks IAM permissions & performs encryption & sends encrypted secret
  - ***Decrypt API***: *KMS* check IAM permissions & performs decryption & sends decrypted secrets (in plain-text)

### AWS Systems Manager - Systems Manager Parameter Store

- Secure storage for configuration and secrets
- (*Optional*) Seamless Encryption using KMS
  - Requires KMS policy to be activated
- Serverless, scalable, durable, easy SDK, free
- Version tracking of configurations / secrets
- Configuration management using path & IAM
  - 💡 Best practice to give IAM role for reading only the needed parameter
  - For plain text configurations it's enough to give SSM read permission
    - For decrypted parameters, you also need to give IAM permission to KMS.
- Notification with CloudWatch Events
- Integration with CloudFormation
- ***Flow***:
  - *Application* sends encrypted configuration to *SSM Parameter Store*
  - *SSM Parameter Store* checks IAM permissions
  - *SSM Parameter Store* decrypts configuration using AWS KMS
  - *Application* receives plain-text configuration from *SSM Parameter Store*.
- Supports hierarchy, e.g. `my-department/my-app/dev/db-url`.
- Configurations are retrieved using `GetParameters` or `GetParametersByPath` API.
- Two ways of accessing from portal:
  1. *EC2 Systems Manager - Parameter Store*
  2. from *AWS Systems Manager* -> Parameter Store.
- Two tier of parameters:

  | Feature | Standard | Advanced |
  | ------- | -------- | -------- |
  | Max parameter | 10.000 | No limit |
  | Max value size | 4 KB | 8 KB |
  | Parameter policies | None | Available |
  | Pricing | Free | Charges apply |
  - Parameter must have:
    - Name (e.g. `/my-app/dev/db-url`)
    - Type: `String`, `StringList` (comma separated), `SecureString`

## Encryption types

- **Encryption in flight (SSL)**
  - Data is encrypted before sending and decrypted after receiving
  - SSL certificates help with encryption (HTTPS = SSL enabled)
  - Encryption in flight ensures no MITM (man in the middle attack) can happen
    - I.e. No one inspecting the traffic can see data
  - ***Flow***
    - You send data SSL encrypted with key and over HTTPS
    - Server (HTTPS website) decrypts data with SSL key.
- **Server side encryption at rest**
  - Encryption/decryption is handled by server-side
    - Data is encrypted after being received by the server
    - Data is decrypted before being sent
  - Used by many AWS services
  - It's stored in a encrypted form thanks to a key (usually a ***data key***)
  - The encryption / decryption keys must be managed somewhere and the server must have access to it.
    - Usually stored in KMS (Key Management Service)
  - ***Flow***
    - Object is being sent using HTTP(s)
      - Server (e.g. EBS AWS service) encrypts object with data key & saves it
    - Object is requested using HTTP(s)
      - Server decrypts object with data key & sends it
  - ❗ Requires migration (through Snapshot Backup) to enable/disable in EBS Volumes, RDS databases, ElastiCache, EFS
  - In S3, it's in-place e.g. you encrypt & decrypt with parameters.
- **Client side encryption**
  - Data is encrypted by the client and never decrypted by the server
  - Data will be decrypted by a receiving client
  - The server should never not be able to decrypt the data
  - 📝Could leverage ***Envelope Encryption***
    - You encrypt encryption key with **Envelope Key** in KMS.
    - You manage encryption key, AWS manages Envelope Key.
  - ***Flow***:
    - Client encrypts object with *Client side data key* & sends
    - Object is stored in encrypted form in AWS
    - Client retrieves encrypted object and decrypts with *Client side data key*
# Monitoring

- CloudWatch is for monitoring/performance.
- CloudTrail is for auditing API call stacks e.g. when/where/by whom.

## CloudWatch

- Can monitor Compute (EC2, ASG, ELB, Route53 health checks..), Storage & Content Delivery (EBS, Storage)...
- **Metrics**
  - Provides metrics (e.g. CPU utilization, Network Utilization, Disk Reads/Writes, Status Check) for every services in AWS.
  - Metrics belong to ***namespaces***
  - ***Dimension*** is an attribute of an metric (instance id, environment, etc..)
  - Up to 10 dimensions per metric
  - ❗ 14 days retention, **Extended retention** offering allows up to 15 months.
  - Metrics have ***timestamps***
  - ***EC2 Detailed Monitoring***
    - 📝EC2 instance metrics have basic metrics for "every 5 minutes"
      - With detailed monitoring (for a cost), you get data "every 1 minute"
    - 💡 Use detailed monitoring if you want to more prompt scale your ASG
    - AWS Free Tier allows up to 10 detailed monitoring metrics
    - ❗ EC2 memory usage is by default not pushed
      - 💡 Must be pushed from inside the instance as a custom metric with e.g. CloudWatch agent.
  - ***Custom Metrics***
    - Possibility to define and send your own custom metrics to CloudWatch
      - E.g. for RAM utilization, disk storage usage.
    - Must be pushed from inside the instance as a custom metric by installing agent
      - E.g. CloudWatch agent
    - Ability to use dimensions (attributes) to segment metrics
      - E.g. `Instance.id`, `Environment.name`
    - Metric resolution
      - Standard: 1 minute
      - High resolution: up to 1 second (`StorageResolution` API parameter)
        - Higher cost
    - API call `PutMetricData`
    - 💡 Use exponential back off in case of throttle errors
  - ❗ CloudWatch itself does not have a native export feature that will send data periodically to S3.
- **Dashboards**
  - Can create CloudWatch dashboard of metrics
  - In console you can monitor & access dashboards
  - Can be ***regional*** or ***global*** (e.g. include graphs from different regions)
  - You can change the time zone & time range of the dashboards
  - You can setup automatic refresh (10s, 1m, 2m, 5m, 15m)
  - Pricing
    - 3 dashboards (up to 50 metrics) for free
    - 3$ per dashboard per month afterwards
  - Types are:
    - *Line*: compare metrics over time
    - *Stacked area*: Compare the total over time
    - *Number*: instantly see the latest value for a metric
    - *Text*: Free text with markdown formatting
    - *Query results*: Explore results from Logs Insights
- **Logs**
  - Applications can send logs to CloudWatch using the SDK
  - CloudWatch Logs **metric filters** can evaluate CloudTrail logs for specific terms, phrases or values.
    - I.e. values are not always from CloudWatch Metrics, but can be generated from Logs e.g. HTTP errors.
  - CloudWatch can collect log from *Elastic Beanstalk*, *ECS*, *AWS Lambda*, *VPC Flow Logs*, *API Gateway*, *CloudTrail*, *CloudWatch log agents*, *Route53* and more.
    - CloudWatch Log Agents
      - Install on EC2 machines `sudo yum install -y awslogs`
        - Ensure EC2 has IAM permissions to write to CloudWatch
      - Configure `/etc/awslogs/awslogs.conf` for logs (errors etc.)
      - Configure `/etc/awslogs/awscli.conf` for region
      - Start service with `systemctl start awslogsd`
  - CloudWatch logs can go to:
    - Batch exporter to S3 for archival
    - Stream to ElasticSearch cluster for further analytics
    - Stream to Lambda
  - You need to store logs in 2 things:
    - ***Log groups***: arbitrary name, usually representing an application
    - ***Log stream***: instances within application / log files / containers
  - Can define ***log expiration policies*** (never expire, 30 days, etc..)
    - You pay for data retention in CloudWatch
  - Using the AWS CLI we can tail CloudWatch logs
    - To see e.g. how application is behaving in real time
  - **Security**
    - ❗ To send logs to CloudWatch, make sure IAM permissions are correct!
    - Encryption of logs using KMS at the Group Level
  - CloudWatch Logs can use ***filter expressions***
    - E.g. find a specific IP inside of a log
    - ***Metric filter***s can be used to trigger alarms
      - E.g. if specific IP appears you can trigger an alarm
  - ***CloudWatch Logs Insights***
    - Log analytics service for CloudWatch
    - Can be used to query logs and add queries into CloudWatch Dashboards
    - Pay for the queries you run
- **Alarms**
  - Alarms are used to trigger notifications for any metrics
  - You can set up ***billing alarms*** to be triggered after the account charges goes over a certain threshold.
  - Alarms invokes **action**s such as:
    - **EC2 Actions**: e.g. restart EC2.
    - **SNS Notifications**: email, SMS, etc.
    - **Auto Scaling**: triggers Auto Scaling policies.
  - Various options (sampling, %, max, min, etc...)
  - Alarm states: `OK`, `INSUFFICIENT_DATA`, `ALARM` (being triggered)
  - Period:
    - Length of time in seconds to evaluate the metric
    - 📝 High resolution custom metrics
      - Decreases as metrics age: 1 sec (for 3 hours), then 1 minute (for 15 days), 5 minute (for 63 days), 1 hour for 15 months.
    - E.g. `NetworkOut < 2.000.000` for 1 data points (EC2) within 5 minutes
  - Data points
    - Represents the values of that variable over time
    - E.g. if period is 5 minutes and data points is three then the alarm will trigger after 15 minutes of being condition met
- **Events**
  - **Event Rule**
    - Types
      - ***Schedule***: Notifications that'll be triggered on demand
      - ***Event Pattern***: React to service doing something e.g. CodePipeline state changes.
    - Targets: e.g. lambda function, EC2 `StopInstances` API call, SNS, SQS, ECS Task, Event bus in another AWS account...
  - Triggers to Lambda functions, SQS/SNS/Kinesis Messages
  - CloudWatch Event creates a small JSON document to give information about the change

## CloudTrail

- Tracks API events allowing you to see who accessed what resources and when.
- CloudTrail reports on who made the change, when, and from which location.
- Per AWS account & per region
  - 💡 Should be enabled in all regions with a cloud formation stack.
  - All accounts / regions can log into same S3 bucket in an account / region.
  - In a region when you apply the trail to all regions, CloudTrail creates a new trail in all other regions.
- Enabled by default
  - Default metrics are from hypervisor (e.g. CPU, connections)
  - Many services has deeper "Advanced monitoring" for inside hypervisor metrics such as connected users, CPU usage per thread / application.
- **Encryption**
  - A single KMS key can be used to encrypt log files for trails applied to all regions.
  - CloudTrail log files are by default encrypted using S3 Server Side Encryption (SSE)
  - You can also enable encryption SSE KMS for additional security
- Get an history of events / API calls made within your AWS Account by Console, SDK, CLI, AWS Services
- Can push to S3 (encrypted by default), CloudWatch Logs and SNS.
- Has 90 days of retention
- 📝 If a resource is deleted in AWS, look into CloudTrail first!

## AWS Config

- Asses, audit, and evaluate the configurations of your AWS resources.
  - Resources include RDS, subnets, DB snapshots, security groups, and event subscriptions.
- Reports on what has changed
  - You can e.g. look back and see what instances were in default VPC last week.
- Per AWS account & per region
  - 💡 Should be enabled in all regions with a cloud formation stack.
  - Data aggregation in AWS Config allows you to aggregate AWS Config data from multiple accounts and regions into a single account.
- Tracks resource state.
- AWS Config is around compliance, Trusted Advisor is more around recommendations but they check same things for security.
- Integrates with SNS to receive notifications.
# EC2 - Elastic Compute Cloud

- Some capabilities/integrations:
  - **EC2**: Renting virtual machines
  - **EBS**: Storing data on virtual drives
  - **ELB**: Distributing load across machines
  - **ASG** Scaling the services using an auto-scaling group
- **Pricing**
  - Billed only when in `running` state.
    - And `stopping` state only if it's hibernating.
    - Also `stopped` state for reserved instance.
  - Billed by the hour or the second, depending on which AMI you're running
  - `t2.micro` is free tier.
  - **Data transfer costs**
    - Free between EC2 and other services.
    - Free between EC2 instances in same availability zone using private IP addresses only.
    - $0.01/GB for transfer between instances in different availability zones
    - See also [data transfer costs](./1.%20Introduction.md#data-transfer-charges)
- **Elastic Network Interface (ENI)**
  - Each EC2 has one default ENI.
  - Each ENI have
    - one primary private IPv4, can have additional private IPv4 (optional)
    - one **Elastic IP** (static IP) per private IPv4 (optional)
    - one public IPv4
    - one or more IPv6
    - one or more security group
    - a mac address
    - source destination check flag
    - description of ENI
  - Can attach a secondary network interface
  - You can attach a network interface to an EC2 instance in the following ways:
    - **Hot attach**: When it's running
    - **Warm attach**: When it's stopped
    - **Cold attach**: When the instance is being launched
  - Termination with instance termination
    - Default interfaces are terminated with instance termination.
    - Manually added interfaces are not terminated by default
- Type describes how powerful the machine is (vCPUs, Memory, Network performance etc.)
- **EC2 Tenancy** allows you to choose a tenant for the instance
  - ***Run a shared hardware*** - Default
  - ***Dedicated*** - Run a Dedicated instance, shared within different instance (non-dedicated) from same AWS account.
  - ***Dedicated host*** - You have visibility and control over how instances are placed on the server
- **Status Checks** checks physical machine (underlying hyper-visor) and returns pass & fail.
  - You can set-up **Status Checks** alarms.
  - Auto Scaling Group uses this check by default.
    - Can set to use Elastic Load Balancer's health check.
- You can enable ***termination protection*** *(termination = deletion)*
  - Prevents an instance from being accidentally terminated by requiring that you disable the protection before terminating the instance
- **EC2 Instance Metadata**
  - Allows AWS EC2 instances to learn about themselves without using an IAM Role / permission.
  - The internal URLs are accessible only by EC2
  - You can retrieve the IAM Role name from the metadata
    - ❗ You can not retrieve the IAM policy
  - ***Meta data*** = Info about the EC2 instance at `http://169.254.169.254/latest/meta-data`
    - Your EC2 instance has always credentials to the role that usually expires in 1 hour
      - Get IAM credentials: `http://169.254.169.254/latest/meta-data/iam/security-credentials/<iam-role-name>`
  - ***User data*** = launch script of the EC2 instance `http://169.254.169.254/latest/user-data`
- **Enhanced Networking** can be enabled on specific instances with a driver to reduce networking overhead + faster networking.
  - 📝Single root I/O virtualization (SR-IOV)
  - Utilizes Elastic Network Adapter (high Performance Network Interface for Amazon EC2)
  - Free
  - ❗ Limitations
    - Instances must be launched in a VPC
    - Instances must be launched from a HVM AMI
  - **Elastic Network Adapter**: High performance
  - **Elastic Fabric Adapter**: High Performance Computing
    - ❗ Linux machines only
- **Monitoring**
  - ❗📝 CloudWatch does not monitor memory usage as well as disk space utilization
    - 💡 Requires installation of CloudWatch agent or use custom scripts to send custom metrics.
- **Launch templates**
  - Store launch parameters so that you don't specify them every time you launch an instance.
  - E.g. AMI ID, instance type, key pairs and Security Groups.t
- 💡 An instance may immediately terminate if:
  - You've reached your EBS volume limit
  - An EBS snapshot is corrupt
  - The root EBS volume is encrypted and you do not have permissions to access the KMS key for decryption
  - The instance store-backed AMI that is attached is missing a required part (an image.part.xx file)

## Basic tasks

- **Hibernating** an instance means persisting all of its data and RAM so you can pre-warm an instance before resuming.
- **Stopping and starting** an instance migrate EC2 to a most likely new underlying host.
- **Retiring**: An instance is scheduled to be **retired** when AWS detects irreparable failure of the underlying hardware hosting the instance.
  - When an instance reaches its scheduled retirement date
    - It is stopped (if it has EBS root volume) or
    - Terminated (if it has instance instance store root volume) by AWS
- **📝Get meta data**
  - Request to EC2 metadata service from your EC2 instance
    - `http://169.254.169.254/latest/meta-data`
    - It returns the properties (recursively)
    - Get data further e.g. `http://169.254.169.254/latest/meta-data/placement/availability-zone`
  - You can also use `Instance Metadata Query Tool` which is a simple bash script that does the curl.
- **Launch virtual server using AWS Console**
  1. Go to EC2 -> Launch an instance -> Choose Amazon Machine Image (AMI)
  2. Choose type for machine (e.g. `t2.nano`, `t2.micro`)
  3. Configurations
     - Storage: EBS is where the data will be stored (SSD, HDD etc.)
     - You can have different tags
       - *Name* is important as it appears in UI.
  4. Launch
     - For SSH you need to have key pair, you can generate new or use existing.
- **Connect to EC2 through SSH**
  - SSH with e.g. `ssh -i privatekey.pem <user-name>@<ip-address>`
    - Username can be `ec2-user` (Amazon Linux, RHEL, SUSE, FreeBSD), `ubuntu` (Ubuntu, NanoStack), `admin` (Debian), `root` (RHEL, TurnKey, OmniOS), `fedora` (Fedora), `centos` (Centos), ``bitnami` (BitNami)
    - IP address is the public IP address of the machine, you cannot use private IP to connect.
    - Private key `privatekey.pem` is enough as AWS puts public key automatically to `~/.ssh/authorized_keys`
  - 💡 When you first download key-pair file permission is 0644 which is to open -> private key can leak as it's accessible by others.
    - 📝Fix with `chmod 0400 awsEc2.pem` -> only root user can see it.
    - 📝chmod 400 is for Linux only
  - On Linux / Mac we use SSH, on Windows we use PuTTy (on Windows 10 SSH can be used as well)
- **Configuring Windows machines**
  - **EC2 Launch** is PowerShell scripts that makes the computer AWS-friendly
    - It sends instance information to EC2 console, sets computer name & wallpaper, adds DNS suffixes, executes user data, persistence static routes to reach the metadata & KMS services, dynamically extends OS partition to include any unpartitioned space and more...

## Security Groups

- Firewall on EC2 instances.
- Associated to ENI and not with instance
- Controls authorized IP ranges (IPv4 and IPv6) and ports with:
  - **Inbound rules**
    - From other to the instance
    - All inbound rules are denied by default
    - You define protocol (e.g. TCP) & port range & source
      - **Source**
        - 📝Refer another security group (type in Security Group ID)
        - An IPv4 or IPv6 CIDR block
        - A single IPv4 or IPv6 address (e.g. anywhere: `0.0.0.0/0` for IPv4 and `::/0` for IPv6)
  - **Outbound rules**
    - From the instance to other
    - Open to everywhere by default
    - You define protocol (e.g. TCP) & port range & destination.
      - **Destination**
        - 📝Another security group
        - An IPv4 or IPv6 CIDR block
        - A single IPv4 or IPv6 address
        - An AWS service (i.e. prefix list ID)
- Can be attached to multiple instances
- 📝Security groups are sateful(once allowed inbound, connection passes through outbound and vice versa)
- An instance can have multiple security groups
- Locked down to a region / VPC combination
  - ❗ You need to recreate security groups if you change region / VPC.
  - You can create a snapshot => Create AMI and use AMI to launch in other AZ or copy AMI to new region and launch there.
- 💡 Good practice: maintain one separate security group for SSH access.
- 📝Any ***timeout errors*** means a misconfiguration of your security groups
- If your application gives "connection refused" error, then it's an application error or it's not launched
- Being able to reference security groups allows:
  - Easier maintenance as you don't need to work with IP's.
  - E.g. allow EC2 instances to connect with each other by authorizing their attached security groups.
- ❗ You can only delete security group after you delete all EC2 instances associated with the security group.
- ❗📝 You cannot block single IP/IP-range with Security Groups, you'll need NACL (Network Access Control Lists) for it.
  - Security groups can only "allow" services through a default deny
  
## EC2 User Data

- Enables to bootstrap instances using EC2 data script.
  - *bootstrapping* means launching commands when a machine starts
- Script is only run once at the instance first start
- Enables automating boot tasks such as *installing updates*, *installing software*, *downloading common files from internet* ....
- Runs with the root user (`sudo` rights)
- 📝Get user data -> Request to `http://169.254.169.254/latest/user-data` from your EC2 instance
- **Flow**
  - Create EC2 -> Configure Instance -> Advanced Details -> User data -> Paste commands
  - 💡 In first line add `#!/bin/bash` -> tells Unix what program to use to run it
- For faster deployment without bootstrapping use **Golden AMI**s:
  1. Create a golden AMI with the static installation components already setup
  2. Use EC2 user data to customize the dynamic installation parts at boot time.
     - 🤗 Elastic Beanstalk is a good example for mixing golden AMIs and EC2 user data.

## EC2 Instance Launch Types

- **On demand instances**
  - Start whenever you want & stop without any long time commitment.
  - Pricing
    - Pay for what you use (billing per second, after the first minute)
    - Has highest cost but no upfront payment
  - 💡 Recommended for short-term and uninterrupted workload, where you can't predict how the application will behave.
    - E.g. when doing auto-scaling
- **Reserved instances**
  - **Standard Reserved instances**
    - Reserve an instance for 1 to 3 years
    - Enables you to modify Availability Zone, scope, network platform, and instance size (within the same instance type)
      - Up to 75% discount compared to On-demand
      - Payment options are "no upfront" (pay monthly), "partial upfront", "all upfront" (cheaper).
    - You can sell sell your unused instances on the AWS Reserved Instance Marketplace.
    - 💡 Recommended when e.g. when running a database over a year
    - 💡 Reserve minimum capacity you'll use in year e.g. 1 EC2 instance in each AZ in multi-AZ architecture.
    - 💡 Can be sold in Reserved Instance Marketplace
  - **Convertible Reserved Instances**
    - Convertible means you have option to change:
      - Instance family, instance type, platform, scope, and tenancy
      - Only if the creation of new instance is equal or greater value
    - Pricing: Up to 54% discount
    - 💡 Workloads are likely to change
  - ***Scheduled Reserved Instances***
    - Launch within time window you reserve
    - 💡 You know something will happen e.g. every week but only during one hour or one day.
- **Spot instances**
  - Cheapest (up to 90% discount compared to on demand) but risk of losing instance
    - Spot instance is charged per second.
    - If AWS shuts down your instance in first hour you don't pay, but you have to pay if you terminate.
  - You bid a maximum price and get the instance as long as it's under the price
    - You lose the instance if you get outbid
      - Instances are reclaimed with a 2 minute notification warning when the spot price goes above your bid.
      - ***Persistence request*** allows instances to come back when the price drops to the right pricing.
    - Price varies based on offer and demand at times
  - **📝Spot instance shut down priority**: The one who had longer time (e.g. 50 min) will be shut down faster than the shorter time (e.g. 5 min)
  - 💡 For short & fault tolerant workloads e.g. batch jobs, Big Data analysis.
  - In the event of an interruption, you can set instances to be **terminated** or **hibernated**.
  - You may get insufficient capacity error
    - Stop all instances & start again to end up in a new hardware.
- ❗ 20 On-Demand & 20 Reserved & 20 Spot instances per region (soft limit)
- **Spot Block**
  - Allows you to request Spot instances for 1 to 6 hours at a time to avoid being interrupted while your job completes

### Tenancy

- Each instance that you launch into a VPC has a tenancy attribute
  - **Default**: Your instance runs on shared hardware.
  - **Dedicated**: Your instance runs on single-tenant hardware.
  - **Host**: Your instance runs on a Dedicated Host, which is an isolated server with configurations that you can control.
- ❗ [Cannot change tenancy](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-instance.html#dedicated-howitworks) after launching instances when
  - From *default* to *dedicated* or *host*.
  - From *dedicated* or *host* to *default*.
  - 💡 You only switch between dedicated instances i.e. from *host* to *dedicated* and from *host* to *dedicated*.
  - A VPC tenancy can be changed to any but it only affects new instances.
- **Dedicated instances**
  - Instances running on hardware that's dedicated to you
    - You don't control your hardware.
    - May share hardware with other instances in same account
  - No control over instance placement (can move hardware after Stop / Start)
- **Dedicated Hosts**
  - Physical dedicated EC2 server for your use
  - Full control of EC2 instance placement
  - Visibility into the underlying sockets / physical cores of the hardware
    - You have direct access to the CPU
  - Allocated for your account for a 3 year period reservation
  - More expensive
  - You can allow instance auto-placement where untargeted EC2s will be launched in dedicated host(s).
  - Useful for
    - Complicated licenses such as BYOL - Bring Your Own License, or you pay based on CPU cores etc.
      - ❗ A Dedicated Host is required if you'd like to use your existing Windows Server licenses.
    - Regulatory & compliance needs.
  - ***Dedicated Instances vs Dedicated hosts***

    | Characteristics | Dedicated Instances | Dedicated Hosts |
    | --------------- | ------------------- | --------------- |
    | Enables the use of dedicated physical servers | X | X |
    | Per instance billing (subject to a $2 per region fee) | X | |
    | Per host billing | | X |
    | Visibility of sockets, cores, host ID | | X |
    | Affinity between a host and instance | | X |
    | Targeted & automatic instance placement | | X |
    | Add capacity using an allocation request | | X |

## EC2 Instance Types

- 📝**Main types**

  | Type | Mnemonic | Category | Description | Use-cases |
  | ---- | -------- | -------- | ----------- | --------- |
  | M | (M)ain / (M)edium | General purpose | Balance of compute, memory, network resources | General, mid-size databases, web apps |
  | C | (C)ompute |  Compute optimized | Advanced CPUs | Modeling, analytics, databases |
  | H | (H)DD | Storage optimized | Local HDD storage | Map reduce |
  | R | (R)am | Memory Optimized | More ram per $ | In-memory caching |
  | X | (X)treme | Memory Optimized | Terabytes of RAM and SSD | In-memory databases |
  | I | (I)ops | IO optimized | Local SSD storage, High IOPS | NoSQL databases |
  | G | (G)PU | GPU Graphics | GPUs with video encoders | 3D rendering |
  | P | (P)ictures | GPU Compute | GPUs with tensor cores | Machine learning |
  | F | (F)ield | Accelerated Computing | Field Programmable Gate Array, custom hardware accelerations | Genomics, financial analytics |
  | T | (T)iny / (T)urbo | Burstable, Shared CPUs, lowest cost | Web servers |

  - Remember all: PHD McGift Rx
- Numbers change over time and indicate generation e.g. `t2.`, `t3.`.
- **Burstable instances**
  - Good performance for a burst / short while e.g. spike and your CPU goes to 100%
  - Burst credits
    - Bursting consumes burst credits and low CPU gets you credits.
    - You get more credits when your CPU is low.
- T2 / T3 unlimited provides *unlimited burst* but you pay extra if you go over your credit balance.
- You can also have *Bare Metal* instances to eliminate virtualization overhead.
- X1e is part of Memory Optimized  instance family and recommended for databases.

## EC2 AMIs

- AMI = Amazon Machine Images
- AWS has base images for Ubuntu, Fedora, Red Hat, Windows, Amazon Linux etc.
- AMI can be found and published on the Amazon Marketplace
- **Two virtualization types**
  - **HVM VM** - Hardware virtual machine
    - Utilizes special hardware extensions as it's on bare metal hardware.
    - Enhanced Networking
    - Only option for Windows.
  - **PV VM** - Paravirtual virtual machines, only available for Linux
  - 💡 HVM is recommended because same or better performance than paravirtual guests.
    - ***Before***: Better storage & network in PV VM because of special I/O drivers.
    - ***Now***: PV drivers are available for HVM VM (instead of emulating hardware)
- **Custom AMIs**
  - AMIs can be built for Linux/Windows machines.
  - Removes need for EC2 user data with faster boot time.
  - Can be created from volumes or snapshots.
  - Can use pre-configured & maintained public AMIs from other people by paying by hour.
  - ❗ AMI are built for a specific AWS region
    - An AMI created for a region can only be seen in that region.
    - 📝 Copying makes it available in different regions for e.g. disaster recovery.
  - ❗ Do not use AMI you don't trust, they may be unsecure or contain malware.
  - **VM Import/Export** allows you to import OVA file (or VMDK, VHD, or RAW) as AMI then create EC2 based on it.
    - **AWS SMS - Server Migration Service**
      - Enhances VM import & export
      - Migrate thousands (concurrently 50) of Hyper-V and VMware workloads to AWS.
- **AMI Storage**
  - S3 but you won't see them in the S3 console
  - By default, your AMIs are private and locked for your account / region.
    - You can make AMIs public in order to share with other accounts or sell in AMI Marketplace.
- **AMI Pricing**: Cheap as you only pay for space it takes in S3.

### Common tasks

- **Copying an AMI**
- AWS does not batch copy following, need manual work:
  - User-defined tags
  - Launch permissions
  - S3 bucket permissions
- **Create AMI from EC2**
  - Right click on EC2 -> Image -> Create Image
  - When it's done it appears on EC2 dashboard -> Images -> AMIs
  - You can right click and
    - *Copy* it to another region
    - *Deregister* it in order to remove it.
    - In *image permissions*:
      - Make it publicly available, or share it with other AWS account numbers.
      - Create volume permissions option allows others accessing AMI to make a copy of the AMI.
    - *Launch* a new EC2 based on it.
- 📝**Cross account / cross region AMI Copy**
  - Share AMI with another AWS account
    - Sharing an AMI does not affect the ownership of the AMI.
      - ❗ However if you copy an AMI that has been shared with your account, you are the owner of the target AMI in your account.
    - Flow:
      1. Right click on EC2 -> Image Permissions
      2. Keep it private & add account number.
      3. Select *Add "create volume permissions to snapshot* to allow other accounts to make a copy.
  - Ensure you have read permissions for AMI storage:
    - Associated EBS snapshot (for an Amazon EBS-backed AMI)
    - Associated S3 bucket (for an instance store-backed AMI)
  - ❗ You can't copy:
    - Encrypted AMI shared from another account
      - 💡 Instead you can copy the snapshot (if it's shared with encryption key) and re-encrypt it & register as a new AMI you own.
    - AMI with an associated **billingProduct** shared from another account
      - They're Windows AMIs and AMIs from the AWS marketplace
      - 💡 You can launch an EC2 instance in your account using the shared AMI -> Create an AMI from the instance

## EC2 Placement Groups

- Use to control how EC2 instances will be placed within AWS infrastructure.
- ❗ Works only for certain types of instances
- ❗ You can't merge placement group
- ❗ Placement group must have unique name within AWS account.
- ❗ You can't move existing EC2 to placement group -> you can create AMI and launch new.
- 💡 AWS recommends homogenous instances within placement groups e.g. using same instance types.
- ***Creation***
  - Console -> EC2 -> Placement Groups -> Create Placement Group -> Select a strategy
  - You can now launch new EC2 instances in the placement group.
- **Placement Group Strategies**
  - **Cluster**
    - Clusters instances into a low-latency group in a single Availability Zone
      - ❗ Cannot span multiple AZ
    - High performance, high risk
      - High network throughput, low-latency (10 Gbps bandwidth between instances)
      - Same rack & same AZ -> If the rack fails, all instances fails at the same time.
    - 💡 Use cases
      - Big Data job that needs to complete fast
      - Application that needs extremely low latency and high network throughput.
  - **Spread**
    - Spreads instances across underlying hardware (❗ max 7 instances per group per AZ)
      - Ensures all instances are located in different hardware.
    - Can span across multi AZ.
    - Minimizes failure risks.
  - **Partition**
    - Spreads instances across many different partitions (which rely on different sets of racks) within an AZ.
    - Partitions are isolated from each other from hardware failures in different racks but not VMs.
    - Can span across multi AZ.
    - Scales to 100s of EC2 instances per group.
    - ❗ Up to 7 partitions per AZ
    - EC2 instances get access to the partition information as metadata
    - 💡 Use cases: HDFS, HBase, Cassandra, Kafka
# Elastic Beanstalk

- Developer centric view of deploying an application on AWS.
- Manages infrastructure for the code by making following very easy:
  - Configuration of databases
  - Configuration of load balancers
  - Configuration of auto-scaling
- Instance / OS configuration / deployment is handled by Beanstalk but are configurable.
- **Pricing**: Elastic BeanStalk is free! You pay for the underlying resources.
- It deploys common HA architectures with EC2, ALB, ASG, RDS, etc..
  1. **Single instance deployments**: good for dev
  2. **LB + ASG**: good for prod + pre-production
  3. **ASG only**: good for non-web apps in production (workers, etc.)
- Still have full control over the configuration
- Support for many platforms: Go, Java SE, Java with Tomcat, .NET on Windows Server with IIS, Node.js, PHP, Python, Ruby, Packer Builder, Single Container Docker, multi-container Docker, Preconfigured Docker
- Deployment:
  - Has three components:
    - Application
    - Application version (increased in each deployment)
    - Environment name e.g. dev, test, prod.
  - You deploy application versions to environments can promote application versions to the next environment.
  - Rollback feature to previous application version
  - Full control over lifecycle of environments
  - If not supported, you can write your custom platform (advanced)
- Can run containers:
  - **Single Container Docker**: 1 container per Beanstalk instance.
  - **Multi-container Docker**: Many docker instances within same Beanstalk instance.
  - **Preconfigured Docker Containers**: e.g. *Java with Glassfish* or *Python with uWSGI*.
- **Configurations**
  - **Presets**
    - **Low cost** (free tier eligible): One EC2 + Elastic IP
    - **High availability**: ELB + ASG
    - **Custom configuration**: Customize everything
  - **Software**
    - Environment (e.g. NodeJs) version and environment properties
    - AWS X-Ray enabled/disabled
    - Rotate logs (disabled/enabled)
    - Log streaming (disabled/enabled)
  - **Instances**
    - Instance type, image ID, root volume type, root volume size, root volume IOPS, security groups
  - **Capacity**
    - Environment type (e.g. load balancing, auto-scaling)
    - Availability zones
    - Instances
  - **Load balancer**: type, listener (e.g. 1), processes (e.g. 1), rules (e.g. 1)
  - **Rolling updates and deployments**
    - Deployment policy (e.g. all at once)
    - Rolling updates (disabled/enabled)
    - Health check(enabled/disabled)
  - **Security**
    - Service role (e.g. `aws-elasticbeanstalk-service-role`)
    - VM key pair
    - VM instance profile.
  - **Monitoring**
    - Health reporting system (enhanced)
    - Ignore HTTP 4xx *(disabled/enabled)*
    - Health event log streaming *(disabled/enabled)*
  - **Managed updates** *(enabled/disabled)*
  - **Notifications** with e.g. (email address)
  - **Network**: VPC
  - **Database**: engine, instance class, storage (GB), multi-AZ)
# Containers

- ECS is a container orchestration service
- Helps you run Docker containers on EC2 machines
- **Tasks**
  - An application can have many tasks.
  - A task contains one or more (max 10) containers.
  - Applications are defined using **task definitions**.
    - You can apply a single IAM role to a task definition.
    - Other task definitions include: *Which docker images*, *CPU/memory*, *whether containers are linked*, *networking mode*, *ports that should be mapped/opened*, *whether task should continue if container finished or fails*, *commands to execute when container is started*, *environment variables to pass*, *data volumes*.
  - A task definitions can have two **launch types**:
    - **EC2** (original): Runs ECS on user-provisioned EC2 instances
      - Requires installation of **ECS Container Agent**
      - Both for Windows + Linux
      - Included in the Amazon ECS optimized AMI
      - Can also be installed on any EC2 instance that supports the ECS specification
    - **Fargate**: Runs ECS tasks on AWS provisioned compute (serverless)
  - **Scheduling tasks**
    - **Service scheduler** for long-running stateless tasks and applications.
    - **Manually running tasks** ideal for batch jobs that perform work and stop.
    - **Scheduled tasks (cron)**: run based on cron expression or based on CloudWatch Events rule.
    - **Custom schedulers**: Your own schedulers or third party e.g. Blox.
- **ECS Clusters**
  - Logical grouping of container instances that you can place tasks on.
  - Clusters can contain tasks using BOTH the Fargate and EC2 launch type
  - Each container instance may only be part of one cluster at a time
  - Clusters are Region-specific.
  - For tasks using the EC2 launch type, clusters can contain multiple different container instance types.
- Made of:
  - **EKS - Elastic Kubernetes Service**
    - Running ECS on AWS-powered Kubernetes (running on EC2)
    - Pros:
      - Service discovery
      - Big open source ecosystem and good competence
      - Internal networking & network overlay
      - No vendor lock-in
    - Cons: more complexity & costs
  - **ECR - Docker Container Registry**
    - Hosted by AWS
    - Store, managed and deploy your containers on AWS
    - Fully integrated IAM & ECS
    - Sent over HTTPS (encryption in flight) and encrypted at rest
    - ECS pulls from ECR with IAM role, while CodeBuild pushes images (CI/CD) to ECR.
- IAM security and roles at the ECS task level
- **Docker**
  - Allows application to work the same way anywhere where docker is installed
  - Containers are isolated from each other
  - Control how much memory / CPU is allocated to your container
  - Ability to restrict network rules
  - More efficient than Virtual machines
  - Scale containers up and down very quickly
- **Use cases**
  - Run microservices
    - Ability to run multiple docker containers on the same machine
    - Easy service discovery features to enhance communication
    - Direct integration with Application Load Balancers
    - Auto scaling capability
  - Run batch processing / scheduled tasks
    - Schedule ECS containers to run on On-demand / Reserved / Spot instances
  - Migrate applications to the cloud
    - Dockerize legacy applications running on premise
    - Move Docker containers to run on ECS
- **Concepts**
  - **ECS cluster**: Set of EC2 instances
  - **ECS service**: Applications definitions running on EC2 cluster
- **Security**
  - **ECS IAM roles**: Roles assigned to tasks to interact with AWS.
  - **Security groups**: Can be associated to container instances.
- **ALB Integration**
  - 📝 Application Load Balancer (ALB) has a direct integration feature with ECS called "port mapping"
    - Allows you to run multiple instances of the same application on the same EC2 machine
    - Dynamic port mapping is not available in classic load balancer and allows the same port to be mapped to many.
      - E.g. containers on port 6789, 9586, 3748 can be exposed on port 80 /443 by application balancer.
  - Use cases:
    - Increased resiliency even if running on one EC2 instance
    - Maximize utilization of CPU / cores
    - Ability to perform rolling upgrades without impacting application uptime
- **ECS Setup & Config file**
  - Run an EC2 instance, install the ECS agent with ECS config file
    - Or use an ECS-ready Linux AMI (still need to modify config file)
      - Config file is at `/etc/ecs/ecs.config`

        ```bash
          ECS_CLUSTER=MyCluster #Assign EC2 instance to an ECS cluster
          ECS_ENGINE_AUTH_DATA={..} #to pull images from private registries
          ECS_AVAILABLE_LOGGING_DRIVERS=[..] #CloudWatch container logging
          ECS_ENABLE_TASK_IAM_ROLE=true #Enable IAM roles for ECS tasks
        ```
# AWS Lambda

## Serverless introduction

- Cloud history: Data Centre => IaaS (EC2, 2016) => PaaS => Containers => Serverless
- Concept came up with Faas (Function as a Service) = Lambda
  - Today the definition includes anything that's managed where you developers don't see / provision / manage servers.
  - In AWS: Lambda & Step Functions, DynamoDB, AWS Cognito, AWS API Gateway, S3, SNS & SQS, Kinesis, Aurora Serverless...
- Serverless application examples:
  - Users *->* Rest API *->* API Gateway *->* Lambda *->* DynamoDB with log in function from Amazon Cognito.
  - Serverless thumbnail creation
    - User uploads image to S3
    - S3 triggers lambda function to create a thumbnail
    - Lambda
      - Creates & pushes thumbnail image into S3
      - Saves metadata in DynamoDB

## Lambda

- **Lambda vs EC2**

  | EC2 | Lambda |
  |-----|--------|
  | Virtual servers in the cloud  | Virtual functions -> no servers to manage! |
  | Limited by RAM and CPU | Limited by time - short executions |
  | Continuously running | Run on-demand |
  | Scaling means intervention to add / remove servers | Scaling is automated |
- **Pricing**
  1. Pay per **calls**.
  2. Pay per **duration** (in increment of 100ms)
     - E.g. you get 400.000 GBs of FREE compute time
       - = 400.000 seconds if function is 1 GB RAM
       - = 3.200.000 seconds if function is 128 MB RAM
- **Integrations**
  - Almost whole AWS Stack can trigger it: API Gateway, Kinesis, DynamoDB, S3, AWS IoT, CloudWatch Events, CloudWatch Logs, AWS SNS, AWS Cognito, Amazon SQS and more.
- Can use different programming languages: Node.js (JavaScript), Python, Java, C# (.NET CORE), Golang, C# / PowerShell, C++ and more.
- Lambda like EC2 and ECS supports hyper-threading on one or more virtual CPUs.
- **Lambda@Edge** lets you run lambda functions in edge locations.
- ***Blueprint***s are code templates for writing Lambda functions.
- You can test lambdas directly on portal by configuring & sending ***test event***s in the Console
- **Security**
  - ***IAM role*** must be attached to it.
  - Deployed within a **VPC** as default.
    - To enable your Lambda function to access resources inside your private VPC:
      - Give subnet IDs and security group IDs
      - Lambda uses those IDs to set up ENIs.
    - AWS Lambda uses this information to set up elastic network interfaces (ENIs) that enable your function to connect securely to other resources within your private VPC.
    - 💡 In your subnet you need enough available IP / ENI's otherwise you get `EC2ThrottledException` for concurrent execution.
  - Lambda can have **Security Groups**.
  - Auditing and compliance through ***CloudTrail*** logging.
- **Configurations**
  - **Timeout**: Default 3 seconds, 📝max of 15 minutes
    - Function fails directly after timeout
  - Environment variables that can be accessed directly from the code.
  - **Allocated memory** (128MB to 10 GB)
    - 💡 Increasing RAM will also improve CPU and network!
    - 📝Scaling is automated.
  - **Lambda DLQ**
    - Debugging and error handling through dead letter queues
    - Can be SNS or [SQS queue](./10.1.1.%20Integrations%20-%20Queues%20-%20SQS.md#dead-latter-queue-dlq)
  - **Encryption**
    - Encryption helpers to pass secure credentials in an encrypted manner.
    - Prevents other developer who has access to console from seeing the credentials.
- **Scaling**
  - ❗ Concurrency limits how many lambda functions can be executed simultaneously.
    - For initial burst between 500-3000 depending on region.
    - Later: 500 per minute until limit is reached.
    - Concurrency limit starts from 1000 (soft limit)
- 💡 Architectures can get complicated -> AWS X-ray allows you to debug what's happening.
  - Trace and analyse keywords.
- ❗📝 Limitations
  - RAM: Up to 10GB
  - **Deployment**
    - **Max size** 250 MB or 50 MB (zipped)
      - 💡 Overcome limit: use `/tmp` directory to load other files at startup
    - **Size of environment variables**: 4 KB
  - **Execution**
    - **Memory allocation**: 128 MB - 10 GB (64 MB increments)
    - **Maximum execution time**: 15 minutes
    - **Disk capacity** in the "function container" (in `/tmp`): 512 MB
    - **Concurrency limits**: 1000 (soft limit)
# EC2 Storage (EBS, EFS & Instance Store)

- AWS Storage Cloud Native Options
  - **Block** (volumes): Amazon EBS, EC2 Instance Store
  - **File** (network file system): Amazon EFS
  - **Object**: S3, Glacier
- **Root Volumes**
  - Root volumes are ephemeral by default i.e. gets deleted when instance is terminated.
  - Both instance store and EFS can be root volume.
    - When you launch an instance, the *root device volume* contains the image used to boot the instance.
    - **EBS** is recommended over **Instance store** as it launches faster and is more consistent.

## Block device mapping

- **Block device**
  - Moves data in sequences of bytes or bits, allows random access.
  - E.g. CD-ROM, flash drives, hard disks.
  - Two supported block devices in AWS:
    - Instance store
    - EBS volumes
- **Block device mapping**
  - Allows block devices to attach to an EC2.
  - Can be defined when you launch an instance (or in [AMI](./4.1.%20Compute%20-%20EC2.md#ec2-amis))
  - Allows you to attach additional EBS volumes during and after launch
    - ❗ Cannot attach instance stores after launching an instance.
    - ❗ Additional volumes are not marked for deletion upon instance termination.

## EC2 storage types

- Include
  - **[EBS](#ebs-elastic-block-store)**: Elastic Block Store
  - **[EFS](#efs-elastic-file-system)**: Elastic File System
  - **AWS FSx**: 📝EFS for Windows.
- Comparison

  | Type | When to use | Snapshots | Scaling |
  | ---- | ----------- | --------- | ------- |
  | [**EBS**](#ebs-elastic-block-store) | Storing data in single instance | In S3 | Can scale volume type & size (available ratios - 3:1 & 50:1 for IOPS per GB), no downtime but needs OS repartitioning |
  | [**EFS**](#efs-elastic-file-system) | Storing data in distributed applications | Not supported | Auto-scaling |
  | [Instance store](#instance-store) | High performance IOPS when persistance is not needed | Not supported | Cannot resize |

### Instance store

- Actual root volume of some instances (some others come with EBS volumes)
- 📝 Ephemeral (temporary) storage physically attached to the machine
  - ❗ You lose data when instance is terminated or stopped
    - But data ata survives reboots
    - Unexpected terminations might happen from time to time (AWS will e-mail you)
  - Cannot be detached or reattached i.e. remapped to another instances
- 📝 Higher performance (millions of IOPS)
  - It has better I/O than EBS as it's not a network drive which EBS is
- 💡 Choose instance store if you can handle losing data e.g. caches otherwise EBS
- ❗ No managed backups, cannot resize.
- ❗ You can't attach instance store volumes to an instance after you've launched it

### EFS (Elastic File System)

- Managed NFS (network file system)
- POSIX compliant
  - POSIX = Portable Operating System Interface for Unix
  - It supports Unix APIs but it's ❗ Linux only
  - 💡 POSIX permissions allows you to restrict access from hosts by user and group
- More expensive (≈ 3x [`gp2` (EBS)](#ebs-volume-types))
- Can be mounted on hundreds of instances
  - ❗ Is not attached, it's mounted!
  - Use ENI (Elastic Network Interface) to reveal EFS (elastic file storage) for multiple different EC2 instances.
  - **Configurations**
    1. Set Linux file system permissions on the presented EFS volume using `chmod` and `chown`.
    2. Mount EFS volume to your EC2 instance using `mount -t nfs -o xxxx`.
    3. Configure a Security Group to allow data traffic on port `2049`
       - in EFS SG to connect to the EFS target.
       - in EC2 SG to connect to the EC2 server.
- Can be mounted to on-prem using AWS Direct Connect
  - Allows you to migrate data from on-prem servers to EFS (EFS File Sync) or/and use it for backup solution.
- Pay per use (not for provisioned space)
- Region-specific but ***AWS DataSync*** can be used to sync data in different EFS in different regions.
- **Scalability**
  - **Auto-scaling** without provisioning capacity.
    - Elastic storage capacity: can grow and shrink based on the needs (files)
  - Ability to burst up to multiple GB/s for short periods of time.
- **Availability**
  - **Multi-AZ**: Data is stored across multiple AZ within a region.
    - Each EFS instance in AZ get IP address
- **Connections**: **Multi-region** or **hybrid** through AWS VPN and Inter-Region VPC Peering.
- Uses NFS v4.1 protocol
  - Allow inbound (NFS -> TCP 204) from EC2 instances in security group of EFS
- Configurations:
  - Performance mode
    1. ***General purpose*** (default)
    2. ***Max I/O*** - used when thousands of EC2 are using the EFS - higher latency & more IO.
  - Throughput mode
    1. ***Bursting***: Recommended for most file systems
    2. ***Provisioned***: Use when throughput from bursting is not enough
- Backup EFS-to-EFS (incremental - can choose frequency)
- **Give per user access**
  1. Create a subdirectory for each user
  2. Grant read-write-execute permissions to the users.
  3. Mount the subdirectory to the users' home directory
- Encryption at rest using KMS keys
- 💡 Choose when you need to share files e.g. on-prem sync to sync, content management, web serving, data sharing, Wordpress.

### EBS (Elastic Block Store)

- Local storage -> 📝 Can be mounted on a single instance.
- Network drive (little latency for network communication to the instance)
  - You can attach to your instances while they run.
    - As there's no filesystem on device so you must create one & mount it, [check documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html).
  - Persists data but can requires to set deletion on EC2 termination to false
- Can be root volume
  - ❗📝 Gets terminated by default if EC2 is terminated -> Can be disabled
- Can be detached & re-attached.
- 💡 Cheaper than [EFS (Elastic File System)](#efs-elastic-file-system)
- Portable
  - E.g. if one EC2 fails, you can detach volume and attach it on another instance
- Have a provisioned capacity & get billed for all of it (does not better how much you use)
- EBS Volumes are characterized in size, throughput, IOPS (I/O ops per second)
- Throughput is a good metric for HDD (large files, infrequent read/writes) and IOPS for SSD (smaller files, many read & writes)
  - **Throughput**: number of bits read or written per second.
  - **IOPS**: Number of read write operations *(of clusters, not files)* per second
- ❗ It's locked to an Availability Zone (AZ)
  - Must be in same AZ as EC2
  - 📝 To move, you need to first snapshot it:
    1. Create a snapshot
    2. Copy to another AZ
    3. Create a volume in that AZ
- **Pricing**
  - You still pay for unattached EBS instances
    - GB per month price is applicable
- **Monitoring**
  - 💡 Good metric: "queue depth"
    - The queue depth is the number of pending I/O requests (from application to volume)
    - The lower the queue length is the more IOPS you maintain
    - The higher the queue length is the higher throughput you maintain
- **Amazon EBS–Optimized instances**
  - Uses optimized configuration stack and provides additional & dedicated capacity for EBS I/O
  - 💡 Recommended to increase storage performance of an instance.

#### EBS raiding

- Software-based not managed by AWS
- **RAID 0**
  - **"Split"** gives you higher performance than single EBS.
  - ❗ Risky, if one volume fails, entire data is corrupted but high performance.
    - E.g. 1000 GB = 500 GB + 500 GB
      - Free space is 1000 GB
      - Provides IOPS of "two" 500 GB volumes improving the performance
- **RAID 1**
  - **"Mirror"** of your data for extra redundancy
  - 💡 Provides high availability/redundancy.
  - ❗ Full space is not usable,
  - E.g. 1000 GB = 500 GB + 500 GB
    - Free space is 500GB, the half of total amount.
    - But has durability of "two" 500 GB volumes as data is replicated in two volumes
- **RAID 10**
  - Also known as **RAID 1/0**
  - Array ***"split"*** and **"mirror"** combined.
  - 💡 Combination of RAID 0 and RAID 1, gives more performance and high availability.
  - ❗ Minimum of 4 disks are required and therefore costlier than other RAID configurations

#### EBS Volume Types

- **Chart**

  | Attribute / API name | `gp2` | `io1` | `st1` | `sc1` |
  |----------:|-----|------|------|------|
  | **Name** | EBS General Purpose SSD  | Provisioned IOPS SSD | Throughput Optimized HDD | Cold HDD |
  | **Summary** | General purpose | Highest performance SSD | Low cost HDD | Lowest cost HDD |
  | **Description** | 📝Balances price and performance | For mission-critical low-latency or high-throughput workloads | For frequently accessed, throughput-intensive workloads | Designed for less frequently accessed workloads |
  | **Storage** | 1 GiB - 16 TiB | 4 GiB - 16 TiB | 500 GiB - 16 TiB | 500 GiB - 16 TiB |
  | **Bursting** | ✔ Small `gp2` volumes can burst IOPS to 3000 | ✔ Provisioned (PIOS) | ✔ Max throughput of 500 MiB/s - can burst | ✔ Max throughput of 250 MiB/s - can burst |
  | **Max throughput** | 250 MiB/s | 1,000 MiB/s | 500 MiB/s | 250 MiB/s |
  | **Max IOPS** | 16000 (5,334 GB) | Provisioned! min 100, max 32000 (64000 for nitro instances) | 500 | 250 |
  | **Use-cases** | Most workloads e.g. system boot volumes, virtual desktops, low-latency interactive apps, deployment & test environments | More IOPS than `gp2` limit, large databases | Streaming workloads requiring fast & consistent throughput at a low price e.g. big data, data warehouses, log processing, apache Kafka |
- ❗ HDD disks (`st1` and `sc1`) cannot be boot volume i.e. only SSD (`gp2` & `io1`) can be boot volumes
- **SSD vs HDD**

  | Features | SSD | HDD |
  | -------- | --- | --- |
  | Best for workloads with | **small, random** I/O operations | **large, sequential** I/O operations | Can be used as a bootable volume ? | Yes | No |
  | Use-cases | Transactional workloads: sustained IOPS, large databases | Large streaming workloads: Big data, data warehouses, log processing, **infrequently** accessed data |
  | Cost | moderate / high | low |
  | Dominant performance attribute | IOPS | Throughput MiB/s |
- ❗ Max 300 TiB of aggregate PIOPS per region (provisioned IOPS for `io1`)

#### EBS Availability

- Stored in multiple physical volumes in the same AZ at no cost.
- Seamless backups can be done by creating
  - [Snapshots](#ebs-snapshots)
  - [AMIs](./4.1.%20Compute%20-%20EC2.md#ec2-amis) (VM images)
    - Snapshot + some metadata to create another instance
  - [RAID 1 configuration](#ebs-raiding) (done manually)
  - 💡 Use snapshots or RAID 1 instead for seamless backups.

##### EBS Snapshots

- Asynchronous
  - Can be done when instance is running.
- Incremental backups e.g. latest snapshot + changed delta are saved.
  - 💡 Just maintain a single snapshot of the EBS volume since the latest snapshot is both incremental and complete.
- Can be encrypted, and encrypted automatically if it's from an encrypted disk.
- ❗ Performance may drop while snapshot is in progress
  - Because snapshot process use IO.
  - ❗ Shouldn't run while your application is handling a lot of traffic.
  - 💡 Recommended to detach root volume to do snapshot but not necessary.
- **Share snapshot with other accounts**
  - Modify the permissions on the encrypted snapshot to share it with the specified account.
  - *(If encrypted)* Share the custom key used to encrypt the volume.
- Constrained to the Region -> 💡 copy to share across region.
- 📝 Stored in S3 (but you won't directly see them)
- ❗ Max 100.000 snapshots per account
- 📝 Can make an [image (AMI)](./4.1.%20Compute%20-%20EC2.md#ec2-amis) from Snapshot for e.g. faster deploy on [ASG (Auto Scaling Group)](./8.3.%20High%20Availability%20-%20Scalability%20(ASG).md#asg-auto-scaling-group).
- 💡 Snapshots can be automated using [Amazon Data Lifecycle Manager](#amazon-data-lifecycle-manager).
- EBS volumes restored by snapshots need to be pre-warmed.
  - In Linux you can use `fio` or `dd` command to read the entire volume.
- 💡 If you want to copy EBS volume fast, restore from a snapshot: the disk will already be formatted and have data!

##### AWS services

- Include
  - [AWS Backup](#aws-backup)
  - [Data Lifecycle](#amazon-data-lifecycle-manager)
- Helps to manage and automate back-ups:
  - They work independently from each other

###### Amazon Data Lifecycle Manager

- Also known as **Data Lifecycle Manager** or **DLM**
- Helps to automate managing EBS snapshots and EBS-backed [AMIs](./4.1.%20Compute%20-%20EC2.md#ec2-amis)
- You create **Lifecycle Policy** to schedule snapshots / deletion of them.
- It's free of charge [1]

[1]: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/snapshot-lifecycle.html "Amazon Data Lifecycle Manager"

###### AWS Backup

- Integrates with EBS and many other services [1]
- Generic service to centralize backup management and monitoring
- Costs based on storage consumed, restored, and transferred in a month [2]

[1]: https://aws.amazon.com/backup/pricing/ "AWS Backup pricing"
[2]: https://docs.aws.amazon.com/prescriptive-guidance/latest/backup-recovery/new-ebs-volume-backups.html "Creating EBS volume backups with AMIs and EBS snapshots"

### Scalability

- You can now increase capacity (GB & IOPS), or change the volume type **while in use**.
  - No down time when resizing but need to repartition in OS to make space available.
- ❗ You can't increase unlimitedly as EC2 has max IOPS limit of 80.000.
- GB and IOPs goes hand in hand
  - For increasing IOPS you need to increase GB
    - You can't have small volume with very high IOPS
    - You get 1 burst IOPS per 3 GB.
  - However `io1` is an exception where you only IOPS & GB.
    - The maximum ratio of provisioned IOPS to requested volume size (in GiB) is 50:1.

### Volume Encryption

- The encryption occurs on the servers that host EC2 instances
  - Provides encryption of data as it moves between EC2 instances and EBS storage
  - i.e. all the data in flight moving between the instance and the volume is encrypted
- As default:
  - All snapshots are encrypted
  - All volumes created from the snapshot
  - Not encrypted (in rest) by default
    - In settings, security can enforce "Always encrypted" per region
- Encryption & decryption are handled transparently
- Encryption has a minimal impact on latency
- EBS Encryption leverages keys from KMS (AES-256)
- A volume can be encrypted during an initial launch or:
  - Use third party tool e.g. BitLocker & handle manually.
  - Create an instance from launching an [image (AMI)](./4.1.%20Compute%20-%20EC2.md#ec2-amis) from an encrypted volume.
- Copying an unencrypted snapshot allows encryption
- Data in transit between an instance and an encrypted volume is also encrypted
- **Snapshots & Encryption**
  - Volumes created from encrypted snapshots are automatically encrypted.
    - Snapshots of encrypted volumes are encrypted
  - Volumes that are created from unencrypted snapshots are automatically unencrypted
  - The encryption happens during snapshot process.
- ❗ No direct way to encrypt an existing unencrypted volume or snapshot
  - You can copy a snapshot & create new volume to encrypt existing unencrypted.
  - ***Encrypt an unencrypted EBS volume***
    1. Create an EBS snapshot of the volume
    2. Encrypt the EBS snapshot (using copy)
    3. Create new EBS volume from the snapshot (The volume will also be encrypted)
    4. You can now attach the encrypted volume to the original instance
# Amazon S3

- Key / value store for objects
  - 💡 Great for big objects, not so great for small objects (latency)
  - It's object storage
    - Object => You rewrite entire object
    - Block store => You can just send changes to change the blob, can update incrementally as data is split into chunks.
- S3 is a global service (not region specific)
  - ❗ A bucket however is attached to a region
- Supports IPv6, IPv4.
- 💡 Use cases: Static files, Key value store for big files e.g. movies, Website hosting
- ***Objects*** (files) are stored in ***buckets*** (directories)
- **Buckets**
  - ❗ Bucket names must be unique across all existing bucket names in Amazon S3.
  - Buckets are defined at the region level and must have globally unique name as it'll following URLs
    - `https://s3-<region>.amazonaws.com/<bucket>/<object>`
    - `https://<bucket>.s3.amazonaws.com/<object>` (virtual host style addressing)
  - You can set default encryption
    - Options are: *None*, *AES-256* (SSE-S3) and *AWS-KMS* (SSE-KMS)
    - 📝Popular question: How to ensure that all objects can be encrypted at upload?
  - ❗ 100 S3 buckets per account -> soft limit
  - **Folders**
    - Can be created under buckets
    - ❗ They are pseudo and will be treated as object by S3
      - S3 has a flat structure with no hierarchy
      - But presentation in console gives the sense of "Directories"
  - **Version Control**
    - Can enable versioning at bucket level
      - ❗📝 Once enabled, it cannot be disabled but just suspended.
    - Same key overwrite will increment the version
    - Best practice as it allows to
      - Roll back to a previous version
      - Protection against **accidental deletion** i.e. unintended deletes.
        - Deleting files does not really delete versioned files
        - It only puts **Delete marker** and files can still be downloaded.
        - Deleting a delete marker -> Restores the object & adds a new delete marker.
          - ❗ Only the owner of a bucket can permanently remove a delete marker e.g. delete a version.
        - 💡 Best practice: Allow only specific users to delete & enforce MFA
    - 📝Any file that is not versioned prior to enabling versioning will have the version `null`.
- **Objects**
  - Have a **key** which is FULL path
    - e.g. `<my_bucket>/my_file.txt` or `<my_bucket>/my_folder1_another_folder/my_file.txt`
    - ❗ The are no concept of directories within buckets just key names with slashes.
  - ***Object values*** are the contents of the file
    - ❗ Max size is 5 TB, min size is 0 bytes (touched)
    - ❗ If file is more than 5 GB, you need to use multi part upload
    - ***Metadata*** is list of text key/value pairs.
      - Can be system or user metadata.
      - ❗ Cannot be modified after creation
  - ***Tags*** are unicode key/value pairs.
    - ❗ Up to 10
    - 💡 Useful for security / lifecycle / replication rules
- **Logging**
  - Object-level logging
  - Can enable CloudWatch request metrics: e.g. total number of HTTP GET/PUT, 4XX etc.
  - Can enable CloudTrail for auditing API calls and call stacks (not CloudWatch).
- 📝**S3 Consistency Model**
  - Read after write consistency for PUTS of **new objects**
    - E.g. `PUT 200` -> `GET 200`
    - ❗ Eventually consistent if you try to `GET` before to see if the object existed
      - E.g. `GET 404` -> `PUT 200` -> `GET 404` -> GET 404 gets cached
  - Eventual consistency for `DELETE`s and `PUTS` of **existing objects**
    - Read after updating object -> Might get an older version
      - E.g. `PUT 200` -> `PUT 200` -> `GET 200` (might be older version)
    - Delete -> Can retrieve it for a short time
      - E.g. `DELETE 200` -> `GET 200`
- **Make an object publicly accessible**
  - On object level
    - Set a predefined grant (also known as a canned ACL) for a bucket in Amazon S3.
      - In Console -> Bucket -> Permissions -> Public access settings -> Untick *"Block new public bucket policies"* and *"Block public and cross-account access if the bucket has public policies"*
    - Update the object's ACL -> Ensure your object is set to be publicly accessible.
  - On bucket level
    - Use a bucket policy that grants public read access to a specific object tag/prefix
- **Lifecycle Policies**
  - Set of rules to automating moving data between different tiers, to save storage cost.
  - **Transition actions**
    - When objects are transitioned to another storage class
    - E.g. General Purpose *--(60 days)-->* Infrequent Access *--(180 days)-->* glacier
    - ❗ Objects must be stored at least 30 days in standard for standard to => Standard IA or OneZone IA
  - **Expiration actions**
    - Expire & delete an object after a certain time period.
    - E.g. access log files can be set to delete after specified period of time
  - You can combine transition & expiration actions.
  - You can filter the resources by a key name prefix, e.g. `tax/` will only include `tax/doc1.txt`.
  - You can configure it to
    - Clean-up expired object delete markers
    - Affect "Current version" and & or "Previous version"
- **Multipart Upload**
  - Use if file is more than >100MB (required for >=5GB)
  - Allows uploading parts concurrently
    - Improved throughput
    - Quick recovery from any network failures
    - Pause and resume object uploads
    - Begin an upload before you know the final object size.
  - All storage that any parts the aborted multipart upload consumed is freed & deleted.
    - S3 supports a bucket lifecycle rule to abort multipart upload that don't complete within a specified number of days.
- **Event Notifications**
  - **Events**
    - Object created, object removed, object restored, object lost (of a [Reduced Redundancy Storage (RRS) storage](#s3-storage-classes))
  - **Destinations**
    - Determines where events will be published
    - Can be [SNS](./10.1.2.%20Integrations%20-%20Queues%20-%20SNS.md), [SQS](./10.1.1.%20Integrations%20-%20Queues%20-%20SQS.md) and [Lambda](./4.4.%20Compute%20-%20Lambda.md)

## S3 Pricing

- Charged mainly based on
  - Storage (per GB)
    - See [S3 storage classes](#s3-storage-classes)
  - Requests
    - Fee for each request make to S3 APIs for managing objects.
      - 💡 Usually very cheap, but can be a good idea to minimize the requests to optimize costs.
    - See also [Glacier](#glacier) for Glacier retrieval fees.
  - [Data transfer](./1.%20Introduction.md#data-transfer-charges)
  - Optional fees for • region replication • transfer acceleration • management • analytics
- **Requester pays**
  - Requester pays for requests + storage in bucket instead of bucket owner.
  - Requester is from AWS (not anonymous) & sends `requester-pays` flag and billed by AWS.
- See [S3 pricing page](https://aws.amazon.com/s3/pricing/) and [AWS Pricing Calculator](./1.%20Introduction.md#aws-pricing-calculator)

## S3 Storage Classes

- Storage class is set based on object
- **S3 Storage Tiers**

  | Storage | Description | Use-cases | Retrieval latency | Cost |
  | ------- | ----------- | --------- | ----------------- | ---- |
  | **Standard** | General purpose | Big Data analytics, mobile & gaming applications, content distribution... | ms | 💲💲💲💲💲💲 |
  | **Standard IA** | Infrequent access | For data that's less frequently accessed but requires rapid access e.g. you'll get a file directly every month. | ms | 💲💲💲💲💲 |
  | **One Zone-Infrequent Access** | Low latency & high throughput performance | Storing secondary backup copies of on-premise data or storing data you can recreate over time | ms | 💲💲💲💲 |
  | **Reduced Redundancy Storage (RRS)** | Deprecated, don't use it, use one zone-infrequent access | Noncritical, reproducible data at lower levels of redundancy than Amazon S3's standard storage | ms | 💲💲💲 |
  | **Intelligent Tiering** | ML moves objects between *IA* and *Standard* tiers based on changing access patterns | Unpredictable workloads, Changing access patterns, Lack of experience with storage optimization | ms | *(small)* Monitoring + auto-tiering fee |
  | **Glacier** | Low cost cold storage. | DR Back-ups, Media Asset Archival | Expedited: 1-5 min, Standard: 3-5 hr, Bulk: 5 -12 hr | 💲💲 + retrieval fee |
  | **Glacier Deep Archive** | Lowest cost coldest storage |  Magnetic tapes / VTL / Regulatory archiving  | 12-48 hours | 💲 + retrieval fee |
- **S3 Storage Tiers Reliability**

  | | Standard | Reduced Redundancy Storage | Standard Infrequent Access | One - Infrequent Access | S3 Intelligent Tiering | Glacier |
  |--|--|--|--|--|--|--|
  | **Durability** | 99.999999999% | 99.99% | 99.999999999% | 99.999999999% | 99.999999999% | 99.999999999% |
  | **Availability** | 99.99% | 99.99% | 99.99% | 99.5% | 99.9% | No SLA |
  | **AZ** | ≥3 | ≥2 | ≥3 | 1 | ≥3 | ≥3 |
  | **Concurrent facility fault tolerance** | 2 | 1 | 2 | 0 | 1 | 1 |

### Glacier

- Glacier classes moves objects to **Amazon Glacier** which is part of S3.
- **Concepts**
  - **Archive**: store data in (one or more files) with unique archive ID.
    - 📝The content of the archive is immutable, meaning that after an archive is created it cannot be updated.
  - **Vault**: collection of archives where you upload data to.
- **Retrieval**
  - ❗ S3 object stored through Glacier can only be retrieved with S3 APIs as it'll map object path to archive id.
  - **Options**

    | Attribute | Expedited | Standard | Bulk |
    | --------- | --------- | -------- | ---- |
    | Data | Subset of archives | Any archive | Large amounts |
    | Time | 1 - 5 minutes | 3 - 5 hours | 5 -12 hours |
    | Pricing | 💲💲💲💲 | 💲💲 | 💲 |
    - **Expedited**: Access subset of archives in 1-5 minutes.
      - **On-demand**: Accepted except for rare high load situations.
      - **Provisioned**: Buy capacity to ensure that your retrieval capacity is available.
    - **Standard**: Access any archive in 3-5 hours.
    - **Bulk**: Access large amounts in 5-12 hours
  - ❗ Deep Archive has only one option for retrieval time around 12-48 hours.
- ❗ You cannot upload directly from console, possible only through AWS CLI.

## Availability

- **Cross region replication (CCR)**
  - Copying is asynchronous replication
  - On bucket level (entire bucket) or object level (based on prefix/tags)
  - ❗📝 Must enable versioning (source and destination)
    - CRR is built on top of versioning functionality.
    - 🤗 As the replication is asynchronous, it needs 'non-changing' copy of data during replication
      - Versioning makes it easy so file can still be modified (upload, delete, etc.) while replication is in progress.
  - Buckets must be in different AWS regions & can be in different accounts
  - Requires IAM permissions to S3
  - Delete markers & deleting individual versions / delete markers are not replicated.
  - **Use cases**: compliance, lower latency access, replication across accounts
  - Flow:
    1. Create a bucket, and one more new one as replica
    2. Original bucket -> Management -> Replication -> Add rule
       - Apply to *entire bucket* or *"prefix or tags"*
    3. Choose if objects encrypted with AWS KMS will also be encrypted
       - Other account must also have access to AWS KMS as well
    4. For replicated objects you can change
       - Storage class
       - Ownership to destination bucket owner
    5. Set IAM rule
       - Role gets created automatically that gives replication permissions to the original bucket
    - ❗ Does not replicate:
      - Existing files are not replicated, just new files but with same metadata and ACLs.
      - Replicas of other objects, lifecycle actions and configurations, objects without bucket owner permissions, SSE-C & SSE-KMS encrypted object (can be configured to replicate)...

## S3 Websites

- S3 can host static websites and have them accessible on the www.
  - 📝 The website URL will be => bucket name + `s3-website` + region + `amazonaws.com`.
    - `<bucket-name>.s3-website-<AWS-region>.amazonaws.com`
    - `<bucket-name>.s3-website.<AWS-region>.amazonaws.com`
- If you get 403 (Forbidden) error; make sure bucket policy allows public reads!
- Supports redirects through a metadata tag.
- In bucket -> Properties -> Static website hosting
  - Select `Use this bucket to host a website` (optionally specify default index and error files)
  - Another option is to redirect requests (to another place)
  - **S3 CORS** (CORS=Cross Origin Resource Sharing)
    - 📝 If you request data from another S3 bucket, you need to enable CORS
    - Allows you to limit the number of websites that can request your files in S3 and this way limit costs.
    - E.g. `bucket1` has image in `bucket2`. `bucket2` must then has CORS headers for `bucket1`.
- **Prerequisites**
  - ❗ The bucket must have the same name as your domain or subdomain.
  - *(Optionally)* Use CloudFront for SSL/TLS
  - A registered domain name.
  - Route 53 as DNS service for the domain to configure DNS.
# S3 Security

- Bucket owner = root account owning the e-mail address.
- It supports Gateway VPC endpoints to be accessed in VPC without internet access
  - Gateway VPC Endpoint = an endpoint network interface (ENI)
- **Object / Bucket Permissions**
  - **User permissions**: *User ID* has Read / Write access to *Bucket* / *Object*?
  - **Public permissions**: Public read access to bucket or not
  - **System permissions**: Grant Amazon S3 Log Delivery group write access to the group
- **MFA Delete**
  - MFA (multi factor authentication) forces user to generate a code on device (mobile phone or hardware) before doing important operations on S3.
  - To use MFA-Delete, enable ***Versioning*** on the S3 bucket.
  - 💡 You will need MFA to
    - permanently delete an object version
    - suspend versioning on the bucket
  - 💡 Give only right users permissions for delete.
  - ❗ Only the bucket owner (root account) can enable/disable MFA-delete.
  - ❗ MFA-delete currently can only be enabled using the CLI
  - ❗ Affects only permanent deletes not delete markers
  - ***Flow***: Create bucket with versioning -> Log-into root account -> Link to MFA Device (IAM -> Security Credentials) -> Generate root access keys -> Connect to CLI -> Set `MFADelete=enabled` with CLI command
- **S3 Pre-signed URLs**
  - 📝 Allows granting access (URL) to one or more users for a certain amount and time and expire it.
    - URL can be used for `GET` (read) or `PUT` (upload).
    - Can generate pre-signed URLs using SDK (uploads as it's harder) or CLI
    - Valid for a default of 3600 seconds, can change timeout with `--expires-in <time-by-seconds>` argument
  - Users given a pre-signed URL inherit the permissions of the person who generated the URL for GET / PUT
  - E.g. premium video service for logged in users where they can view & list & upload temporarily.
  - Command: `presign <S3URI> --expires-in <time-by-seconds> --region <region-of-object>`
    - 💡 You can run `aws configure set default.s3.signature_version s3v4` for making URL to be compatible with KMS encrypted object before signing

## Logging and Auditing

- **Logging and Audit**
  - **CloudTrail**
    - Logs bucket-level API calls
    - Logs S3 object-level API activity e.g. `GetObject`, `DeleteObject`, and `PutObject API` operations.
    - Also called **CloudTrail Data Events**
  - **S3 access logs**
    - Logs object-level API activity
    - ***Flow***: Go to properties -> Turn on *Server Access Logging* -> Choose bucket
  - **Access logs vs Object-Level CloudTrail logs**

    | | Server Access Logging | Object-Level Logging |
    | -- | --------------------- | -------------------- |
    | **Complexity** | Low | High |
    | **Format** | Loosely-structured, space-delimited text records | JSON |
    | **Delivery** | Timeliness & delivery are not guaranteed | API calls within 15 min are a captured and sent within 5 min |
    | **Filtering** | No filtering | Cloud Trail (basic), Cloud Watch (advanced, e.g. regex) |
    | **Integrations** | S3 | S3, CloudTrail, CloudWatch |
    | **Costs** | S3 | S3, CloudTrail, CloudWatch |
  - ❗ Do not store your own access logs in the same bucket, otherwise; recursion
  - Data can be analyzed using data analysis tools
    - Also **Amazon Athena** and **S3 Select** can be used to query access logs
  - You can set prefixes for the logs

## Policies

- **User based policies**
  - **IAM policies**
  - which API calls should be allowed for specific user from IAM console
  - 💡 IAM policies instead of S3 bucket policies are in general more operationally efficient.
    - You need to control access to AWS services other than S3
      - Easier to manage everything from IAM instead of spreading
    - You have numerous S3 buckets each with different permissions requirements
      - Easier to manage with a few IAM policies instead of policy per bucket.
- **Resource based policies**
  - 📝 **Bucket policies**
    - Allows cross account
    - JSON based policy (can be generated from Policy Generator)
      - `Resources`: buckets or objects
      - `Actions`: Set of API to Allow or Deny
      - `Effect`: Allow / Deny
      - `Principal`: The account or user to apply the policy to
    - 💡 Use-cases
      - **📝Force objects to be encrypted at upload**
        1. Save statements in *Bucket Policy*:
           1. Create bucket policy -> ***Deny*** if any ***principal*** ( `*` ) uses ***action*** `PutObject` has ***condition*** header `x-amz-server-side-encryption` *EqualsTo* `null` is `true`.
           2. Create bucket policy -> ***Deny*** if any ***principal*** ( `*` ) uses ***action*** `PutObject` has ***condition*** header `x-amz-server-side-encryption` *StringNotEquals* `AES256`
        2. Set *Default Encryption* as *Amazon S3 master-key* to set the right header when uploading from console.
      - Grant access to another account (Cross Account)
      - Grant public access to the bucket
    - **Syntax for including root and its endpoints**
      - ❗ Careful: In policies if you use `arn` *(amazon resource name)* with only `/` e.g.it'll not apply policy to all endpoints under it you need to define it as with `/*`.
        - E.g. `arn:awss3::::thebucketname/*` instead of `arn:awss3::::thebucketname/`.
        - URL with `/*` will not include the root URL, if you need root as well use both `/` and `/*`.
  - **Object Access Control List (ACL)**
    - Access policy option to grant basic read/write permissions to other AWS accounts on object level.
      - E.g. account X can *list objects*, *write objects*, *read bucket permissions*, *write bucket permissions*
  - **Bucket Access Control List (ACL)**: Less common
  - **Glacier**
    - Can have:
      - Single resource-based vault access policy
      - Single **Vault Lock policy**
        - For compliance e.g. can deny users permissions to delete an archive until the archive has existed for one year.
        - 💡 After the vault is locked it cannot be unlocked

## Encryption

- **Encryption at rest**
  - S3 Glacier encrypts your data at rest by default.
  - 4 methods of encrypting objects:
    1. **SSE-S3** (Server Side Encryption - S3)
       - Encrypts S3 objects using keys handled & managed by AWS
       - Object is encrypted server side
       - AES-256
         - 📝 You must set header `"x-amz-server-side-encryption":"AES256"`
       - S3 provides ***S3 Managed Data Key*** as encryption key
         - Also called Amazon S3 master-key
       - Works with HTTP + HTTPS
    2. **SSE-KMS** (Server Side Encryption - Key Management Service)
       - Leverage AWS Key Management Service to manage encryption
       - KMS advantages: user control (over rotation of key) + audit trail (how the key was used)
       - Object is encrypted server side
       - 📝 Must set header `"x-amz-server-side-encryption":"aws:kms"`
       - KMS generates & uses CMK (***Customer Master Key***) as encryption key.
         - Also called AWS KMS master-key
         - You can select a key from KMS
       - Works with HTTP + HTTPS
       - 💡 Maintain control of the rotation policy for the encryption keys, but not know the encryption keys values
    3. **SSE-C** (Server Side Encryption - Customer)
       - When you want to fully manage your own encryption keys
       - ❗ HTTPS must be used (no HTTP)
       - Encryption key must be provided in HTTP headers for every HTTP request made
       - Uses client side data key
       - Amazon does the encryption & throws every key and returns data.
       - 💡 Recommended over client side encryption if you need to ensure that encryption is always used.
    4. **Client Side Encryption**
       - Client library such as *Amazon S3 Encryption Client* makes it easier.
       - Clients encrypt & decrypt data themselves
       - Clients fully manages the keys and the encryption cycle
       - Works with HTTP + HTTPS
  - Responsibilities

    | Encryption type | Who en/decrypts the data | Who stores the secret | Who manages the secret |
    |---:|:------------------------:|:---------------------:|:----------------------:|
    | **SSE-S3** | AWS | AWS | AWS |
    | **SSE-KMS** (AWS managed CMK) | AWS | AWS | AWS |
    | **SSE-KMS** (customer managed CMK) | AWS  | AWS | you |
    | **SSE-C** | AWS | you | you |
    | **AWS SDK + KMS** (AWS managed CMK) | you | AWS | AWS |
    | **AWS SDK + KMS** (customer managed KMS) | you | AWS | you |
    | **AWS SDK + self-managed secret** | you | you | you |
  - **SDK**
    - AWS SDK supports for Amazon S3 Client-Side Encryption
    - **AWS Encryption SDK** is an encryption library that is separate from the language–specific SDKs.
    - Difference between AWS SDK (in language-specific AWS SDKs) vs Encryption SDK
      - Encryption SDK is not tied to Amazon S3 and can be used to encrypt or decrypt data to be stored anywhere.
      - Good for enforcing best practices in organization.
- **Encryption in transit (SSL)**
  - 📝Achieved by SSL/TLS
  - S3 exposes HTTP and HTTPS URL, HTTP has encryption in flight.
- **Default Encryption**
  - Encryption is per object level, but you might want to enforce encryption of all objects.
  - ***Old way***: Use a bucket policy and refuse any HTTP command without the proper headers
  - ***New way***: Enable *Default Encryption* setting on S3 by ticking.
    - Bucket policies are evaluated before *default encryption*
    - You can choose between SSE-S3 (AES-256) or SSE-KMS (AWS-KMS)
# S3 Integrated Services

## S3 Select

- Pull out only the subset of data you need from an object by using simple SQL expressions
- Improve the performance by 400% and reduce the cost of applications that need to access data in S3.

## Snowball

- Peta-byte scale data transport solution
- Physical data transport between 80-50 TB to/from S3.
- Secure tamper resistant, use KMS 256 bit encryption
- Tracking using SNS and text messages. E-ink shipping label
- Pay per data transfer job
- ***Use cases***: large data cloud migrations, DC decommissions, disaster recovery
  - 💡 Use it if data takes longer than a week to upload e.g. > 2TB for 45 Mbps (T3, 269 days), >5TB 100 Mbps (120 days), >60TB 1000 Mbps (12 days).
- Flow
  1. Request snowball devices from the AWS console for delivery
     - AWS Console -> Snowball service -> Plan your job (Import into S3, Export from S3, Local compute and storage only) -> Give shipping information & choose shipping speed ->  Choose snowball or snowball edge variant you want -> Choose storage -> Load EC2 EMI -> IAM Role for Snowball -> Select AWS KMS Encryption key -> Set notification (SNS) & notification topics
  2. Install the snowball client on your servers
  3. Connect the snowball to your servers using ID and manifest file from Console and copy files using the client
  4. Ship back the device when you're done (goes to the right AWS facility)
  5. Data will be loaded into an S3 bucket
  6. Snowball is completely wiped
  7. Tracking is done using SNS, text messages and the AWS console
- **Snowball Edge**
  - Snowball Edge add computational capability (e.g. lambda) to the device
    - You can use as *local storage only* if e.g. your region doesn't have lambda or for durability & capacity.
  - 100 TB capacity with either:
    - Storage optimized - 24 vCPU
    - Compute optimized - 52 vCPU & optional GPU
  - Supports
    - A custom EC2 AMI so you can perform processing on the go
    - Custom Lambda functions
  - Very useful to pre-process the data while moving
  - ***Use cases***: data migration, image collation, IoT capture, machine learning
- **Snowmobile**
  - Exabyte scale data transfer service - 45 foot long container truck
  - Transfer exabytes of data (1 EB = 1,000 PB = 1,000,000 TBs)
  - If you have more than 100 TB or PBs data
  - Each Snowmobile has 100 PB of capacity (use multiple in parallel)
  - Better than Snowball if you transfer more than 10 PB e.g. video libraries, image repositories, on-premise migration.
- 💡 Choose based on capacity:
  - 50 TB Snowball has 42 TB usable free space
  - 80 TB Snowball has 72 TB usable free space
  - 100 TB Snowball Edge has 83 TB usable free space

## Storage Gateway

- 📝Allows you to expose S3 to on-premises.
- Bridge between on-premise data and cloud data in S3
- 💡 Use cases: disaster recovery, backup & restore, tiered storage
- ***Flow***
  - Can be installed on Hyper-V and VMware.
  - You then create gateway on Console
- 📝 Three types of Storage Gateway:
  - **File gateway** -> S3
    - Store & update files as objects in S3 asynchronously as you change them.
    - Ownership/permissions and timestamps are stored in S# in the user-metadata of the object.
    - Accessible using the NFS (network file system) and SMB protocol
    - Supports S3 standard, & S3 IA, S3 One Zone IA & Glacier.
    - Bucket access using IAM roles for each File Gateway
    - Most recently used data is cached locally in the file gateway -> Low latency access
    - Can be mounted on many servers
    - 📝 File access / NFS => File Gateway (backed by S3)
  - **Volume gateway** -> EBS
    - Block storage in Amazon S3 with point-in-time backups as compressed Amazon EBS snapshot.
    - Allows you to view S3 bucket as mountable volume
    - Block storage using iSCSI protocol backed by S3
    - Two options:
      - **Cached volumes**: most frequently data are cached on premise on the volumes
      - **Stored volumes**: entire dataset is on premise, scheduled backups to S3 as EBS snapshots
    - 📝 Volumes / Block Storage / iSCSI => Volume gateway (backed by S3 with EBS snapshots)
  - **Tape Gateway** -> Glacier
    - Input: Your existing tape-based processes
    - Output: As virtual tapes (using Virtual Tape Library, i.e. VTL) in S3 or Glacier.
    - Uses iSCSI which is interface & networking standard for linking data storage facilities.
    - 📝 VTL Tape solution / Backup with iSCSI => Tape gateway (backed by S3 and Glacier)
- **Security**: Your data is encrypted at rest by default in S3.
- Flow
  - AWS Console -> Storage Gateway -> Choose gateway type -> Select host platform (VMware / hyper-v / EC2) -> IP address of gateway VM

## Athena

- Serverless service to perform analytics directly against S3 files
- Uses SQL language to query the files
- Has a JDBC / ODBC driver that allows you to connect BI tools to it.
- Charged per query and amount of data scanned
- Supports CSV, JSON, ORC, Avro and Parquet (built on Presto)
- 💡Use cases: Business intelligence / analytics / reporting, analyze & query VPC Flow Logs, ELB Logs, CloudTrail trails, etc..
- 📝 Analyze data directly on S3 -> use Athena
- Usage
  1. Go to Athena on Console
  2. Create database with a query
  3. Create table & link to a bucket
     - It's a link so you don't pay anything to have tables
  4. You can then write SQL queries against the table
- Allows you to easily query encrypted data stored in S3 and write encrypted results back.
  - Both, server-side encryption and client-side encryption are supported
# Networking - IP Addresses

- AWS supports both types of IP's
  - IPv4 e.g. `1.160.10.240`
    - Allows 3.7 billion addresses that's almost running out today.
      - `[0-255].[0-255].[0-255].[0-255]`
  - IPv6 e.g. `3ffe:1900:4545:3:200:f8ff:fe21:67cf`
    - Newer & commonly used for IoT (internet of things) as it solves a lot of problems.
    - 💡 IPv6 are all public addresses (global unicast addresses)
- 💡❗ Use private IP whenever possible: Communication using Public, Elastic IP or Elastic Load Balancer inside EC2 network costs Data Transfer changes even in same AZ.
- **Bring Your Own IP Addresses (BYOIP)**
  - You can bring part or all of your public IPv4 address range from your on-premises network to your AWS account
  - You need to create a ROA (**Route Origin Authorization**)
    - A document that you can create that contains the address range, the ASNs that are allowed to advertise the address range, and an expiration date.

## CIDR (IPv4)

- Classless Inter-Domain Routing
- Used for Security Groups rules, or AWS networking in general
- ❗ Max CIDR size in AWS is `/16`
- CIDR has two components:
  - The base IP (`XX.XX.XX.XX`)
    - The base IP represents an IP contained in the range
  - The subnet mask (`/26`)
    - Defines how many bits can change in the IP
    - Can take two forms:
      1. `255.255.255.0`: Less common
      2. `/24`: More common
    - Basically allows part of the underlying IP to get additional values from the base IP

      | Range | Total Allowed IPs | Calculation |
      |:-----:|:-----------------:|:-----------:|
      | /32 | 1 IP | 2 ^ 0 |
      | /31 | 2 IP | 2 ^ 1 |
      | /30 | 4 IP | 2 ^ 2 |
      | /29 | 8 IP | 2 ^ 3 |
      | /28 | 16 IP | 2 ^ 4 |
      | /27 | 32 IP | 2 ^ 5 |
      | /26 | 64 IP | 2 ^ 6 |
      | /25 | 128 IP | 2 ^ 7 |
      | /24 | 256 IP | 2 ^ 8 |
      | /16  | 65.536 IP | 2 ^ 16 |
      | /0 | all IPs | 2 ^ 32 |
    - 📝 So just know that `32 - CIDR number = X power of 2`
      - E.g `192.168.0.0/24`:
        - `32 - 24 = 8` => `2 ^ 8 = 256` IP addresses
        - Between `192.168.0.0` - `192.168.0.255` (256 IP)
        - Decimal subnet mask = 255.255.255.**0**
      - E.g. `192.168.0.0/16`:
        - `32 - 16 = 16` => `2 ^ 16 = 65.536` IP addresses
        - Between `192.168.0.0` - `192.168.255.255`
        - Decimal subnet mask = 255.255.**0**.**0**
    - 💡📝 Memo
      - Each 8 points from 32 represents which IP number can change totally:
        - /32 - no IP number can change
        - /24 - last IP number can change
        - /16 - last IP two numbers can change
        - /8 - last IP three numbers can change
        - /0 - all IP numbers can change

## Public & Private IP

- **Private vs Public IP (IPv4)**
  - IANA had defined blocks of IPv4 addresses for private (LAN) and public (Internet) access
    - IANA: The Internet Assigned Numbers Authority
  - **Public IP**
    - Machine can be identified on internet (WWW)
    - Must be unique across the whole web (two machines cannot have same public IP)
    - Can be geo-located easily
  - **Private IP**
    - Machine can only be identified on a private network only
    - IP must be unique across the private network.
    - Machines connect to WWW using an ***internet gateway*** (a proxy)
    - Only a specified range of IPs can be used as private IP
      - `10.0.0.0` - `10.255.255.255` (`10.0.0.0/8`) <- in big networks
      - `172.16.0.0` - `172.1.255.255` (`172.16.0.0/12`) <- default AWS one
      - `192.168.0.0` - `192.168.255.255` (`192.168.0.0/16`) <- e.g. home networks
    - AWS registers 5 IP addresses per subnet, see [VPC - Subnets](./6.4.%20Networking%20-%20VPC%20-%20Subnets.md) for more information.
  - **Other reserved ranges**
    - **🤗Trivia** <sup>[StackOverflow](https://stackoverflow.com/questions/42314029/whats-special-about-169-254-169-254-ip-address-for-aws)</sup>
      - `169.254.0.0/16` exists only on the directly connected network.
      - It's a reserved IPv4 Link Local Address.
      - AWS choose its metadata server to make it reachable within this range (169.254.169.254).
    - `127.0.0.0` – `127.255.255.255` is used for addresses to the local host.

- **Elastic IP**
  - Fixed public IPv4 IP address for attaching to EC2 instances (to their Elastic Network Interfaces).
    - Because when you stop & start an EC2 instance, it'll change its public IP.
  - You own it as long as you don't delete it.
  - Can help you mask the failure of an instance by remapping the address to another instance.
  - You can attach it to a one instance at a time
  - ❗ Max 5 Elastic IP per account (soft limit)
  - 💡 Elastic IP reflects often poor architectural decisions
    - Horizontal scaling becomes much harder
    - Instead you can:
      - Use a random public IP and register a DNS name to it.
      - Load balancer (best pattern).
  - **Pricing**
    - Elastic IPs are free as long as
      - They are being used by an instance i.e. attached to instance and instance is running.
        - ❗ Amazon will charge you $0.01 per hour for each EIP that you reserve and do not use.
      - The instance has only one Elastic IP address attached to it.
    - ❗ You will be charged if you ever remap an EIP more than 100 times in a month.
# Route 53

- Route53 is a Managed DNS (Domain Name System)
  - DNS = Collection of rules and records to guide clients to reach a server through URLs.
  - 🤗 DNS is on port 53 that's where the name comes from.
- Route 53 can use both public and private domain names:
  - e.g. `application1.mypublicdomain.com`
  - e.g. `application1.company.internal` (can be resolved by your instances in your VPCs).
- Route 53 has advanced features:
  - **Load balancing** -> through DNS - also called client load balancing
  - **Health checks** -> although limited
  - **Routing policy** -> simple, failover, geolocation, latency, weighted, multi value
- **Hosted Zone**
  - Container that holds information about how you want to route traffic for a domain
    - E.g. `example.com` and its subdomains.
  - Can be public or private zone for public & private domain names.
    - Public hosted zone for public domain names
      - Accessible from www e.g. `application1.mypublicdomain.com`
    - Private domain names for private domain names
      - Gets resolved by instances in VPC e.g. `application1.company.internal`
      - 📝 ❗ If you use custom DNS domain names in a private zone in Route 53, you must set both attributes to true in VPC: `enableDnsSupport`, `enableDnsHostname`.
  - ❗ You need to delete all record sets in order to delete Hosted Zone
- **Pricing**
  - $0.50 per month per hosted zone
  - A domain name (.com) costs 12$ per year
- **TTL (time to live)**
  - Computer caches DNS request for TTL duration
    - E.g. if `domainname.com` points to an IP, computer caches it.
  - Cache is only updated after TTL is expired, computer then talks to Route 53 again.
  - High TTL vs Low TTL
    - High TTL: e.g. 24 hours
      - Less traffic to DNS so it's cheaper
      - Possible change of out-dated DNS
    - Low TTL e.g. 60 seconds -> more traffic to DNS & easy to change records
- You can test DNS queries to see if it resolves right with `nslookup domainname.com` in Windows and `dig domainname.com` in Mac.

## DNS Records

- **DNS Records**
  - **SOA** (Start Of Authority record)
    - Every DNS starts with authority record SOA
    - Stores name of the server that supplied the data for the zone, the administrator of the zone, current version of the data file, default number of seconds for TTL file on resource records.
  - **NS** (Name Server Records)
    - Used by Top Level Domain servers to direct traffic to the Content DNS server which contains the authoritative DNS records.
  - In AWS, the most common records are:

    | Record | Description |
    |:------:|-------------|
    | A     | URL to IPv4 |
    | AAAA  | URL to IPv6 |
    | CNAME | URL to URL |
    | Alias | URL to AWS resource |
  - Other common records are **MX** (e-mail) and **PTR** (reversed A, look up name against IP address) records.
  - DNSSEC is not supported, all supported are: A (address record), AAAA (IPv6 address record), CNAME (canonical name record), CAA (certification authority authorization), MX mail exchange record), NAPTR (name authority pointer record), NS (name server record), PTR (pointer record), SOA (start of authority record), SPF (sender policy framework), SRV (service locator), TXT (text record).

- **How does DNS work?**
  1. ***Query NS***: *Browser* makes DNS request to *Route 53 (DNS Server)* with e.g. `http://myapp.mydomain.com`
     - Top Level Domain Server then returns NS record to the name server.
  2. ***Query DNS***: *Browser* then queries NS server and gets SOA (start of authority) record where all DNS records exists
     - *Route 53 (DNS Server)* sends back IP e.g. `32.45.67.85` (= a record: URL to IP)
  3. ***Request content***: *Browser* makes HTTP Request to *application server* with e.g. `IP: 32.45.67.85` and with header `Host : http://myapp.mydomain.com`.
  4. ***Get content***: *Application server* returns with HTTP response
- **CNAME vs Alias**
  - AWS resources (load balancer, CloudFront, etc..) expose an AWS URL e.g. `lb1-1234.us-east-2.elb.amazonaws.com` and you want it to be `myapp.mydomain.com`
  - Two options
    1. CNAME
       - Points a URL to any other URL (app.mydomain.com => blabla.anything.com)
       - ❗ Only for non root domain (aka something.mydomain.com)
    2. Alias (AWS concept)
       - Points a URL to an AWS resource (app.mydomain.com => blabla.amazonaws.com)
       - 💡 Works for root domain and non root domain (e.g. mydomain.com)
       - Free of charge
       - Native health check
       - Good for pointing to e.g. load balancer / EC2 that'll have IP changing all the time.

## Health Checks

- If an instance is unhealthy, Route 53 will not send traffic to that address.
- It decides if the instance is healthy after running X check (default 3).
- Default **health check interval**: 30s (can set to 10s -> costs more)
- **Health checker**
  - You can customize which regions checkers will come from.
  - About 15 health checkers will check the endpoint health.
    - So if health check interval is 30s, it's checked every 2 secs on average,
- Can have HTTP, TCP and HTTPS health checks (no SSL verification)
- Can be integrated with CloudWatch
- Health checks can be linked to Route53 DNS queries
  - DNS failover -> It'll change behavior of Route 53
- Can have string matching (compare response) to decide if healthy.
- You can monitor:
  - Endpoint (can be IP address or domain name)
  - Status of other health checks (calculated health check)
  - State of CloudWatch alarm
- You can enable **Latency Graph** to track latencies.
- You can set-up SNS notification for failed health-checks.

## Routing policies

- Associated in a DNS record set.

  | Policy | Description | Use cases | Creation | Notes |
  | ------ | ----------- | --------- | -------- | ----- |
  | **Simple routing policy** | One domain to one/more IP/URL | Single instance | One set to target(s) | ❗ You can't attach health checks to simple routing policy • Multiple IP handling is not standard, most clients do round robin • Route 53 retuns in random order |
  | **Weighted routing policy** | Control % of request to which targets | AB testing, split traffic between regions | Record set per endpoint with single weight | Only selected address is calculated returned to the client by Route 53 |
  | **Latency routing policy** | Redirect to least latency target | Low latency requirement | Record set per endpoint & region to the same DNS name. | Not based on region e.g. Germany may be directed to the US (if that's the lowest latency) |
  | **Failover routing policy** | To healthy target(s) | Active-passive failover | 2 record sets, one primary and one secondary for same target | • Secondary is activated when primary fails • Primary must have health check, optional for secondary |
  | **Geolocation routing policy** | Based on user location | E.g. traffic from UK goes to IP X | Record set per location (country, region or default) | 💡 Should create "default" policy if there's no match |
  | **Geoproximity routing policy** | Based on user and resource location | Shift traffic from resources in one location to resources in another | For each role, AWS resource? AWS region, not? latitude and longitude  | • You must use Route 53 traffic flow • Uses configurable biases to route more or less • Bias expands or shrinks zone from where traffic is routed to a resource |
  | **Multi Value routing policy** | Improves *simple routing policy* with health checks | • Traffic to multiple resources • Must have health checks | Record set for each target | • Enables client side load balancing |

## 3rd Party Domains

- **Domain names**
  - Top level: last part e.g. `.gov`
    - IANA [has a database](http://iana.org/domains/root/db) of all available top-level domains
  - Second level *(optional)*: e.g. if it's `.gov.uk` then `.gov` is second level `.uk` is top level domain name.
- Domain name **registrar** is an organization that manages the reservation of Internet domain names under one or more domain names.
  - E.g. GoDaddy, Google Domains, Gandi...
    - You can also buy from AWS with Route 53.
      - It can take up to 3 days for registration to be completed.
  - Registers domains with InterNIC, a service of ICANN which enforces uniqueness of domain names across the internet.
  - Each domain name becomes registered in a central database known as the WhoIS database.
- You can also use 3rd party registrar with Route 53.
  1. Create a public Hosted Zone in Route 53
  2. Update NS records on 3rd party website to use Route 53 ***name servers*** (NS Records).
     - You get NS (name server) records from Hosted Zone & Route 53 that you copy & paste to you registrar.
- ❗ Soft limit of up to 50 domains managed by Route 53.
# VPC

- VPC = Virtual Private Cloud
- ❗ You can have max 5 VPCs in a region - soft limit
- ❗ Your VPC CIDR should not overlap with your other networks (e.g. corporate)
- VPC can be deployed in to shared hardware (default) or dedicated hardware (costly).
- A VPC is multi AZ but deployed into a single region.
- **IP addressing in VPC**
  - Only private IP ranges as it's private.
  - Can add new ranges as IPv6 or IPv4 CIDR after creating the VPC.
  - ❗ Max CIDR per VPC is 5.
  - ❗ For each CIDR:
    - Min size is /28 = 16 IP addresses
    - Max size is /16 = 65536 IP addresses
- **Default VPC** in
  - All new accounts have a default VPC in each region.
  - It comes with
    - Subnet per AZ
    - Internet gateway
    - Network ACL: allows all inbound and outbound traffic
    - Main route table that routes VPC CIDR range to local and `0.0.0.0/0` to Internet gateway.
    - Security group denying inbound from internet, allowing outbound to internet.
  - Instances get public & private IPv4 + hostnames
    - In custom VPC = private IP + hostname, and public depending on settings
- **VPC Components**
  - ![VPC network components](img/networking/networking-components.png)
- **DNS Resolution in VPC**
  - `enableDnsSupport` (DNS Resolution setting)
    - Helps decide if DNS resolution is supported for the VPC
    - If true (default), queries the AWS DNS server at `169.254.169.253`
  - `enableDnsHostname` (DNS Hostname setting)
    - If True, assign public hostname to EC2 instance if it has a public IP
      - Private DNS is always assigned regardless true or false
    - False by default for newly created VPC, True by default for Default VPC
    - Won't do anything unless `enableDnsSupport=true`
  - 📝 Private DNS + Route 53: enable DNS Resolution + DNS hostnames (VPC) DNS domains and their subdomains keeping the resources masked from the Internet.
- ❗ **Multicast** is not supported.
  - Multicasting: one or more sources can transmit network packets to subscribers
  - 💡 You can enable virtual overlay network
    - It's IP level multicast across network fabric unicast IP routing supported by VPC.

## Package security

![Package security](img/networking/package-security.png)

### Network Access Control Lists (NACLs)

- Like firewall which control traffic from and to subnets.
- Deployed into VPC
- NACLs are ***stateless*** filtering rules
  - Stateless because if an ingress traffic is allowed, the response is not automatically allowed unless explicitly allowed in the rule for the subnet.
- Applied at the subnet level and applies to every resource deployed to the subnet
  - A subnet can have only one NACL
  - Same NACL can be applied to multiple subnet
- ***NACL Rules***
  - Rules are numbered and evaluated in order
  - Starting with the lowest numbered rule
    - E.g. `#100 ALLOW <IP>` override `#200 DENY <IP>`
    - Last rule is an asterisk (*) and denies a request in case of no rule match
    - 💡 AWS recommends adding rules by increment of 100 for leaving some places for future rules
- Default NACL allows everything outbound and everything in bound
  - ❗ Newly created NACL will deny everything
- **Ephemeral ports**
  - An ephemeral port is a short-lived transport protocol port for Internet Protocol (IP) communications.
  - Ephemeral ports are allocated automatically from a predefined range by the IP stack software.
  - Allows returning traffic to clients
  - Range varies depending on the client's operating system
    - E.g. Linux 32768 - 61000, Elastic Load Balancing 1024 - 65535, Windows Server 49152 - 65535
    - Different OS and different applications will pick different ports.
  - To cover the different types of client that might initiate traffic to public-facing instances in your VPC, you can open ephemeral ports 1024 - 65535 (NAT Gateway ports) to outbound connections.
    - You should deny malicious ports

### Security Groups

- ***stateful*** e.g. if inbound rule passes, outbound rule passes as well
- Applied at the EC2 instance level
  - More specific: Elastic Network Interface (ENI) level

### Security Groups vs Network Access Control Lists

- Differences

  | Attribute | Security Group | NACL |
  | --------- | -------------- | ---- |
  | Execution | Instance (ENI layer) | Subnet boundary |
  | State | Stateful (one direction rules) | Stateless (must configure both inbound & outbound) |
  | Life scope | VPC (any AZ or Subnet) | Subnet Scoped (must be attached explicitly to subnets) |
  | Applies to | Instance only | All instances in the associated subnets |
  | Rules | Allow rules only | Allow and Deny rules both |
  | Rule order | Evaluate all rules as group | In number order, if hit a match, does not check other rules |
- Reasons to use both:
  - SG have soft limit of 50 rules.
  - More security layers = better, protection against misconfigurations
  - Segregation of responsibilities, Network Admins for NACLs, SGs for Server Admins.

## VPC Peering

- Connect two VPC, privately using AWS network
- Make them behave as if they were in the same network
- ❗ Unsupported VPC Peering Configurations
  - Must not have overlapping CIDR
  - VPC Peering connection is not transitive
    - Must be established for each VPC that need to communicate with one another
    - E.g. if a <-> b and b <-> c it does not mean a <-> c
  - Edge to Edge Routing Through a Gateway or Private Connection
    - E.g. corporate network through Direct Connect, Internet through internet gateway, internet in subnet through NAT, VPC endpoint to an AWS and Ipv6 ClassicLink connection
- You can do ***cross-account*** & ***cross-region*** VPC peering
- You must update route tables in each VPC's subnets to ensure instances can communicate,
- The accepter VPC must "accept" the peering connection,
- Flow in console:
  1. Go to VPC -> Peering connections -> Create ***peering connection***
  2. Define requester & accepter VPC.
  3. Go to accepter VPC -> right click -> accept request
  4. In each VPC, route CIDR range of other VPC to the ***Peering Connection***

## VPC Endpoints

- Allow you to connect to AWS Services using AWS network instead of the public www network.
- They scale horizontally and are redundant
- They remove the need of IGW, NAT etc... to access AWS Services
- PrivateLink = VPC Endpoints
- Two types of endpoints:
  - **Interface**
    - Provisions an ENI (Elastic Network Interface with private IP address) as an entry point
    - Must attach security group
    - Most AWS services
      - Services hosted by other AWS customers and partners in their own VPCs
      - AWS marketplace services
    - You can see the resource but can't manage
    - You have to define which subnets the endpoint will live.
    - Ensure that the attributes `Enable DNS hostnames` and `Enable DNS Support` in VPC are set to true.
  - **Gateway**
    - S3 and DynamoDB only
    - In route table it provisions a target for internal S3/DynamoDB to the VPC endpoint automatically.
    - Ensure resources accessing S3 (e.g. EC2) has IAM role to the resource
- In case of issues:
  - Check DNS Setting Resolution in your VPC
  - Check route tables
  - Ensure you're trying to reach endpoint within same region

## Flow Logs

- ![VPC flow logs](img/networking/vpc-flow-logs.png)
- Capture information about IP traffic going into your interfaces:
  - VPC Flow Logs
  - Subnet Flow Logs
  - Elastic Network Interface Flow Logs
- Can capture from managed services such as ELB, RDS, ElastiCache, Redshift, WorkSpace
- Helps to monitor & trouble shoot connectivity issues & security attacks
  - Can log ACCEPT traffic, REJECT traffic or both.
- Data is sent to either S3 or CloudWatch Logs
  - 📝 You can query VPC flow logs using ***Athena*** on S3 or ***CloudWatch Logs Insights***
- **Flow Log Syntax**
  - `<version>`, `<account-id>`, `<interface-id>`, `<srcaddr>`, `<dstaddr>`, `<srcport>`, `<dstport>`, `<protocol>`, `<packets>`, `<bytes>`, `<start>`, `<end>`, `<action>`, `<log-status>`
    - `<srcaddr>` and `<dstaddr>` help identify problematic IP
    - `<srcport>` and `<dstport>` help identify problematic ports
    - `<action>`: success or failure (accept / reject) of the request due to  Security Group / NACL
    - `<log-status>`: `OK` -> logging normally, `NODATA`: No network traffic to or from during capture window, `SKIPDATA`: Skipped during capture windows because of e.g. capacity constraint or an internal error.
- Can be used for analytics on usage patterns, or malicious behavior
- ❗ You cannot allow it in peered VPCs unless the peer is in your account.
- ❗ Does not monitor: instances when they contact Amazon DNS server, Windows license activation, to and from `169.254.169.254` instance metadata, DHCP, to reserved Id address for the default VPC router.

## Connect & manage private instances

- **Bastion Host** (Jump Boxes)
  - Designed to withstand security attacks and usually hosts only a proxy server
  - Used to administer EC2 (SSH/RDP)
    - Does not replace NAT gateway/instance as they are not used for SSH/RDP but to provide internet traffic to private subnets.
  - The bastion is in the public subnet /(DMZ) which is then connected to all other private subnets
  - 💡 Small EC2 instances is enough as it does not require much processing power.
  - Bastion Host security group must be tightened
    - 📝💡 Make sure the bastion host only has port 22 traffic from the IP you need, not from the security groups of your instances

- **SSM Session Manager**
  - Systems Manager allows you to remotely execute commands on managed hosts without using a bastion host
    - Way to go in future and will replace bastion hosts
    - You don’t have to manage SSH keys.
  - Free & serverless AWS managed service
  - Requires an IAM policy that allows users or roles to execute commands remotely.
  - Agents require an IAM role and policy that allow them to invoke the Systems Manager service.
    - The Systems Manager agent is an open-source executable that runs on supported versions of Linux and Windows
  - Systems Manager immutably logs every executed command
# Subnets

- 📝 A subnet must only belong to one AZ and cannot span AZs.
  - ![VPC and network placement](img/networking/vpc-and-subnet-placement.png)
- ❗ Max allowed CIDR range is `/16`, min is `/28`
- ❗ AWS reserves 5 IP addresses (first 4 and last 1 IP address) in each Subnet
  - These 5 IPs are not available for use and cannot be assigned to an instance
  - E.g. if CIDR block `10.0.0.0/24`, reserved IP are:
    - `10.0.0.0`: Network address
    - `10.0.0.1`: Reserved by AWS
    - `10.0.0.2`: Reserved by AWS for mapping to Amazon-provided DNS
    - `10.0.0.3`: Reserved by AWS for future use
    - `10.0.0.255`: Network broadcast address.
      - AWS does not support broadcast in a VPC but address is reserved.
  - 📝 E.g. if need 29 IP addresses for EC2 instance, you can't choose a Subnet size of /27 (32 IP)
  - Each instance in any subnet is assigned a static private address, i.e. does not change with stopping/starting instances.
- **Route table**
  - Deployed into VPC
  - When you create a VPC, it automatically has a ***main route table***.
  - Each subnet must have a route-table attached to it.
    - They're associated to main route table if they don't have any.
  - Any time when a route destination is down/terminated, the status of the route becomes ***blackhole***.
  - Default route in route table is destined for the IP range of the VPC that allows all subnets within a VPC to communicate with each other.
  - Route rules
    - Defined destinations (where packet wants to go) and targets (where it will go).
    - Priority: most specific range gets selected if overlapping i.e. **longest prefix match**.
  - **Route propagation**: It is the task of your service provider to advertise to the backbone sites that they are the point of connection (and thus the path inward) for your site. This is known as route propagation.
- 🤗 Penetration testing & vulnerability scanning to own VPC is allowed!

## Private Subnet

- Any subnet without the IGW is regarded as private subnet and have no internet connectivity without NAT gateway.
- Bigger in size as you put all your applications etc.
- Can deploy **Egress-Only Internet Gateway (EGW)**
  - Blocks incoming traffic while still allowing outbound traffic
  - ❗ IPv6 only use NAT for IPv4.
    - All IPv6 are public addresses; instances become publicly accessible.
  - ❗ Edit the route tables, after creating; outbound route to destination (`::/0`), target: gateway

## Public Subnet

- Usually much smaller in size and contains load balancers only.
- A subnet is public if it has an internet gateway (IGW) attached.
  - **Internet Gateway**
    - Allows VPC to connect with the internet
      - Route tables must be edited: Destination 0.0.0.0/0 to Internet Gateway
    - It scales horizontally and is HA and redundant
    - ❗📝 One VPC can only be attached to one IGW and vice versa
    - It's **NAT** for instances that have a public IPv4
      - NAT translates the IP addresses of computers in a local network to a single IP address.
    - ❗ Edit the route tables, after creating; outbound route to destination (`0.0.0/0` and `::/0`), target: gateway
- You can enable ***auto-assign public IPv4 address***
  - A new EC2 instance by default get an IP address
  - By default instances do not get public IP if it's not enabled.

### NAT

- The actual role of a NAT device is address and port address translation (PAT
- In AWS, you can use a NAT device to enable instances in a private subnet to connect to the internet (for e.g. for software updates) or other AWS services, but prevent the internet from initiating connections with the instances.
- NAT instances and Gateways must be deployed in public subnet.
- **NAT Instances**
  - Outdated & not recommended use ***NAT Gateway*** instead
  - EC2 instance that allows instances in the private subnets to connect to the internet
    - Amazon Linux AMI pre-configured are available
  - Configurations:
    - 📝 Must disable EC2: Source / Destination Check
      - Keep it enabled for all instances except NAT
      - NAT instance must be able to send and receive traffic when the source or destination is not itself.
    - Must have Elastic IP attached to it
    - Allow HTTP+HTTPS from VPC.
  - Route table must be configured to route traffic from private subnets to NAT instance
  - Not highly available / resilient setup out of the box
    - Would need to create ASG in multi AZ + resilient user-data script
  - Internet traffic bandwidth depends on EC2 instance performance
  - Must manage security groups & rules:
    - Inbound
      - Allow HTTP / HTTPS traffic coming from Private Subnets
      - Allow SSH from your home network (access is provided through Internet Gateway)
    - Outbound
      - Allow HTTP /HTTPS traffic to the internet
- **NAT Gateway**
  - AWS managed NAT, higher bandwidth, better availability, no administration
    - Deployed into public subnet
  - NAT is created in a specific AZ, uses an EIP (Elastic IP)
    - 💡 For high availability, it's good to deploy different NAT gateways in different AZ.
  - Cannot be used by an instance in that subnet (only from other subnets)
  - Requires an IGW (internet gateway): Private Subnet => NAT => GW
  - **Scalability**
    - 5 GBPS of bandwidth
    - ❗ Automatic scaling up to 45 GBPS
  - No security group to manage
  - Pay by the hour for usage and bandwidth
  - **Security**: Use NACL to control traffic to and from the subnet in which your NAT gateway resides.
- **NAT Gateway vs NAT Instance** <sup>[doc](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-comparison.html)</sup>

  | Attribute | NAT Gateway | NAT instance |
  | --------- | ----------- | ------------ |
  | High availability | highly available in each AZ with redundancy | Use a script to manage failover between instances |
  | Bandwidth | Can scale up to 45 Gbps | Depends on bandwidth of the instance type |
  | Maintenance | Managed by AWS | Managed by you |
  | Performance | Software optimized | Generic Linux AMI |
  | Security groups | Cannot be associated (use NACL in its subnet) | can be associated with |

## Gateway cheat sheet

| Type | Subnet accessibility | When |
| ---- | ---------- | ---- |
| Egress only | Private subnet | IPv6 internet in private subnet. |
| NAT Gateway | Private subnet | IPv4 internet in private subnet. |
| Internet Gateway | Public subnet | Once for VPC for internet access in VPC. |
# Hybrid Connections

- ![Hybrid connections](img/networking/hybrid-connections.png)

## Site to site VPN

- Allows customer site (corporate network) to seamlessly communicate with AWS VPN as it's in the same network
- Links *Virtual Private Gateway* from AWS side to *Customer Gateway* from customer side.
- **Virtual Private Gateway (VPG)**
  - Deployed on VPC level (not subnet).
  - Allows AWS to provide connectivity from AWS to other networks via VPN or Direct Connect.
  - AWS requires Customer Gateway (CGW) on the customer side to connect to AWS VPC
  - VPN concentrator on the AWS side of the VPN connection
  - Created and attached to the VPC from which you want to create the Site-to-Site VPN connection
  - Possibility to customize the **ASN**
    - ASN = autonomous system number
    - A unique number that's available globally to identify an autonomous system and which enables that system to exchange exterior routing information with other neighboring autonomous systems.
    - Internet = network of networks
      - Routing is done through cooperation of autonomous systems (AS)
      - Each AS cooperate to route the traffic to destination
      - Each AS does routing within AS, then across AS and when it reaches destination, routing is done by the destination AS
    - Different ISPs communicate through Border Gateway Protocol (BGP)
      - Border Gateway Protocol (BGP) is a standardized exterior gateway protocol designed to exchange routing and reachability information among autonomous systems
- **Customer Gateway**
  - Software application or physical device on customer side of the VPN connection
  - 📝 IP Address:
    - Use static, internet-routable IP address for your customer gateway device
    - If behind a CGW behind NAT, use the public IP address of the NAT instead of the public IP of the gateway
- ❗📝 You need to allow Route propagation on VPC
  - **Route propagation** allows VPC to find your internal site in Site-to-Site connection.
  - Routes representing your Site-to-Site VPN connection automatically appear as propagated routes in your route table.
- ***Flow***
  1. Set-up customer gateway on-premises with your device/software
  2. Create customer gateway in AWS
  3. Set-up Virtual Private Gateway
     - You can choose amazon default ASN (dynamic: BGP ASN) or custom ASN (static)
     - Add private network in route table and enable route propagation.
  4. Create Site-to-Site VPN connection between gateways

## Direct Connect

- Provides a dedicated ***private*** connection from a remote network to your VPC.
  - Uses AWS Direct Connect links (fiber cables) from your Data Center to Direct Connect locations.
- You need to setup a ***Virtual Private Gateway*** on your VPC
- Access public resources (S3) and private (EC2) on same connection
  - Accesses all AZs within a region
- 💡 Use cases:
  - Increase bandwidth throughput
    - E.g. working with large data sets and want lower cost on bandwidth
  - More consistent network experience
    - E.g. you experience shutdown & data loss
    - E.g. you need to have applications using real-time data feeds
  - Hybrid environments (on-prem + cloud)
- Supports both IPv4 and IPv6
- If you want to setup a Direct Connect to one or more VPC in many different regions (same account), you must use a Direct Connect Gateway
- **Direct Connect Gateway**
  - Allows multi region, multi account and multi VPN connections
  - 📝Does not peer, it allows between VPC and customer network
- **VPN as back-up**
  - Use same VPG for both Direct Connect and VPN connection
  - IP configurations
    - For BGP in VPN => Advertise the same prefix for Direct Connect and the VPN.
    - Static VPN => Add the same static prefixes to the VPN connection
    - If same routes advertised: Direct connect path will always be preferred.
# CloudFront

- Content Delivery Network (CDN)
- Improves read performance, content is cached at the edge locations (+136 point of presence globally)
- 📝 Popular with S3 but works with EC2, Load balancing
- Can help protect against network attacks
- You use domain name that CloudFront assigns to your distribution, e.g. cannot attach Elastic IP.
- **Security**
  - Can provide SSL encryption (HTTPS) at the edge using ACM (AWS Certificate Manager)
    - Supports: **Perfect Forward Secrecy** (new SSL key for each session)
  - Can protect against DDoS attacks.
- ***Geo Restriction*** allows you to specify a list of whitelisted or blacklisted countries in your CloudFront distribution.
- A ***distribution*** is a CloudFront instance (collection of edge locations) that can be:
  - ***Web distributions*** -> Web pages
  - 📝 ***RTMP*** -> a video/media (streaming) protocol over SSL
    - For RTMP CloudFront distributions files must be stored in an S3 bucket
- You set up ***origin*** (domain name & path)
  - Origin can be S3, EC2, ELB, or Route53.
  - **Origin Failover**: Set-up primary + secondary origins on selection of 500, 502, 503, 504, 404, or 403.
    - 💡 Enables high availability.
- ***Cache Behavior settings***
  - Can set-up viewer Protocol Policy: HTTP & HTTPS, Redirect HTTP to HTTPS, HTTPS only
  - Can allow HTTP Methods: GET & HEAD & PUT & OPTIONS & PATCH & DELETE.
  - Set minimum maximum & default TTL
  - Forward cookies and/or query strings
  - And more: e.g.  Path Pattern, Compress objects
- ***Distribution Settings*** are • Price Class • AWS WAF Web ACL • Alternate Domain Names (CNAMEs), SQL certificate • Supported HTTP versions, logging (with log prefix & option to include cookies) • Enable IPv6
- 📝You can clear cache objects by creating an ***invalidation*** but you will be charged.
  - 💡 AWS recommends versioned file names instead of invalidating if files are updated frequently.
  - Integrates with **AWS Shield** for DDoS protection.
  - Integrates with **AWS WAF** for application layer security.
- **Regional Edge Cache**: If data is infrequently accessed, instead of CloudFront sends request back to your origin, it caches your data in a regional edge caches and gets from there (faster).
- 💡 Use cases
  - Accelerate uploads -> Users can upload to edge locations
  - Can stream videos from edge locations
  - Used in general to accelerate other services e.g. S3 for e.g. faster upload.
  - Allows Lambda with Lambda@Edge to run in edge locations.
  - Integrates with API gateway to run on edge locations.
  - Use with any text, blob (e.g. `.pdf`)
- `Cache-Control` and `Expires` headers control how long objects stay in the cache.
  - `Cache-Control max-age` lets you specify how long (in seconds) you want an object to remain in the cache before CloudFront gets the object again from the origin server.
    - Minimum 0, Max 3600

## S3 & CloudFront

- Allows to directly upload to and read from edge locations.
- **Transfer acceleration**
  - In S3 enables faster transfers through routing from CloudFront's edge locations.
  - 💡 Costs extra but not always fastest, test it.
  - Supports multipart uploads.
- **CloudFront vs S3 Cross Region Replication**

  | Attribute | CloudFront | S3 Cross Region Replication |
  | --------- | ---------- | --------------------------- |
  | Cache location | Global edge network | Each unique region you set up for replication |
  | Cache duration | TTL | Updated in near real time |
  | Can write? | Yes, can put object | Read-only |
  | Use case | Static content that must be available everywhere | Dynamic content that needs to available at low-latency in few regions |
- 📝You can enable **Restrict Bucket Access**
  - Requires users to always access S3 content using CloudFront URLs, not S3 URLs
  - Grants permissions to *Origin Access Identity* for `GetObject`.
    - ***Origin Access Identity*** is Identity for CloudFront distribution
- **Origin Access Identity**
  - More secure: Clients must go through CloudFront, cannot go directly to S3
  - Serving static content, globally
    - Client **<--** CloudFront **-->** Amazon S3
  - Serving static content, globally, **securely**
    - Client **<--** CloudFront *(OAI: Origin Access Identity)* **<-->** Amazon S3 *(bucket policy + only authorize from OAI)*
- 📝**CloudFront Signed URL / Signed Cookies**
  - It's commonly use to give access to paid content
    - E.g. you want to distribute paid shared content to premium users over the world & content lives in S3.
    - ❗ If S3 can only be accessed through CloudFront, self-signed S3 URLs cannot be used.
      - 💡 We can use CloudFront Signed URL.
  - Security: e.g. **IP restriction**
  - ❗ Can only be created using the AWS SDK
  - Flow:
    - Attach a policy with
      - URL expiration
        - 💡 URL invalidation best practices
          - Shared content (movie, music): make it short e.g. a few minutes
          - Private content (private to the user): make it last for years.
      - IP ranges to access the data from
      - Trusted signers (which AWS accounts can create signed URLs)
    - You need to write an application:
      - *Client* gets *signed URL* from *application* where *application* talks to *Amazon CloudFront* using SDK to generate a signed URL
  - **Example architecture**: API Gateway **-->** Lambda *(Creates signed URL & verifies if user is premium with DynamoDB)* **-->** CloudFront *(issues signed URL)* **<---** OAI: Origin Access Identity **-->** S3 (serves videos + authorizes only from OAI with a bucket policy)

## API Gateway & CloudFront

- CloudFront in front is generally not a good idea.
  - Most of the functionality in CloudFront can be found API gateway.
    - You can use ***edge-optimized*** endpoints in API Gateway.
      - CF usage is already included on API GW pricing.
- Deploy it if you want to fully control CloudFront distribution
  - E.g. manage WAF, add custom behaviors (static files on S3, some paths going to an ALB or other API), host API on multiple regions, set-up CF access logs etc.
# High Availability and Scalability

## Scalability

- Application / system can handle greater loads by adapting
- Scalability is linked but different to high availability
- **Elasticity**
  - System can adapt to workload changes by provisioning and de-provisioning resources automatically to match current demand as closely as possible.
- Two kinds of scalability
  - **Vertical Scalability**: scale up & down
    - Increasing the size of the instance e.g. from `t2.micro` to `t2.large`
    - Common use case
      - Distributed systems, such as database
      - RDS, ElastiCache can scale vertically
    - Hardware limits how much you can scale
  - **Horizontal Scalability** (= elasticity): scale in & out
    - Increase number of instances / systems for application
    - Common for web applications / modern applications
    - Easy with e.g. Amazon EC2 through right-clicking
      - For EC2, you can use *Auto Scaling Group*s or *Load Balancer*s for horizontal scaling

## High Availability

- Goes hand in hand with horizontal scaling
- Running application in at least 2 data centers (= Availability Zones)
  - Goal is to survive a data center loss
- Can be:
  - Passive e.g. RDS Multi AZ
  - Active e.g. for horizontal scaling
- For EC2, you can use *Auto Scaling Group* with multi AZ enabled and *Load Balancer* with multi AZ enabled.
- 💡 Design for failure as it can happen
  - 🤗 Netflix has simian army (monkeys) to inject failure into their production systems.
    - E.g.
      - Chaos Monkey => Terminate random EC2 instances
      - Chaos Gorilla => Delete entire AZ
      - Latency Monkey => introduces latency.
      - and [more](https://medium.com/netflix-techblog/the-netflix-simian-army-16e57fbab116)...

### Common architectures

- **Single region HA architecture**
  - ![Single region AZ failure tolerant architecture](img/high-availability/single-region-architecture.png)
  - 3 tier application with 3 tiered security

    | Tier | Role | Example service | Security Group |
    | ---- | ---- | --------------- | -------------- |
    | 1 | Client | Application Load Balancer | Allow HTTP + HTTPs |
    | 2 | Application  | Application servers (EC2) | Allow HTTP from Client |
    | 3 | Database | Amazon RDS | Allow e.g. MySQL from application SG |
  - In a single VPC
    - At least 2 AZ and and in each AZ:
      - A public subnet and in each public subnet:
        - Deploy NAT gateway
        - Load balance public subnet in AZs through application load balancer
      - A private subnet and in each private subnet:
        - Deploy application server in same auto-scaling group
        - Create route table that routes internet (0.0.0.0/0) to the NAT gateway of a public subnet in same AZ
        - Configure auto-scaling group to put servers in public Load balancer
        - Deploy RDS instance in one, and RDS multi-AZ standby in other one
  - 📝 E.g. if you have minimum 6 instances running and can tolerate failure of 1 AZ failure:
    - Deploy 3 AZ with 3 instances in each AZ so you always have 6 instances running.

- **Multi region HA architecture**
  - E.g. using S3
    - ![Multi region HA architecture using S3](img/high-availability/multi-region-s3-architecture.png)
  - E.g. using RDS
    - ![Multi region HA architecture using RDS](img/high-availability/multi-region-rds-architecture.png)
# Load Balancers

- Requests and responses go through load balancer to EC2
- Benefits
  - Spread load
  - Single point of access (DNS) to your application
  - **Fault tolerance**: Seamlessly handle failure of downstream instances with health checks
  - Enforce ***stickiness*** (sessions) with cookies: same user -> same instance
    - You can control expiration date of the cookie.
    - 📝Load is not spread evenly then: e.g. causes one LB is having 80% CPU, other one 20%.
  - High availability across zones
  - Separate public traffic from private traffic

## ELB: Elastic Load Balancer

- ELB is ***managed load balancer***
  - Highly available & AWS guarantees that it'll be working
  - Cheaper to setup your own load balancer but more effort.
  - It is integrated with many AWS offerings / services

### CLB: Classic Load Balancer

- Deprecated: (v1 - old generation) - 2009
- Requires one load balancer per application -> very expensive & inefficient
- Has an associated IPv4, IPv6, and dualstack (both IPv4 and IPv6) DNS name
- Cross-zone load balancing

### ALB: Application Load Balancer

- **Application Load Balancer (ALB)** (v2 - new generation) - 2016
- Layer 7 of OSI: HTTP traffic
- Components
  - **Target Groups**
    - Multiple HTTP applications across multiple machines (***target groups***)
      - Target type can be an IP-address or an instance.
    - When you create each ***listener rule***, you specify a ***target group*** and **conditions**.
      - When a rule condition is met, traffic is forwarded to the corresponding target group.
    - Chooses target group based on the load & health checks.
    - ***Stickiness*** is generated only by ALB and can be enabled at target group level.
  - **Listeners** (= rules)
    - E.g. if *protocol = X*, *hostname = Y*, *port = 5*, *path = /test*, *query = q=Hello* then redirect to target group A.
    - Supported condition types are: `host-header`, `http-header`, `http-request-method`, `path-pattern`, `query-string` and `source-ip`.
- Load balance based on
  1. path (e.g. example.com/users, example.com/payments)
     - called also path patterns or path based routing.
  2. hostname in URL (users.example.com & payments.example.com)
- Very good for micro services & container-based applications (e.g. docker & Amazon ECS)
- Has a port mapping feature to redirect to dynamic port
  - 📝Allows load balancing to multiple applications on the same machine (e.g. ECS containers)
- Supports HTTP/HTTPS & WebSockets
- 💡 Application servers don't see the IP of the client directly
  - By default e.g. EC2 instance sees the private IP of the load balancer, not the client.
    - True IP of the client is inserted in the header `X-Forwarded-For`, port in `X-Forwarded-Port` and proto in `X-Forwarded-Proto` headers.
- ❗📝 Uses DNS name you cannot attach a static IP
  - However you can deploy NLB in front of the ASG to get a static IP.
- Supports **authentication**
  - From OIDC compliant identity providers such as Google, Facebook and Amazon.
  - It is implemented through an authentication action on a listener rule that integrates with Amazon Cognito to create user pools.

### NLB: Network Load Balancer

- v2 - new generation - 2017
- Layer 4 of OSI: TCP traffic
- Allows you to
  - Forward TCP traffic to your instances
  - Very high performance: handle millions of request per seconds with less latency
    - Less latency around 100 ms (vs 400 ms for ALB)
- Uses target groups just like ALB
- Types:
  - **Public facing**: must attach Elastic IP -> 📝can help whitelist by clients
  - **Private facing**: Random private IP at time of creation.
- Sees client IP directly
- Can terminate SSL/TLS
- Automatically provides a static IP per AZ to load balancer
  - Enables assigning an Elastic IP to the load balancer per AZ.

### NLB vs ALB

| Attribute | ALB | NLB |
| ---------:|:---:|:---:|
| OSI Layer | 7 | 4 |
| Use case | App player, great for dockers | High network performance |
| Private facing | Private DNS only | Static IP per AZ |
| Public facing | Public DNS only | Must attach Elastic IP |
| Protocol | HTTP, HTTPS, WebSockets | All TCP |
| Latency | ≈ 400 ms | ≈ 100 ms |
| Target groups | ✓ | ✓ |
| Client IP visibility for app servers | Sent in headers | Directly |
| SSL / TLS certificates & termination | ✓ | ✓ |
| To multiple ports on target | ✓ | ✓ |
| To lambda functions | ✓ | ✖ |
| IP/port information | `X-forwarded-for` header | Proxy protocol |

#### Common functionality

- Static host name (do not resolve and use underlying IP)
- Target can be IP address
  - They allow registering e.g. Instances in a peered VPC
- **Pre-warming**: Can scale but not instantaneously - contact AWS for a "warm-up"
  - Traffic increases more than 50% in less than 5 minutes -> Faster for ELB to scale up to meet it.
- Have error status codes:
  - `4XX`: Client induced errors
  - 📝`5XX`: Application induced errors (`503` -> at capacity or no registered target)
- Can be in multiple availability zones
- You can setup ***internal*** (private) or ***external*** (public, internet facing) ELBs.
  - ❗ For public facing ELBs: you need one public subnet in each AZ where the ELB is defined
  - Both types of ELB route traffic to the private IP addresses of EC2 instances
- 📝They must span to at least two public subnets.
- 📝Do not have pre-defined IPv4 addresses
  - NLB can have elastic IP but better to resolve to them using a DNS name
- Route traffic across AZ, but not region.
- **Health Checks**
  - Enable load balancer to know if instances it forwards traffic are available to reply to requests.
    - ELB won't send traffic to unhealthy (crashed) instances
  - The health check is done on a port and a route (`/health` is common)
  - By default, response is not 200 (OK), then the instance is unhealthy
    - You can customize health checks e.g. *healthy threshold*, *unhealthy threshold*, *timeout*, *interval*, *success codes*.
- **Cross load balancing**
  - If disabled:
    - Traffic is distributed evenly to multiple AZ's
    - E.g. for 2 AZ: 50% 50, 5 instances in first AZ share 50% of traffic (under-utilization), 1 instance in second gets 50% (over-utilization)
  - If enabled: Traffic is distributed evenly to instances as if they're on same AZ.
  - Always on in ALB, can be enabled in NLB/ALB.
- **Connection Draining**
  - Enables the load balancer to complete in-flight requests made to instances that are de-registering or unhealthy.
    - Keeps existing connection open when instances are de-registering/unhealthy to get last response.
    - Stops sending new requests though.

#### Security

- **Load Balancer Security Group**
  - LBS must have security group attached to it.
    - 💡📝 If LB can't connect to your application, check your security group.
    - 💡📝 If you don't want your instance IP to be publicly available ->
    - Allow inbound traffic in instance security group only for load balancer security group
  - Usually:
    - In LB, allow inbound HTTP (TCP 80) & HTTPS (TCP 443) from anywhere
    - In instance, allow HTTP (TCP 80) from LB
- **Monitoring**
  - **CloudWatch**
    - Sent from ELB to CloudWatch every 1 to 5 minutes (1 when requests are active)
  - **Access Logs** is an optional feature that logs to S3.
    - Logs useful information for debugging and auditing.
    - E.g. protocol, timestamp, client and target (port & ip), request size,  user agent, processing time.

#### SSL Termination

- **SSL Termination**: HTTPS is not needed internally as LB (network & application) can terminate SSL/TLS.
- You can manage certificates using **ACM (AWS Certificate Manager)** on Load Balancer
  - 📝Alternatively you can create & upload your own certificates.
- Supports **Perfect Forward Secrecy** (new SSL key per session).
- For HTTPS listener:
  - You must specify a default certificate
  - You can add an optional list of certs to support multiple domains
  - Clients can use **SNI (Server Name Indication)** to specify the hostname they reach.
    - SNI is an extension of the TLS protocol.
    - 📝 Allows clients to map many domain names & certificates in one IP.
    - Only available in **Application Load Balancer** and **CloudFront**.
    - You can have unique certificates for each domain (i.e. many certificates) while those domains share the same IP
    - Indicates requested hostname from the browser at the beginning of the 'handshake' process.
    - ***How it works in SSL/TLS?***
      1. Browser require a digital certificate from the server, before it even knows what page the browser wishes to access.
         - Web server checks IP address to see target website of user.
         - Web server send the right certificate to the browser or client.
         - ❗ Problem: IP addresses are limited, can be problematic to require every website to have IP
           - 💡 SNI solves this: the browser communicates directly the hostname instead of just IP so that web server can respond with right certificate
      2. Browser then compares the name on the certificate from the server with the name of the page it is trying to make a connection with.
      3. If the names match, the connection will be made in an ordinary way.
         - The names do not match > A warning message that could indicate a man-in-the-middle attack.
# Scalability

## Scalability vs High Availability

- **Scalability**
  - Application / system can handle greater loads by adapting
  - Scalability is linked but different to high availability
  - **Elasticity**
    - System can adapt to workload changes by provisioning and de-provisioning resources automatically to match current demand as closely as possible.
  - Two kinds of scalability
    - **Vertical Scalability**: scale up & down
      - Increasing the size of the instance e.g. from `t2.micro` to `t2.large`
      - Common use case
        - Distributed systems, such as database
        - RDS, ElastiCache can scale vertically
      - Hardware limits how much you can scale
    - **Horizontal Scalability** (= elasticity): scale in & out
      - Increase number of instances / systems for application
      - Common for web applications / modern applications
      - Easy with e.g. Amazon EC2 through right-clicking
        - For EC2, you can use *Auto Scaling Group*s or *Load Balancer*s for horizontal scaling
  - Improves resiliency as failing components can be easily and automatically replaced.
- **High Availability**
  - Goes hand in hand with horizontal scaling
  - Running application in at least 2 data centers (= Availability Zones)
    - Goal is to survive a data center loss
  - Can be:
    - Passive e.g. RDS Multi AZ
    - Active e.g. for horizontal scaling
  - For EC2, you can use *Auto Scaling Group* with multi AZ enabled and *Load Balancer* with multi AZ enabled.

## ASG: Auto Scaling Group

- Scale out (add EC2 instance) and in (remove EC2 instances) to match load.
- Specify & ensure minimum *(scale in down to)* and maximum *(scale out up to)* of machines running.
  - You also specify ***desired capacity***: actual size before any scale in/out rule triggers.
- Automatically Register new instance to a load balancer
- It's across AZ's & you can specify AZ's but ❗ region locked.
- ***Health checks***
  - *Own health checks*: ASG terminates unhealthy instances (based on EC2 status) and replaces them with new.
  - *ELB health checks*: You can get health data from ELB
- IAM roles attached to an ASG will get assigned to EC2 instances
- ***Pricing***: ASG are free. You pay for the underlying resources being launched.
- ASG restart if its instances get terminated to ensure always one application is running.
- Can send SNS notifications: when instance is launched/terminated, fails to launch/terminate.
- Components
  - Size e.g. start with X instances.
  - Network, subnet information.
  - Load balancer (target group) information
  - 📝 **Health check grace period**
    - An EC2 instance needs to warm up before it can pass the health check.
    - ASG waits until the health check grace period ends before checking the health status of the instance.
    - Status checks / ELB can complete before grace period, but ASG does not act before grace period expires.
  - You need to first create **Launch Configuration**
    - Answers to how to launch EC2
    - Update an ASG by providing a new launch configuration
      - 📝 Existing EC2's are not configured, only new instances are created using updated launch configurations.
    - Includes such as:
      - AMI + Instance Type, EC2 User Data, EBS Volumes, Security Groups, SSH Key Pair, assign public IP or not.
  - A **Scaling Policy**
    - What'll trigger scale out and in
      - E.g. can be CPU, Network, custom metrics or based on schedule.
    - Scale between X and Y instances.
    - When to auto-scale in or not can be based on:
      - **Auto Scaling Alarms**
        - Scale based on CloudWatch alarms.
        - CloudWatch monitors metrics of instances and triggers based on threshold e.g. Average CPU.
        - Metrics are aggregated i.e. computed for the overall ASG instances.
        - **Auto Scaling Custom Metrics**
          - E.g number of connected users.
          - You send custom metrics from application on EC2 to CloudWatch (`PutMetric` API) and set & up CloudWatch alarm & use it.
      - **Auto Scaling rules**
        - Easier to set-up than auto scaling alarms
        - E.g. target average CPU usage, number of requests on the ELB per instance, average network in, average network out.
- 💡 You can attach running EC2 instances to an ASG
- 📝 **Scaling Cooldowns**
  - Ensures your ASG doesn't scale before the previous scaling activity takes effect (metrics goes down to normal)
  - An ASG has a default scaling cooldown
    - You can create cooldowns that apply to a specific simple scaling policy (overrides default)
    - You can reduce costs by terminating additional instances quickly e.g. 180 seconds cooldown instead of 300.
    - 💡 Default is 300 seconds (remember, CloudWatch default interval 5 minutes = 300 seconds)
  - If your app is scaling up and down multiple times each hour ->
    - Modify the ASG cool-down timers
    - Modify CloudWatch Alarm Period that triggers the scale in.
- **Instance termination**
  - If any health check (EC2 or ASG) returns an unhealthy status the instance will be terminated.
  - It will mark the instance for termination, terminate it, and then launch a replacement.
    - I.e. it terminates existing instance before launching a replacement instance
  - **ASG Default Termination Policy**
    - 📝**ASG Default Termination Policy**
      - Balances number of instances across AZ by deciding which instance gets terminated first.
      - Priority:
        1. Find the AZ which has the most number of instances.
        2. Delete the one with the oldest launch configuration.
        3. If all has same, delete the one closest to its billing hour.
      - Always terminates unhealthy instances.
- **Rebalancing**: Auto Scaling can perform rebalancing when it finds that the number of instances across AZs is not balanced using default termination policy.
- You can enable **Instance Protection** to protect a specific instance in an ASG from a scale in action
- **Performing updates/changes/troubleshooting**
  - You can **suspending** scaling processes to suspend invoking scaling processes.
  - Set **standby state** on instance
    - Auto scaling does not perform health checks on instances in the standby state.

## Auto Scaling Types

- Comparison

  | | AWS Auto Scaling | Amazon EC2 Auto Scaling | Auto Scaling for Other Services |
  | - | -------------- | ----------------------- | ------------------- |
  | Resources you can scale | EC2 Auto Scaling groups, EC2 Spot Fleets, ECS services, DynamoDB provisioned capacity for tables & GSIs, Aurora Replicas | EC2 Auto Scaling groups | EC2 Spot Fleets, ECS services, DynamoDB provisioned capacity for tables & GSIs, Aurora Replicas, EMR clusters, Appstream 2.0 fleet, Sagemaker endpoint variants |
  | Scaling method | Application-wide scaling using a unified interface | One Auto Scaling group at a time | One resource at a time |
  | Predictive Scaling | Yes (EC2 Only) | No | No |
- In EC2 with ASG
  - **Scheduled scaling**
  - **Dynamic scaling**
    - **Target tracking scaling**: Based on a target value for a specific metric, e.g. CPU at 40%.
    - **Step scaling**: based on a set of *scaling adjustments*
      - Scaling adjustment has
        - A lower bound for the metric value
        - An upper bound for the metric value
        - The amount by which to scale, based on the scaling adjustment type:
          - **Change in capacity** e.g. increase by 5
          - **Exact capacity** e.g. set to 5
          - **Percent change** in capacity e.g. 10% CPU increase = 1 instance
    - **Simple scaling**: based on a single scaling adjustment
    - ❗ Step scaling & simple scaling is only available for **EC2 Auto scaling**
  - **Predictive Scaling** uses machine learning models to forecast daily and weekly patterns.
    - ❗ Only for EC2
    - 💡 Good to combine with *target tracking scaling*
      - Predictive scaling for minimum
      - Target tracking scaling for desired
- In Application Auto Scaling *(ECS, Spot Fleet, EMR, AppStream 2.0, DynamoDB, Aurora, SagMaker, or even custom API-compatible APIs)*
  - **Target tracking scaling**: Based on a target value for a specific CloudWatch metric.
  - **Step scaling**: Based on a set of scaling adjustments that vary based on the size of the alarm breach.
  - **Scheduled scaling**: Based on the date and time.
# Disaster Recovery in AWS

- Disaster Recovery (DR) is about preparing for and recovering from a disaster
  - Disaster: events that have negative impact on business continuity or finances
- Recovery types to cloud:
  1. ***On-premise => On-premise***: Traditional DR, very expensive
  2. ***On-premise => Cloud***: Hybrid recovery
  3. ***Cloud => Cloud***: e.g. AWS Cloud Region A => AWS Cloud Region B
- 📝Terminology
  - **RPO: Recovery Point Objective**
    - The amount of data loss you're willing to accept
    - Based on:
      - How back in time you can recover
      - How often you take back-up
  - **RTO: Recovery Time Objective**
    - Amount of downtime you're willing to accept
- **Disaster Recovery Strategies**
  - From business continuity to uninterrupted business:
    - *Backup and Restore* -> *Pilot Light* -> *Warm / Hot Standby* -> *Hot Site / Multi Site*
  - **Backup and Restore**
    - Cheapest option, slowest RTO.
    - Take frequent snapshots of your data:
      - from e.g. EBS Volumes, RDS databases, EC2 AMI's
      - to e.g. S3
    - AWS Storage Gateway enables from on-premises to cloud back-up seamlessly
    - ![Disaster Recovery - Backup and Restore](img/disaster-recovery/backup-and-restore.png)
  - **Pilot Light**
    - Critical core element(s) of the app is always running in the cloud
      - Faster than Backup and Restore as critical systems are already up.
    - Example of ***active/passive failover configuration***.
    - From networking, you have two options: Elastic IP addresses or Elastic Load Balancing (ELB)
      - DNS records to point to EC2 or point to LB using CNAME.
    - Changed data in DR site after failover must be reversed back to primary site after failback.
    - ![Disaster Recovery - Pilot Light](img/disaster-recovery/pilot-light.png)
  - **Warm / Hot Standby**
    - Scaled-down full production environment always running in the cloud.
      - Upon disaster, we can scale to production load
    - Example of ***active/passive failover configuration***.
    - Changed data in DR site after failover must be reversed back to primary site after failback.
    - ![Disaster Recovery - Warm / Hot Standby](img/disaster-recovery/warm-standby.png)
  - **Hot Site / Multi Site**
    - Full Production Scale is running AWS and on-premise
    - Example of ***active-active failover configuration***.
    - Changed data in DR site after failover must be reversed back to primary site after failback.
    - 💡 In Route 53: You configure active-active failover using any routing policy (or combination of routing policies) other than failover.
    - ![Disaster Recovery - Multi site / Hot Site / Active - Active](img/disaster-recovery/multi-site-active-active.png)
- 💡 **Disaster Recovery Tips**
  - ***Backup***
    - EBS Snapshots, RDS automated backups / Snapshots, etc...
    - Regular pushes to S3 / S3 IA / Glacier, Lifecycle Policy, Cross Region Replication
    - From On-premise: Snowball or Storage Gateway
  - ***High Availability***
    - Use Route 53 to migrate DNS over from Region to Region
    - RDS Multi-AZ, ElastiCache Multi-AZ, EFS, S3
    - Site to Site VPN As a recovery from Direct Connect
  - ***Replication***
    - RDS Replication (Cross Region), AWS Aurora + Global Databases
    - Database replication from on-premise to RDS
    - Storage Gateway
  - ***Automation***
    - CloudFormation / Elastic Beanstalk to re-create a whole new environment
    - Recover / Reboot EC2 instances with CloudWatch if alarms fail
    - AWS Lambda functions for customized automations
  - 🤗 **Chaos**
    - Netflix has a "simian-army" randomly terminating EC2.
      - Chaos monkeys terminates instances in production.
# Infrastructure as Code

## CI/CD

### Continuous Integration

- Developers push the code to a code repository often
  - GitHub, BitBucket, ***CodeCommit*** (in AWS) etc...
- A testing / build server checks the code as soon as it's pushed
  - Jenkins CI, TeamCity, ***CodeBuild*** (in AWS), etc...
  - The developer gets feedback about the tests and checks that have passed / failed
- Helps
  - Find bugs early, fix bugs
  - Deliver faster as the code is tested
  - Deploy often
  - Happier developers, as they're unblocked

### Continuous Delivery

- Ensure that the software can be released reliably whenever needed.
- Ensures deployments happen often and are quick
- Automated deployment -> Shift away from e.g. "one release every 3 months" to 5 releases a day.
- ***CodeDeploy*** in AWS, Jenkins CD, Spinnaker, etc...

### Technology Stack

- **AWS CodePipeline** allows you to automate all steps of the deployment:

  | Step | Name | AWS | Others |
  | ---- | --- | ---- | ------ |
  | 1 | Code | CodeCommit | GitHub, GitLab etc.. |
  | 2 | Build & test | CodeBuild | Jenkins CI, TeamCity etc.. |
  | 3 | Deploy | ElasticBeanstalk, CodeDeploy | Octopus |
  | 4 | Provision | ElasticBeanstalk, CloudFormation | Terraform |

- **CodeDeploy**
  - Two deployment options:
    1. **In-place deployment**: EC2/On-Premises apps are stopped & updated & validated.
    2. **Blue/green deployment**: Using slots (deploy A -> switch to A -> stop B) with minimal downtime and rollback capabilities.
       - **EC2/On-Premises**: Must have one ore more EC2 with tags or ASG
       - **Lambda**
         - **Canary**: Traffic is sifted in two increments, 90% to first version, 10% to second before 100% to second.
         - **Linear**: Traffic is shifted in equal increments with number of minutes e.g. 10% per minute.
         - **All-at-once**: All traffic is shifted at once.
       - **Amazon ECS**: Task set switches

## CloudFormation

- Code will be deployed and create / update / delete infrastructure
  - Can be JSON or YAML.
- Can be part of disaster recovery strategy.
- Declarative way of outlining AWS infrastructure for any resources (most of them are supported)
  - CloudFormation provisions in the *right order* with the *exact configuration* that you specify.
- 💡 Allows re-use the best practices around your company for configuration parameters.
- **Templates**
  - You can upload in S3 and then reference in CloudFormation
  - Update a template
    - Can't edit previous ones.
    - Have to re-upload a new version of the template to AWS.
  - **Building Blocks**
    - Cloud formation template consists of components and helpers:
      - Template components
        1. **Resources**: your AWS resources declared in the template (❗ mandatory)
        2. **Parameters**: the dynamic inputs for your template
           - Allows you to parameterize & prompt inputs while deploying.
        3. **Mappings**: the static variables for your template
        4. **Outputs**: References to what has been created
        5. **Conditionals**: List of conditions to perform resource creation
        6. **Metadata**
      - Template helpers
        1. References (***Logical IDs*** are used to reference resources within the template)
        2. Functions
- **Templates**
  - JSON or YAML text file that contains instructions for building out AWS environment
- **Change sets**
  - Before making changes to your resources, you can generate a change set, which is a summary of your proposed changes, e.g. resource A will be deleted.
- **Stacks**
  - The entire environment described by the template and created, updated, and deleted as a single unit
  - Related resources in a template.
    - When you update stack, it updates the resources
  - Can rollback on failure after timeout in minutes.
  - Termination protected with stack from being accidentally deleted.
  - **Monitoring**
    - Fires events such as on resource creation
    - Can have monitoring time (CloudFormation monitors resources during X minutes after creation)
    - Can set up CloudWatch alarms if something fails.
  - Can have IAM role or policy to deny/allow access.
  - Can use sample template or create one in Designer.
  - Identified by name
  - Can have input parameters that'll be filled from portal.
  - Reusable output information
    - It's output information (key, value, description) can be imported by other stacks by using an unique export/import name.
  - **Stack Sets** lets you create stacks in AWS accounts across regions by using a single AWS CloudFormation template
  - Operations
    - **Deleting a stack** -> deletes every artifact created by CloudFormation
    - **Deploying templates/stacks**
      1. by uploading YAML files directly or referencing to Amazon S3 URL
      2. Use a sample template
      3. Create template in Designer
         - Helps you to see resources & relations
         - Might be good for PowerPoints
      - ❗ Redeploying a stack deletes old instances.
    - **Changing a stack**
      - Done using **Stack change sets**
      - Warns you about resources that'll be deleted, modified and added.
      - **Drift**: difference between the expected configuration values and actual deployed.
        - Allows you to better manage your CloudFormation stacks and ensure consistency in your resource configurations
- Can deploy Elastic Beanstalk that'll then deploy:
  - E.g. EC2, ALB, ASG, RDS
- **IAM Conditions for CloudFormation**
  - `cloudformation:TemplateURL`: Ensure template in TemplateURL is used for e.g. update/delete.
  - `cloudformation:ResourceTypes`: Specify which types of resources can be created or updated.
  - `cloudformation:StackPolicyURL`
    - **Stack policies** prevents stack resources from unintentionally being updated or deleted during stack updates
    - You define which actions (t.ex. update) are allowed on which resources in a JSON file.

### Benefits

- **Control**
  - No resources are manually created / configured.
  - Code can be version controlled e.g. using git.
  - Changes are reviewed through code
  - Cost control:
    - Each stack has ID: follow-up costs.
    - Estimate costs of a stack.
    - You can automate deletion of templates at 5 PM and recreation at 8 AM safely.
- **More productivity**:
  - Destroy and re-create an infrastructure on the cloud on the fly.
  - Automated generation of diagram for your templates!
  - Declarative programming (no need to figure out ordering and orchestration)
- Better separation of concerns:
  - Many stacks for many apps, and many layers.
  - E.g. VPC stacks, network stacks, app stacks

## OpsWorks

- AWS managed service for Chef & Puppet
- They work great with EC2 & On Premise VM
- Alternative to AWS SSM (Systems Manager)
- **OpsWorks stacks**
  - Groups applications into layers depending on Chef recipes
  - No need to provision chef server
  - Uses the embedded Chef solo client that is installed on EC2 instances on your behalf

## Systems Manager

- Serverless & free & open-source
- Is built-in in AWS AMIs
- Does not require any jump-boxes (bastion hosts)
- Can run Ansible, PowerShell DSC or any script from S3 through **Run command**.
- Can use AWS Systems Manager Automation
  - Leverages documents for tasks that can be automated.

### Chef & Puppet

- Help with managing server configuration as code
- Helps in having consistent deployments
  - Can automate: user accounts, cron, NTP, packages, services
- They leverage "Recipes" or "Manifests"
- Similar to SSM / Beanstalk / CloudFormation but they're open-source tools that work cross-cloud.

## Serverless applications

- Serverless applications are e.g. Lambda, DynamoDB, API Gateway...
- To be order to locally run, debug and easily deploy serverless applications you can use:
  - **AWS Serverless Application Model (AWS SAM)**
    - Open source tool used to package, test, and deploy serverless applications
    - YAML based infrastructure as code.
  - **serverless.com**
    - Vendor-neutral (Azure, Google, AWS) serverless framework.
    - YAML-based infrastructure as code.
# Overview of AWS Databases

## OLTP vs OLAP

| Parameters | OLTP | OLAP |
| --------- | ---- | ---- |
| **Meaning** | Online transaction processing | Online Analytics processing |
| **Functionality** | Online database modifying system | Online database query management system |
| **Purpose** | Real time business operations | Analysis of business measures by category and attributes |
| **Characteristic** | Large numbers of short online transactions | Large volume of data |
| **Style** | Fast response time, low data redundancy and is normalized | Created uniquely to integrate different data sources for building a consolidated database |
| **Design** | Application oriented e.g. Banking, ticket booking, SMS delivery etc. | Subject oriented e.g. for sales / marketing / purchasing etc. |
| **Query type** | Standardized and simple | Complex queries involving aggregations |
| **Operation** | Read/write | Only read and rarely write |
| **Tables** | Normalized | Not normalized |
| **Method** | DBMS | Data warehouse |
| **AWS Services** | Aurora, RDS | Redshift |

## Choose right database

| Category | Question | Deeper |
| -------- | -------- | ------- |
| Workload | Read-heavy, write-heavy, or balanced workload? | • Throughput needs? High or low throughput? • Will it change, does it need to scale or fluctuate during the day? |
| Size | How much data to store and for how long? | • Will it grow? • Average object size? • How are they accessed? Security needs? |
| Durability | Data durability (e.g. for a week or forever)? | • Source of truth for the data? |
| Latency | Latency requirements? | • Concurrent users? |
| Model | Data model? | • How will you query the data? Primary key? Joins? • Structured? Semi-structured? |
| Schema | Strong schema or more flexibility? | Reporting? Search? RDBMS / NoSQL? |
| Licensing | License costs? | Switch to Cloud Native DB such as Aurora? |

## Database Types

- All Database types comes with backup / restore feature.

| Type | Databases | Use cases |
| ---- | --------- | --------- |
| RDBMS | RDS, Aurora | SQL, OLTP, Joins, Data in tabular form |
| Key-value | DynamoDB (also Document ≈JSON), ElastiCache | No joins & more performance and scalability |
| Object Store | S3, Glacier | Big objects (S3), backups / archives (Glacier) |
| Data Warehouse | Redshift, Athena | SQL Analytics, BI, OLAP (Redshift) |
| Search | Elastic Search (JSON) | Free text, unstructured searches |
| Graphs | Neptune | Relationships between data. |

## Comparison

| Database | Type | Use-case | Operations | Security | Reliability | Performance | Cost |
| -------- | ---- | -------- | ---------- | -------- | ----------- | ----------- | ---- |
| **RDS** | RDBMS | Relational datasets (RDBMS, OLTP), transactional SQL queries. | • Small downtime if failover, maintenance, scaling replicas / EC2, restore EBS • Changes requires application changes | • AWS -> OS / EC2 • Users -> KMS, SG, IAM policies, user auth with e.g. IAM, SSL | Multi AZ feature with auto-failover | • Up to 5 read replicas • Depends on EC2, EBS, read replicas • no auto-scaling | Pay per hour based on provisioned EC2 and EBS |
| **Aurora** | RDBMS | Same as RDS with less maintenance and more performance & flexibility | Less operations than RDS e.g. auto scaling storage | Same as RDS | • Multi AZ • More HA than RDS • Serverless option for more reliability | • 5x than RSD • Up to 15 read replicas | • Pay per hour for EC2 + storage • Lower than Oracle • Higher than RDS |
| **Redshift** | Columnar | • Analytics • Data Warehousing  | Similar to RDS | IAM, VPC, KMS, SSL (similar to RDS) | highly available, auto healing features | • Massively Parallel Query Execution • compression • scale to PBs of data | Pay per node provisioned, 10% of cost vs other warehouses |
| **ElastiCache** | Key-value | • caching, user sessions, leaderboard, distributed states, pub / sub messaging, recommendation data | Same as RDS | Same as RDS but auth through Redis Auth)  | Clustering (sharding), Multi-AZ | Sub-millisecond, in-memory, read replicas for sharding | Pay per hour based on EC2 and storage usage. |
| **DynamoDB** | Key-value & document | • no SQL with transactions • serverless development • cache • small objects | • no operations needed • auto scaling capability • serverless | through IAM policies, KMS encryption, SSL in flight | • Multi AZ • Backups | • Single digit (1-9 millisecond) • DAX for read caching • Performance doesn't degrade if usage scales | Pay per provisioned capacity and storage usage (can use auto-scaling) |
| **S3** | Key-value object store | • big objects • static files • website hosting | no operations needed | • IAM • Bucket Policies • ACL • Encryption (Server/Client) • SSL | • 99.999999999% durability / 99.99% availability • Multi AZ • Cross-region replication | • Scales to thousands of read / writes per second • Transfer acceleration with CloudFront • Multi-part for big files | Pay per • Storage usage • Network cost: bandwidth to transfer and retrieve data • Requests number |
| **Athena** | S3 query engine | • Serverless *(one time)* queries on S3 • Log analytics • Lightweight queries | no operations needed | IAM + S3 security | • managed service • uses presto engine • highly available | Queries scale based on data size | pay per query / per TB of data scanned |
| **Neptune** | Graph | High relationship data e.g. social networking, knowledge graphs (wikis) | similar to RDS | IAM, VPC, KMS, SSL (similar to RDS) + IAM Authentication | Multi-AZ, clustering with read replicas | best suited for graphs, clustering to improve performance | pay per node provisioned (similar to RDS) |
| **ElasticSearch** | Lucene search engine | • complement to another database • big data • log analysis  | similar to RDS | Cognito, IAM, VPC, KMS, SSL | Multi-AZ, clustering | based on ElasticSearch project (open source), petabyte scale | pay per node provisioned (similar to RDS) |

### RDS vs DynamoDB Availability and Scalability

| | Aurora | DynamoDB |
| - | ----- | ------- |
| Roll-back impact | No downtime, more IO (uses snapshots), requires re-establishing connection after DB change | No downtime, no IO (uses Backup and Restore), requires re-establishing connection after DB change |
| Multi-master | Across AZ in single region (even with global database) | Only across regions with global tables |
| Multi-reader | Across AZ & regions | Automagically, only can set-up with DAX |
| Failover to replicas | Any replica | Any replica |

## Data migration

- **Native SQL DB migration**: From SQL Server => upload `.bak` to S3 => restore from `.bak` with SQL statement in RDS
- **AWS Database Migration service**
  - Migrate and/or replicate databases and data warehouses to AWS.
    - ***From***
      - ***On-premises/EC2/Azure***: Oracle, SQL Server, Azure SQL, PostreSQL, MySQL, SAP ASE, MongoDB, S3, DB2
      - ***AWS Native***: RDS & Aurora & S3
    - ***To***:
      - ***AWS-native***: RDS, S3, DynamoDB, Redshift, Kinesis Data Streams, Elasticsearch, DocumentDB
      - ***On-premises/EC2***: Oracle, SQL Server, PostgreSQL, MySQL, SAP ASE
  - Supports
    - **Cloud to cloud**: e.g. you can stream to Amazon Kinesis Data Streams through AWS Database Migration Service
    - **On-prem to cloud**
      - You can migrate SQL databases
        - 💡 Instead: native SQL DB migration through `.bak` file is recommended.
        - 💡 Only recommended if your database cannot be offline while back-up is created/copied and restored.
    - **On-prem to on-prem**
      - E.g. upgrade a minor version with on-prem SAP ASE to on-prem SAP ASE
  - **Schema Conversion Tool**: E.g. from NoSQL to SQL, SQL to NoSQL or NoSQL to NoSQL.
  - **Data Extractor**: Runs on-prem, pulls data from DB, uploads to S3 so other DBs can pull it from S3.
  - 💡 Other use-cases: Classic to VPC, Data warehouse to Redshift, Consolidate shards into Aurora, Archive old data, migrate from NoSQL <=> SQL or NoSQL <=> NoSQL.
  - **Pricing**
    - Ingress into AWS Database Migration Service is free
    - Egress to RDS and EC2 in same AZ is also free.
# RDS (Relational Database Service)

- Managed Aurora / PostgreSQL / MySQL / MariaDB / Oracle / SQL Server
- 💡 Use cases: Relational datasets (RDBMS, OLTP), transactional SQL queries.
- Must provision an EC2 instance & EBS Volume type and size
  - You can reserve RDS Reserved Instances from 1 to 3 years.
- ***Maintenance***: Auto minor version upgrade, Maintenance window (when)
- ***DB parameter group*** acts as a container for engine configuration values that are applied to one or more DB instances.
  - Changing groups = recreate a new group & assign (you can copy existing)
- **MySQL & PostgreSQL engines**
  - MyISAM (storage engine)
    - Use for intense, full-text search capability,
    - Not recommended overall
  - InnoDB is recommended (better crash recovery)
    - Compatible with Aurora
- **Monitoring dashboards**
  - CloudWatch monitors metrics from its hyper-visor, very high level.
  - **Enhanced Monitoring**
    - Collects metrics within the agent
    - For each processes or threads use CPU, memory, disk, their virtual size etc.
    - Pros
      - smaller monitoring interval results
      - pay only for monitoring that exceeds the free tier provided by Amazon CloudWatch Logs.
    - Cons
      - Compute-intensive workload have more OS process activity to report and higher costs.

## Security

- Usually deployed within a private subnet, not in a public one.
- Network level
  - Are into **VPC**
  - **DB subnet group** allows you to specify set of subnets in your VPC for your instances.
  - **Security groups**
- Can allow public accessibility (outside VPC access with public IP)
  - Gets an endpoint like `sqldb.c2bqxjtofpnk.eu-west-3.rds.amazonaws.com`)
  - I.e. does not & cannot have an IP address.
- IAM policies help control who can ***manage*** AWS RDS
- ❗ You cannot access the SSH into underlying instances directly by design.
- To connect:
  1. Traditional username and password
  2. IAM DB Authentication (for MySQL & Aurora)
     - Provides an IAM DB authentication.
     - Lifespan of an authentication token is 15 minutes (short-lived)
     - 💡 Recommended over username & password
     - ❗ Ensures SSL must be used when connecting to the database
- **Encryption**
  - **Encryption at rest** capability with AWS KMS - AES-256 encryption
    - ❗ Can only be enabled when you first create the DB instance
      - 💡 You can however: unencrypted EB -> snapshot -> copy snapshot as encrypted -> create DB from snapshot
    - **Transparent Data Encryption**
      - TDE is an encryption type which encrypts the ***data*** and ***log files*** of a database.
        - It means encrypting databases both on the hard drive and consequently on backup media.
        - Key is stored in master database -> Protection of backup files as they don't have the DEK (***database encryption key***)
          - Restoring from back-up requires same DEK in the destination database.
        - Enabling TDE also encrypts your `tempdb`.
      - ❗ Only for Oracle or SQL Server DB instance only as its their technology.
        - Cannot be used on Postgres or MySQL but similar feature is KMS Encryption
      - TDE can be used on top of KMS - may affect performance.
  - **SSL certificates** to encrypt data to RDS in flight
    - 📝To ***enforce*** SSL
      - *PostgreSQL*: `rds.force_ssl = 1` in the AWS RDS Console (Parameter Groups)
      - *MySQL*: Within the DB: `GRANT USAGE ON *.* TO 'mysqluser'@'%' REQUIRESSL;`
    - To ***connect*** using SSL
      - Provide the SSL Trust certificate (can be downloaded from AWS).
      - Provide SSL options when connecting
  - **Encryption of Read Replicas**
    - If in same region => encrypted using same key as the master instance
    - Different regions => you encrypt using the encryption key for that region

## Scalability

- Vertical scaling with EC2 sizing
  - ❗ Resizing makes database temporarily unavailable (generally a few minutes).
    - 💡 For minimal downtime: resize reader instance and then promote it to its own DB.
  - Occur during the maintenance window if you don't choose to apply it directly.
  - **Amazon RDS Storage Types**
    - **General Purpose SSD**: Cost effective, can burst to 3.000 for limited time.
    - **Provisioned IOPS**: I/O-intensive workloads, particularly database workloads, that require low I/O latency and consistent I/O throughput.
    - **Magnetic**: for backward compatibility, lower storage, not recommended
- **Read replicas**
  - Enables horizontal read scaling.
  - Cannot be auto-scaled.
  - Replicate master to replicas asynchronously
  - Each read replica will have separate DNS endpoint.
  - Improves read performance
  - Within AZ, Cross AZ or Cross Region
  - Replicas can be promoted to their own DB -> breaks replication with the read replica.
  - Can have own Multi-AZ copies.
  - MySQL & MariaDB read replicas can have read replicas
  - You can migrate to Aurora by creating Aurora Read Replica (single click).
  - ❗ Must enable back-ups to enable read replica (not Aurora).
    - The replica is built by 1st restoring from the backup, and then only replicating blocks / record of changes since the backup was taken
  - ❗ You can create up to 5 read replicas
  - ❗ Not available for SQL server.
  - ❗ SELECT (=read) only kind of statements (not INSERT, UPDATE, DELETE)
  - ❗ Read replicas does not help with disaster recovery
    - You cannot failover to a read replica, use Multi-AZ instead.
- **Auto-scaling**
  - *For horizontal scaling*: Not available for read replicas
  - *For vertical scaling*: **RDS Storage Auto Scaling** automatically scales storage capacity in response to growing database workloads, with zero downtime.

## Availability

- **Point in Time Restore**: Continuous backups and restore to specific timestamp
  - **RDS Backups**: Automatically enabled
    - **Automated backups**
      - Daily full snapshot of the database
      - Capture transaction logs in real time -> ability to restore to any point in time by applying transaction logs to the latest snapshot
      - Retention between 1 to 35 (❗ max) days (default: 7 days)
      - 💡 If you want to copy database: Restore from a snapshot, the database will have schemas and ready & will be quicker than big create query.
    - **DB Snapshots**
      - Manually triggered by the user
      - Retention of backup for as long as you want
      - When you delete an instance you can choose whether to create a final snapshot of the DB instance.
        - 💡 Should be set as default with CloudFormation.
    - ❗ I/O may be briefly suspended while the backup process initializes (typically under a few seconds), and you may experience a brief period of elevated latency.
    - Restored DBs will always be a new RDS instance with a new DNS endpoint.
    - You can restore up to the last 5 minutes.
- **Multi AZ**
  - Seamless sync replication of master DB to standby replica in another AZ
    - You cannot read from it, for it see read replicas.
  - One DNS name - automatic app failover to standby
  - Not used for scaling but for DR.
  - 📝 When rebooting you can select to fail-over to other AZ.
  - **Multi AZ vs Read Replicas**

    | Attribute | Multi-AZ Deployments | Read Replicas |
    | --------- | -------------------- | ------------- |
    | **Replication** | Synchronous replication, Durable | Asynchronous replication, Scalable |
    | **Accessibility** | 👎 Only primary instance is active | 👍 Accessible and can be used for read scaling |
    | **Backups** | 👍 Automated backups are taken from standby (no I/O on primary) | 👎 No backups configured by default |
    | **Zone** | Always span two Availability Zones within a single Region | Can be within an Availability Zone, Cross-AZ, or Cross-Region |
    | **DR** | 👍 Automatic failover to standby when a problem is detected | 👎 Can be manually promoted to a standalone database instance |
  - **Single AZ -> Multi AZ**
    - AWS does: snapshot primary instance, create new standby instance from snapshot in different AZ, configure synchronous replication between primary and snapshot.
- **Backtrack** rewinds the DB cluster to the time you specify
  - Does not replace point-of-time restore to back-ups
  - You can easily undo mistakes.
- **Multi regions** for DR (Disaster Recovery).
# Aurora

- Compatible API for PostgreSQL / MySQL
- Define EC2 instance type for aurora instances
- **Automatic fail-over**: Failover in Aurora is instantaneous. It's HA native.
- **Auto-expanding**: Automatically grows in increments of 10 GB, up to 64 TB
- Does not have free tier.
- DB engine can be MySQL / Postgres with version
- DB instance must have unique identifier unique in AWS region
- Other features: backup and recovery, industry compliance, push-button scaling, automated patching with zero downtime, advanced monitoring, routine maintenance
- **Capacity types**
  - ***Provisioned***: Provision and manage the instance sizes
  - ***Serverless***: Specify min and max resources and Aurora auto-scales
  - ***Multi-AZ deployment***: Through replicas in different zone
- Can enable Enhanced monitoring for more metrics.
- **Monitoring**: Export log types to publish to Amazon CloudWatch (Audit, Error, General, Slow query)
- **Deletion protection**: You can't delete the database (protects from accidental deletions)
- You can handle parameters using
  - DB Parameter Group
  - DB Cluster Parameter Group
  - Option groups
- **Pricing**
  - Aurora costs more than RDS (20% more) but more efficient
  - Billed per IO.
    - **Read IO**: Every database page read operation is 1 IO.
    - **Write IO**: Pushing transaction logs to shared storage for high availability.
- 💡 Improve query performances
  - **Hash Joins**: If you need to join a large amount of data by using an equijoin.
  - **Asynchronous key prefetch (AKP)** Improve the performance of queries that join tables across indexes
- **Aurora DB Cluster**

## Aurora DB Cluster

- Cluster-types
  - **Multi-master clusters**
    - All DB instances have read-write capability.
  - **Single-master clusters**
    - Has two instances types:
      1. **Primary DB instance**: master
         - Each Aurora DB cluster has one primary DB instance.
         - You can use MySQL / PostgreSQL RDS as master in a cluster
      2. **Aurora Replica**
         - Read-only replicas of master
- **Cluster endpoints**
  - **Reader endpoint**: Read replicas
  - **Writer endpoint**: Master
    - If master fails, clients still talk to the endpoint and will be redirected to the right instance
  - **Custom Endpoints**: E.g. point analytics workload to higher memory capacity replicas
  - 💡 Best practice: connect writer endpoint to write and reader endpoint to read.

## Availability

- Automated **backups** are always enabled.
  - Or use ***Backtrack*** to restore data at any point of time without using backups
  - You can also take **snapshots** & share with other AWS accounts without impacting performance.
    - User-created snapshots are retained after deleting the DB.
    - You can share snapshots across accounts.
- Faster RTO than other RDS engines by making buffer cache of DB immediately available.
- **Multi-AZ** by default with 6 copies (replicas) across 3 AZ:
  - 4 copies out of 6 needed for writes
  - 3 copies out of 6 need for reads
  - You can add additional MySQL or Aurora (recommended) replicas.
    - External MySQL databases as replicas are supported.
    - ❗ Global databases are recommended instead.
- You can have **multi-region** replicas.
- Self healing with peer-to-peer replication
  - Auto healing i.e. Aurora will recover if we lose one replica
- One Aurora Instance takes writes (master)
- Automated failover for master in less than 30 seconds
- **Failover behavior**
  - You can failover to any replica -> Secondary becomes the master, replication breaks.
    - You can assign priority for replicas, or the one which has same size as primary will be more prioritized.
  - **Single instance**: Create a new DB in the same AZ, if it fails => try different AZ
    - RTO: 15 min
  - **Multi replicas**: Flips the canonical name record (CNAME) for your DB Instance to point at the healthy replica
    - RTO: 15 seconds
- **Aurora Global databases**
  - Span multiple regions (global)
  - One primary region, one DR region (can be used for lower latency read)
  - < 1 second replica lag on average
  - 📝If not using Global Databases, you can create cross-region Read Replicas
    - 💡 Global databases are recommended: much faster replicas.

## Scalability

- **Vertical Scaling**
  - **Storage Scaling**
    - No need to provision
    - Auto-scales up to 64 TB, in 10GB increments
    - Zero-impact to database performance when scaling.
  - **Compute resources**
    - Can happen during maintainance window
    - Or immediately (a few minutes of downtime)
- **Horizontal Scaling**
  - **Write Scalability**
    - **Multi-Master**: create multiple read-write instances across multi-AZ.
  - **Read Scalability**
    - Aurora can have 15 replicas (+ master)
      - MySQL has 5, an the replication process is faster (sub 10ms replica lag)
    - Support for Cross Region Replication
    - All replicas and master uses a shared storage that's auto expandable
    - Read Replicas can be global
    - Read replicas can have second their (their own) read replicas.
    - 💡 You can use Aurora Replicas as failover targets.
    - Auto-scaling is done through **Auto Scaling policy**
      - You can scale with based on metrics e.g. average CPU utilization of Aurora Replicas
      - For target value e.g. 60% of CPU utilization, specify minimum & maximum replicas and cooldown periods (Seconds between scale-in and out), and enable/disable "Scale in"
- **Shared storage architecture**
  - Makes your data independent from the DB instances in the cluster.
  - You can add a DB instance quickly because Aurora doesn't make a new copy of the table data
  - Storage is striped across 100s volumes
  - You have auto-scaling for all replicas
  - All read replicas connects to **Reader Endpoint** (e.g. `dbname-ro.id.region.amazonaws.com`)
    - Reader endpoint is connection load balancing
    - Load balancing happens at the connection level not the statement level.
      - Anytime client connects to reader endpoint, the client will get connected to the one of the reader endpoint.

## Security

- Encryption at rest using KMS
  - Automated backups, snapshots and replicas are also encrypted
- Encryption in flight using SSL (same process as MySQL or Postgres)
- 📝Can use IAM authentication for Aurora MySQL and Postgres
  - You can also use master username, master password but it's worse.
- Network level: VPC, subnet, public accessibility (can SSH), availability zone (specify where), create new security group or choose existing one.
- ❗ You cannot access the SSH into underlying instances directly by design.
- ❗ You cannot encrypt an existing unencrypted database.
  - Create a new database with encryption enabled and migrate your data instead.

## Aurora Serverless

- No need to choose an instance size -> it auto-scales for you
- Helpful when you can't predict the workload
- DB cluster starts, shutdown and scales automatically based on CPU / connections
- Can migrate from *Aurora Cluster* to *Aurora Serverless* and vice versa
  - Through restoring snapshots
- Aurora Serverless usage is measured in ACU (Aurora Capacity Units)
  - Billed in 5 minutes increment of ACU
- ❗ Some of Aurora features are not supported, check documentation

## Aurora parallel query

- Ability to push down and distribute the computational load of a single query across thousands of CPUs in Aurora’s storage layer
- Good fit for analytical workloads requiring fresh data and good query performance, even on large tables.
- Can be enabled/disabled globally and on session level with `aurora_pq` parameter.
- Causes higher I/O costs as it scans all data.
- ❗ Not available for serverless, Backtrack-enabled instances, and non R-* instances.
- 💡 You can extend your own MySQL databases to Aurora
- You can use Aurora to scale reads for external MySQL databases
- You can use Aurora for DR with external MySQL databases
# DynamoDB

- AWS proprietary technology, managed NoSQL database
- Serverless, provisioned capacity, auto scaling, on demand capacity
- Can replace ElastiCache as a key/value store
  - Advantage is that it's serverless e.g. no provisioning & maintenance
  - Not sub millisecond (as ElasticSearch) performance but single digit (1-9 millisecond) performance.
- **Availability**
  - Multi AZ in 3 data centers by default
  - Highly available
  - **Backup and Restore**: Point in time restore like RDS without performance impact
    - ❗ On restored table, you need to manually setup the following: ASG, IAM, CloudWatch metrics/alarm, tags, stream & TTL settings.
- Read & Writes are decoupled: you can provision read and write capacity units
- **Consistency**
  - Reads can be eventually consistent or strongly consistent
    - Eventually consistent: accept stale data
    - Strongly consistent: get always newest data
  - *(Optionally)* **Transactions** for writes
    - All or nothing type of operations
    - Coordinated Insert, Update & Delete across multiple tables
    - ❗ Include up to 10 unique items or up to 4 MB of data
- Monitoring through CloudWatch
- ❗ Can only query on primary key, sort key or indexes
- 💡 Use cases:
  - Serverless applications development (small documents 100s KB)
  - Distributed serverless cache
  - Doesn't have SQL query language available
  - Has transactions capability
- Amazon DMS (Database Migration Service) can be used to migrate to DynamoDB from Mongo, Oracle, MySQL, S3, etc...)
- You can launch a local DynamoDB on your computer for development purposes.
- 💡 **Best Practices**
  - Keep item sizes small
  - If you are storing serial data in DynamoDB that will require actions based on data/time use separate tables for days, weeks, months
  - Store more frequently and less frequently accessed data in separate tables
    - Allows you to set appropriate provisioning levels independently for the tables
  - If possible compress larger attribute values
  - Store objects larger than 400KB in S3 and use pointers (S3 Object ID) in DynamoDB

## Security

- VPC Endpoints available to access DynamoDB without internet
  - VPC Endpoint = an endpoint network interface (ENI)
- Access fully controlled by IAM
- Encryption at rest using KMS
- Encryption in transit using SSL / TTLS

## Scaling options

1. On-demand
   - Auto scales by Application Auto Scaling
     - **Scaling policy**
       - Whether to scale read or/and write capacity.
       - Minimum and maximum provisioned capacity unit
       - Supports *setting target utilization* with *target tracking*.
   - 2.5x more expensive than provisioned capacity (use with care!)
2. **Provisioned** RCU (read compute unit) & WCU (write compute unit)
   - They're decoupled can be increased / decreased individually
   - 💡 Allows you to control costs for predictable workloads.
   - 1 RCU
     - 1 strongly consistent read of 4 KB per second
     - 2 eventually consistent read of 4 KB per second
   - 1 WCU = 1 write of 1 KB per second
   - Throughput can be exceeded temporarily using ***burst credit***
     - If burst credit are empty, you'll get `ProvisionedThroughputException`.
     - It's then advised to do an exponential back-off retry

## DynamoDB Tables

- DynamoDB is made of **tables**
  - You don't create database, database is available and you just create tables
- Each table has a **primary key** (must be decided at creation time)
  - Primary key values are unique identifiers across all partitions
- Each table have optional ***secondary indexes***.
  - Efficient access to data with attributes other than the primary key
  - **Global secondary index**
    - = Partition key (optionally + sort key) that can be different from those on the base table.
    - Index can span all of the data in the base table, across all partitions
    - No size limitations: Has its own provisioned throughput settings for read + write separate from table.
  - **Local secondary index**
    - = Partition key same as the base table + different sort key.
      - Sort key e.g. timestamp to query for time ranges.
    - Every partition of a local secondary index is scoped to a base table partition that has the same partition key value.
    - ❗ The total size of indexed items for any one partition key value can't exceed 10 GB.
  - 💡 All indexes requires unique partition key and a sort key
    - ❗ So A limitation is e.g. when you need to perform an indexed lookup of records by time across multiple primary keys:
      - DynamoDB might not be the ideal service for you to use
      - You might need to utilize a separate table (either in DynamoDB or a relational store) to store item metadata that you can perform an indexed lookup against.
- Each table can have an infinite number of items (=rows)
- Each item has ***attributes*** (can be added over time - can be null)
- ❗ Maximum size of an item is 400 KB
- Data types supported are:
  - Scalar types: `string`, `number`, `binary`, `boolean`, `null`
  - Document types: `list`, `map`
  - Set types: `string set`, `number set`, `binary set`

## DynamoDB Streams

- Changes in DynamoDB (Create, Update, Delete) can end up in a DynamoDB Stream
  - Generates changelogs for every operation
- Enables driven programming by integrating to **AWS Lambda**.
  - ❗ Must enable it to be able trigger a lambda.
- Can implement cross region replication using Streams
  - Streams enable DynamoDB to get a changelog and use that changelog to replicate data across regions
- Stream has 24 hours of data retention

## DAX: DynamoDB Accelerator

- Seamless cache for DynamoDB, no application rewrite
  - Application talks directly to DynamoDB accelerator with same connection string
- Writes goes through DAX to DynamoDB
- Micro second latency for cached reads & queries
- 📝 Solves Hot Key problem (too many reads)
- 5 minutes TTL for cache by default
- ❗ Up to 10 nodes in the cluster
- Multi AZ (3 nodes minimum recommended for production)
- Security with encryption at rest with KMS, VPC integration, IAM, CloudTrail...

## Global Tables

- Global service with region specific tables
- ❗ You must enable first **Dynamo Streams**
- **Global Tables** are multi region, fully replicated, high performance
- **Multi-Master**
  - Create multiple read-write instances per region
  - All changes are replicated to all tables.
  - Allows write-scaling.
- If data is updated from multiple regions
  - ***last writer wins*** to ensure eventual consistency
# Redshift

- 📝 Redshift = Data warehouse = Analytics / BI
- Columnar database e.g. stores columns of data instead of rows.
  - Good for OLAP (online analytical processing), see RDBMS for OLTP (online transaction processing)
  - Makes compression very easier
- For analytics and data warehousing
- Used to pull in very large and complex data sets.
- Has a SQL interface for performing the queries
- Heavily modified version of PostgreSQL
- **Scaling**
  - Can scale to PBs of data
  - From 1 node to 128 nodes, up to 160 GB of space per node
    - ***Leader node***: for query planning, results aggregation
    - ***Compute node***: for performing the queries, send results to leader
- It's a **Massively Parallel Query Execution (MPP)** database.
  - Coordinated processing of a single task by multiple processors, each processor using its own OS and memory and communicating with each other using some form of messaging interface.
  - Data and query is automatically distributed and balanced.
- Pay as you go based on the instances provisioned (not serverless)
- 💡 Choose right cluster size & use as much as you can
- 💡 Great to use with BI tools on top of it such as AWS Quicksight, SQL Server Reporting Services,Tableau integrate with it
- Data is loaded from S3, DynamoDB, DMS (Database Migration Services),  other DBs...
- Good practice -> Create read replica from RDS and pull the data from the read replica and load it into Redshift
- **Copy between regions**: Take snapshot => Copy snapshot to new region => Create cluster from snapshot
- **Redshift Spectrum**: serverless service to perform queries directly against S3 (no need to load)
- **Security**: VPC / IAM / KMS & CloudWatch Monitoring
  - **Redshift Enhanced VPC Routing**: COPY / UNLOAD goes through VPC
  - Encrypted in transit using SSL
- **Pricing**: Pay per Compute Node hours (not leader node), backup and data transfer (only within a VPC)
- **Cluster subnet group** allows you to specify set of subnets in your VPC for your instances.

## Availability

- No multi AZ, only available in 1 AZ
  - Snapshots can be deployed to new AZ for e.g. DR.
- Backups are enabled by default, max 35 days.
- Attempts to maintain at least three copies of your data.
  - Original, replica on the compute nodes, and a backup in S3.
- Have functionality for **Automated Cross-Region Snapshot**.
- Always keeps three copies of your data
# ElastiCache

- ElastiCache is managed Redis or Memcached
- Key-value store
- Caches are in-memory databases with really high performance, sub-millisecond latency
- Leads to faster reads & stateless applications
- Must provision an EC2 instance type e.g. `cache.t2.micro` & number of read replicas.
- **Scaling**
  - Write Scaling using sharding
- **Availability**
  - Multi AZ with Failover Capability
- An alternative is DynamoDb with DAX.
- Monitoring through CloudWatch
- Backup / snapshot / point in time restore features
- Caching patterns
  - *Application* queries *ElastiCache*, if not available, get from RDS and store in *ElastiCache*
    - **Cache hit**: Get data directly from ElastiCache
    - **Cache miss**: Data is not in the DB, application reads from DB and writes to the cache
  - Cache must have an ***invalidation strategy*** to make sure only the most current data is used in there.
    - **Cache invalidation**: whether or not the cache is out of date.
  - Patterns for ElastiCache
    - **Lazy Loading**: all the read data is cached, data can become stale in cache
    - **Write Through**: Adds or update data in the cache when written to a DB (no stale data)
    - **Session Store**: store temporary session data in a cache (using TTL features)
- ❗ None of the caches support IAM authentication
  - IAM policies are only used for AWS API-level security, such as create a cache, delete a cache etc.
- Managed and Scheduled maintenance with SNS notifications
  - **Subnet group** allows you to specify set of subnets in your VPC for your instances.
- 💡 Use case: Key/Value store, Frequent reads, less writes, cache results for DB queries (writethrough pattern), store session data for websites, cannot use SQL.

## Memcached

- In-memory object store
- ❗ Cache doesn't survive reboots
- Memcached support SASL authentication
- Use cases
  - Quick retrieval of objects from memory
  - Cache often accessed objects

## Redis

- Most popular in-memory key-value store
- Super low latency (sub ms)
- Read Scaling using **Read Replicas**
  - Redis Clustering = Collection of one or more cache nodes
  - One of the nodes is a read/write primary node.
  - All other nodes in the shard are read-only replicas.
- Cache survive reboots by default (persistent)
- 💡 Use-cases: User sessions, Leaderboard (for gaming) with ***Redis Sorted Sets***, Distributed states, Relieve pressure on databases (such as RDS), messages with ***Redis Pub/Sub***, recommendation data with ***Redis Hashes***
- Availability: ***Multi-AZ*** with Auto-Failover and enhanced robustness
- Cluster mode -> more persistence and robustness with specified number of read replicas.
  - Sharding through clustering
- **Security**
  - **Network**: Deploy into subnet, set preferred availability zone(s), security group
  - Encryption at rest (KMS) and in-transit (using SSL)
  - Redis support **Redis AUTH** (username / password with `--auth-token` parameter)
- Import data to cluster through seeding RDB file from S3
- Automatic back-up
- Persistence by snapshotting your Redis data using the Backup and Restore feature

## Redis vs Memcached

| | Memcached | Redis |
| -- | ------ | ----- |
| **Persistence** | Not supported | Supported through snapshots |
| **Authentication** | SASL | Redis Auth |
| **Sub-millisecond latency** | Yes | Yes |
| **Clustering** | Each node => Different partition | 1 master and read replicas of it |
| **Unique features** | Multi-threading | Snapshots, replication, transactions, pub/sub, lua scripting, geospatial support |
# Other databases

- **DocumentDB**
  - Document database with Mongo DB compatibility.
  - More hands on DynamoDB => Middle step between MongoDB and DynamoDB
- **Athena**
  - Fully serverless query engine (does not hold data) with SQL capabilities
  - Used to query data in S3 using Presto engine
  - Pay per query based on how much capacity you're using, how much capacity you're analyzing etc...
  - Output results back to S3
  - Secured through IAM
  - 💡 Use Case: • One time SQL queries for e.g. exploratory work, • Serverless queries on S3, • Log analytics • Lightweight queries (not too complicated / not too many joins).
- **Neptune**
  - Fully managed graph database
  - Highly available across 3 AZ ***(Multi-AZ)*** , with up to 15 read replicas ***(Clustering)***
  - Point-in-time recovery, continuous backup to Amazon S3
  - Support for KMS encryption at rest + HTTPS
  - 💡 Use-cases: • High relationship data • Social networking: e.g. Users friends with Users, replied to comment on post of user and likes other comments • Knowledge graphs (e.g. Wikipedia)
- **ElasticSearch**
  - Indexes & allows you to search any field, even partially matches in unstructured data.
  - It's common to use ElasticSearch as a complement to another database
  - ElasticSearch also has some usage for Big Data applications
  - You can provision a cluster of instances
  - Built-in integrations: Amazon Kinesis Data Firehose, AWS IoT, and Amazon CloudWatch Logs for data ingestion
  - Security through Cognito & IAM, KMS encryption, SSL & VPC
  - Comes with Kibana (visualization) & Logstash (log ingestion) - ELK stack
