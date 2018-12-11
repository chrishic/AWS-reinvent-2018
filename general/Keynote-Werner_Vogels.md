Keynote - Werner Vogels
- Thursday Nov 29th @ Venetian

############
############

He starts off his keynote by describing his worst day at Amazon... 

12/04/2004
- that was the order deadline date for orders with free super-saver shipping to be delivered by Christmas
- OracleDB was used for storing orders, items, and customers
- OracleDB went down for 12 hours because of a database bug

After the dust settled, they decided to take the following actions to reduce "blast radius" of future outages:
1. Sharding - at the application layer
2. Cell based architecture - each cell has its own app + persistence layer (think region->AZ->service cell)

They realized RDBMS is not designed for the Internet/cloud. They needed:
- sharding
- shared nothing clusters
- shared disk

5 typical layers of functionality provided by RDBMS:
1. SQL
2. TRANSACTIONS
3. LOCKING
4. CACHING
5. LOGGING

=> He then spent a fair amount of time discussing Aurora and how its architecture is specifically built for Internet demands.

Aurora:
- shared storage volume (spread over 3 AZs, each AZ has 2 storage nodes)
- this shared storage volume provides LOCKING and LOGGING, so the RDBMS now only needs to provide 3 layers of functionality: SQL, TRANSACTIONS, CACHING

Quorums for distributed systems:
- Membership set of 3 is not enough, that's why Aurora uses 6 (2 nodes in each of 3 AZs)

MTTR - Mean Time to Recovery
- need to recover very fast before next failure happens
- need to be able to replicate data to new nodes very quickly

Typical MySQL DB does 5 write operations for each write request:
- write to primary (EBS)
- write to primary (EBS mirror)
- write to secondary (EBS)
- write to secondary (EBS mirror)
- write log to S3

Aurora only needs to use log to create replicas:
- Log sequence number (LSN) can inform read operations (based on which storage nodes have acknowledged data)

############
############

DynamoDB
-----------

As part of the 12/04/2004 post-mortem analysis, they realized that the information stored in the RDBMS had the following characteristics:
- 70% was single table, single row
- 20% was single table, multiple rows
- Only remaining 10% involved multiple tables
=> "Internet data"!

DynamoDB whitepaper was published in 2007.
- First version of DynamoDB launched in 2012

Four pillars of DynamoDB:
1. Performance at scale
2. Fully managed
3. Comprehensive security
4. Built for modern applications

############
############

S3
-----------

When S3 launched in 2005, it was implemented as 8 microservices.
- today, it encompasses more than 235 microservices

S3 engineering organization has cultivated a culture of durability

Some key metrics that guide their engineering:
1. MTTF - mean time to failure (how often do they expect failures to happen)
2. MTTR - mean time to recovery (how long does it take to repair a failure and be back to normal)

With MTTR, they focus on the WORST CASE estimates (not mean or best case).

Total time to recover is an elastic property (controlled via scale up/down).

S3 is designed to support the loss of a data center:
- data is replicated over 3 AZs
- each AZ is composed of at least 1 data center (physical, independent building)

############
############

RedShift
-----------

11/01/2018 - replaced OracleDB with RedShift for all its analytics

Redshift is now 3.5x faster than it was 6 months ago.
=> That is a VERY dramatic performance gain that opens up new possibilities for analytics

############
############

NEW - "AWS Toolkit for VS Code"

############
############

AWS Lambda
-----------

Lambda functions can range from 128MB - 3GB RAM.

NEW - Ruby is now supported as a Lambda runtime language

NEW - "Custom Lambda Runtimes"
- bring your own runtime to Lambda
- add support for C++, Rust, PHP, Cobol, bash, etc...

NEW - "Lambda Layers"
- way of modularizing common/shared libraries
- extend runtime environment with custom libraries

NEW - "Nested Apps using Serverless App Repo"
- compose application architecture from building blocks

Lambda Step Functions allow for workflows with Lambda. Consist of:
- Sequential steps
- Parallel steps
- Conditional steps

NEW - Step Function service integrations
* AWS Batch
* ECS
* Fargate
* AWS Glue
* DynamoDB
* SNS
* and more...

NEW - WebSocket support for API Gateway

NEW - ALB support for Lambda

############
############

NEW - "Amazon Managed Streaming for Kafka"
- fully managed service for Kafka clusters

Kinesis consists of:
- Video streams
- Streams
- Firehose

############
############

Being "well architected"

Currently, customers typically engage with either AWS solution architects or partner solution architects to perform well-architected reviews of their platforms.

But demand for well-architected reviews is increasing, and supply of solution architects cannot keep up with growth. To give customers a "self-service" option, AWS is releasing the "AWS Well Architected Tool".

NEW - "AWS Well Architected Tool"
- measure and improve your architecture using well-architected best practices
- makes review self-service (instead of needing a solution architect)
=> feels basically like a table of contents with links to resources (like documents, FAQs, videos, workshops, etc)

############
############

Action items:
* Evaluate VS Code IDE - strongly consider switching from Sublime to VSCode
* Checkout "AWS Toolkit for VS Code"
* Review the new "serverless lens" of the Well Architecture Framework
* Do a deep dive on Kinesis; how is it different than Kafka?
* Check out the "Now Go Build" video series on AWS YouTube channel (also coming to Amazon Prime Video in future) - Werner Vogels goes around the world talking to customers about what they are building
