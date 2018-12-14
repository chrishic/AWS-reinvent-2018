SRV305 - Inside AWS: Technology Choices for Modern Applications

AWS offers a wide range of cloud computing services and technologies, but we rarely state opinions about which services and technologies customers should choose. When it comes to building our own services, our engineering groups have strong opinions, and they express them in the technologies they pick. Join Tim Bray, Senior Principal Engineer, to hear about the high-level choices that developers at AWS and our customers have to make. Here are a few: Are microservices always the way to go? Serverless, containers, or serverless containers? Is relational over? Is Java over? The talk is technical and based on our experience in building AWS services and working with customers on their cloud-native apps.

Tim Bray - Senior Principal Engineer

############
############

"Modern app" principles:
1. security/compliance first
2. use microservices
3. serverless where possible
4. CI/CD
5. monitor, monitor, monitor!
6. databases
7. integration interfaces

############
############

Architecture overview: SQS implementation

SQS is implemented via 4 primary microservices + DynamoDB:

1. Front end
- handle incoming messages

2. Metadata service

3. Back end
- clusters of workers that host queues

4. Load manager
- assigns/moves queues from backend clusters when they get hot

5. DynamoDB
- tracks which queues are on which clusters

fyi: They started off using OracleDB, but then moved to DynamoDB
=> migration was done lazily (start with all OracleDB, then move to mix, then all DynamoDB - abstract via interface/API)

############
############

Why serverless?
1. Cost/frugality
2. Security
3. Elasticity
4. Better design? (debatable, maybe, but probably not poorer design)

Note that items 1-3 are basically business reasons for adopting.

=> Goal of serverless: "all the code you ever write is business logic" i.e. don't perform the undifferentiated heavy lifting

############
############

Amazon MQ
- managed version of Apache ActiveMQ

Control plane (completely serverless): API Gateway, Lambda, DynamoDB
Data plane: ActiveMQ

A few other AWS services with serverless control planes:
* Sagemaker
* AWS Batch
* AWS Elemental
* AppSync
* IoT Core
* GuardDuty
* EKS
* API Gateway

############
############

Reasons for not using serverless

- What about latency?

When talking about latency, talk about scope of impact:
P50 - half of requests
P90 - 90% of requests
P99 - 99%
P100 - 100%

Sources of serverless latency:
- runtime startup
- state hydration

Need to consider:
- cold startup vs warm startup
- serverless platform startup vs language runtime startup
- P50 vs P90 vs P99 vs P100

############
############

Latency mitigation techniques

Mitigation technique 1: keep functions warm
- bummer that customers have to do this (expect AWS will fix by next year)

Mitigation technique 2: programming languages
- Java typically much slower (mostly due to class loading, not so much because of JVM)
    - Go (compiled), NodeJS, Python are typically faster than Java
- Tim doesn't recommend NodeJS (mostly because he doesn't like `npm` and its model of pulling in dependencies)
- Tim prefers Go language for Lambda implementation

Mitigation technique 3: state hydration (i.e. state that must be loaded before function can do its work)
- Example: Thomson Reuters application for transcoding video
    - Baseline: Transcode the entire length of video in a single operation
        - Problems: delay in loading entire video, then lengthy operation to transcode in single operation
    - Improvement: Divide and conquer
        - Divide the video into small chunks broken on key frames
        - Store chunks in S3
        - S3 put event triggers Lambda function that transcodes chunk
- Consider using lower latency sources for state (like ElastiCache, Kinesis, SNS, SQS, etc)

=> Sometimes cure for latency can be worse than the disease of latency itself.

############
############

Databases in Modern Apps
- RDBMS (RDS)
- NoSQL (DynamoDB)
- Amazon Quantum Ledger DB (QLDB) - Amazon uses this when they need transactions

############
############

Q: Relational or not?
A (AWS/Amazon answer): NOT

For the most part, when designing systems, AWS/Amazon opt for non-relational databases.  In almost every situation, a non-relational database can be used instead of RDBMS.

############
############

Amazon Quantum Ledger DB (QLDB):
* Transactions are SQL-ish
* Query the Summary with SQL
* "Records" are [AWS Ion](http://amzn.github.io/ion-docs/) documents (JSON superset)
* Journal is cryptographically chained immutable ledger
* Journal is also database table
* Serverless

Transactions -> Journal -> Summary

############
############

Containers

Some AWS services that use containers:
* Sagemaker
* Rekognition
* Batch
* Elemental
-> They are almost exclusively on ECS (but looking at using Fargate more).

############
############

Containers and serverlessness
- orthogonal in theory

VM: Turtle
Container: Donkey
Function: Cheetah

=> These distinctions are getting blurred. For example, VMs can spin up EXTREMELY fast with Firecracker.  Docker can be optimized for quicker container spin up.

############
############

Integration patterns:

1. APIs
- tightly coupled, contract-driven, synchronous, fast

2. Orchestration
- long-lived, data-driven, durable, unified logging
=> AWS Step Functions
Example: Use Step Functions to compose various implementation options
Encoder decider -> Use ECS or Lambda? -> If Lambda, invoke and fire success/fail -> If ECS, run ECS task -> Wait for complete -> Fire success/fail

3. Eventing
- loosely-coupled, data-driven, asynchronous, buffered, durable
    - Lambda, SNS, CloudWatch events

############
############

How StepFunction connectors work

Example:  Using Step Functions to coordinate Glue jobs (AWS Glue requests are async - fire & forget, so we need a way of "waiting" in our Step Function)

Flow looks like:

1. From Step Function, issue Glue `StartJobRun` async API call, and then start polling SQS queue for completion notification
2. When Glue is done, it fires Glue State change event to CloudWatch
3. Create an event-matching rule for the CloudWatch event to then send message to our SQS queue to notify
4. Blocked Step Function uses `RetrieveMessages` polling API call to then get notification to proceed

NOTE:  Turned an async flow into a synchronous flow from aspect of Step Function caller, and it is implemented COMPLETELY with async processing.

############
############

Action items:
* Start looking at Go (for Lambda functions)
* When making database decisions, think long and hard about RDBMS vs DynamoDB - in general, DynamoDB should be the usual choice
* Event-driven computing is the important architecture pattern for the future
* Look into CloudWatch events as message bus
* Research and experiment with AWS Step Functions for coordinating orchestration
