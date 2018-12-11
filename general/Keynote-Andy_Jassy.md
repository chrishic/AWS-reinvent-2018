Keynote - Andy Jassy
- Wednesday Nov 28th @ Venetian

############
############

About 50k attendees at re:invent 2018

AWS revenues:
* $27B revenue/yr
* 46% growth rate (down from 49%)

He spent a lot of time proving AWS is bigger, and better, than the other cloud providers (MS and Google).
- Breadth & depth (number of services, multiplied by sophistication of those services)
- They claim to have about 51% market share versus 13% for Azure.

############
############

Starting to see integration of ML into services.  Examples:
* S3 Intelligent Tiering
* ASG Predictive Autoscaling
=> build models based on cumulative data sets and apply to your workloads

############
############

AWS spent past year surveying customers on what they want/need, and identified 5 themes:
1. "Builders want it all, and they want it now."
2. "The right tools for EVERY builder" - first generation of tools for tinkerers, next generation wants more prescriptive solutions
3. "Freedom" - from old guard databases
4. "A little less conversation, and little more action please" - lots of progress to make ML more practical/useful vs theoretical
5. "Should I stay or should I go?" - transition from on-prem to the cloud

############
############

THEME 1 - "Builders want it all, and they want it now."

Storage Announcements:

1. Glacier Deep Archive (coming 2019)
- extremely cost-effective tape backup replacement - 1 TB/month for $1

2. FSx for Windows File Server
- fully managed file system for Windows

3. FSx for Lustre
- high throughput managed file system, for compute intensive workloads

4. EFS-IA
- EFS will now have two classes of service, EFS-Std and EFS-IA (for infrequently accessed file systems)

############
############

THEME 2 - "The right tools for EVERY builder" 
- first generation of tools for tinkerers, next generation wants more prescriptive solutions

Announced three tools to make common tasks easier:

1. AWS Control Tower
Easy way to setup and govern secure, compliant multi-account environments or Landing Zones.
=> Hmmm... sounds like CloudFormation templates & StackSets driven by macros

2. AWS Security Hub
Single GUI to centrally manage security and compliance across all AWS environments.

3. AWS Lake Formation
Service that leverages ML that allows you to create a data lake in days (instead of the typical months). Helps automate typical tasks involved with building a data lake, like collecting data, tagging and storing in S3.

############
############

THEME 3 - "Freedom" 
- from old guard databases

Learning to fly... 
- move off of Oracle & SqlServer => MySQL // Postgres // MariaDB

Aurora is the FASTEST growing AWS service ever.

Aurora Global Database
- multi-region replication

NEW - "DynamoDB Read/Write Capacity on Demand"
- provisioning capacity can be tricky so AWS is launching "DynamoDB Read/Write Capacity on Demand"
- no more capacity planning, pay only for what you use
- ideal for unpredictable workloads

NEW - "Amazon Timestream"
- fast, scalable managed time-series database
- serverless
- 1000x faster and 1/10th the cost of relational database
- can handle trillions of daily events
- ideal for storing streaming data points from edge devices

Blockchain has seen a lot of hype, and cryptocurrency has made it more complicated.  In talking with customers about blockchain, they really see two common use cases:
1. Ledgers with centralized trust - append-only, immutable ledgers
2. Transactions with decentralized trust

NEW - "Amazon Quantum Ledger Database" (QLDB)
- fully managed ledger database with centralized trust authority
- immutable
- crytographically verifiable

NEW - "Amazon Managed Blockchain"
- supports Hyperledger Fabric (now) and Ethereum (coming)

############
############

THEME 4 - "A little less conversation, and little more action please" 
- lots of progress in the past 12 months to make ML more practical/useful vs theoretical

NEW - "Optimized TensorFlow"
- AWS optimized version of TensorFlow to speed/efficiency
- optimized for P3 instances

Two big parts to ML:
1. Training
2. Inference (prediction)
=> running inference in production is the majority of the total cost of ML

NEW - "Amazon Elastic Inference"
- attach Elastic GPU to any EC2 instance
- how it works: provision the Elastic GPU in your VPC, and then attach the Elastic GPU to any EC2
- capacity starts at 1 teraflops, up to 32 teraflops
- leads to 75% reduced cost (compared to running P3 instance)

NEW - "AWS Inferencia"
- new custom chip specialized for ML
- hundreds of TOPS
- coming in 2019

SageMaker brings ML to all devs; more than 10k customers currently using SageMaker

NEW - "Amazon SageMaker Ground Truth"
- service for labeling training data
- can use automatic labeling or human labeling
- can specify thresholds of when to automate vs use human (i.e if confidence of automatic labeling is greater than 80%, than use that - if less, then resort to human labeling)
- feels like this is simply a workflow product

NEW - "AWS MarketPlace for ML"
- for selling models and algorithms in the AWS marketplace

Three broad types of learning in ML:
1. Supervised learning - e.g. computer vision, speech
2. Unsupervised learning - e.g. anomoly detection
3. Reinforcement learning - for situations where there is no "right" answer, i.e. best way to clear board in Pac-Man (many solutions) - state space search with heuristics

NEW - "Amazon SageMaker RL"
- reinforcement learning models integrated with SageMaker
- fully managed

NEW - "Amazon Deep Racer"
- toy, self-driving car, 1/18th scale
- build model in the cloud using SageMaker RL
- deploy model to physical car
- kicking off "Deep Racer League", series of races/competitions held at AWS Summits throughout 2019, with finals at re:invent 2019
 
NEW - "Amazon Textract"
- OCR++
- scan paper documents intelligently (like medical and legal forms)
- can handle formatting that typical OCR cannot, like embedded tables and multiple columns

NEW - "Amazon Personalize"
- service that leverages ML to deliver personalization and recommendations

NEW - "Amazon Forecast"
- service for accurate time-series forecasting

############
############

THEME 5 - "Should I stay or should I go?" 
- transition from on-prem to the cloud

Yes, it's a lot of work to move from traditional apps on-premise, but the longer you wait, the more difficult the transition will be.

VMWare is a key partner to help bridge the gap from on-prem to cloud
=> "VMWare Cloud" on AWS

NEW - "Amazon Outposts"
- AWS infrastructure in your data center
- you place order for racks - same hardware used by AWS
- two software options: 1. VMWare Cloud on AWS, 2. native AWS
- coming in 2019

############
############

Action items:
* Start leveraging Aurora more - it's a much better version of Postgres
* Check out "Amazon Personalize" - can we use this to recommend new wines to try?
