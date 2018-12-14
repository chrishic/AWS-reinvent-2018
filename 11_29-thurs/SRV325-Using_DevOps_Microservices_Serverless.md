SRV325 - Leadership Session: Using DevOps, Microservices, and Serverless to Accelerate Innovation

In this session, learn how AWS can help you innovate faster with DevOps, microservices, and serverless. Join us for a rare and intimate discussion with AWS senior leaders. Hear them share development best practices and discuss key learnings from building modern applications at Amazon.com. Also, learn how developers can leverage containers, AWS Lambda, and developer tools to build and run production applications in the cloud.

Ken Exner - Director, Developer Tools
Deepak Singh - Director, Compute Services, Containers, and Linux
David Richardson - VP, Serverless (joined Amazon in 2006)

############
############

Amazon.com in 2001: monolithic application and teams
- basically composed of 2 applications: Obidos and ABD (Amazon BookDb)

In 2002: move to microservices and 2-pizza teams
- all about agility

2 pizza teams:
- full ownership
- full accountability
- dev-ops
- focused innovation

############
############

Changes to architecture patterns
- when impact of change is small, release velocity can increase

[Conway's law](https://en.wikipedia.org/wiki/Conway%27s_law)
- "organizations which design systems ... are constrained to produce designs which are copies of the communication structures of these organizations."

"Cloud-native architectures are small pieces, loosely joined"

############
############

Pattern #1: Request/respond API services
-----------

APIs are the front door of microservices
=> Manage APIs with API Gateway

Coming soon: WebSockets support in API Gateway
- realtime 2-way communication
- managed persistence
- event-based triggers

Front-end clients have stateful connections, go through API Gateway to make connections to back-end services which have stateless connections.

############
############

Pattern #2: Event-driven architectures
-----------

Decouple state from code with messaging

Queues - SQS
Pub/sub - SNS
Sync - CloudWatch events

############
############

Pattern #3: Data streams
-----------

Data stream capture
- Kinesis data streams
- DynamoDB streams

New: [AWS Lambda supports Kinesis Data Streams Enhanced Fan-Out for faster streaming](https://aws.amazon.com/about-aws/whats-new/2018/11/aws-lambda-supports-kinesis-data-streams-enhanced-fan-out-and-http2/)

############
############

Build workflows to orchestrate everything
- AWS Step Functions, now with more integrations

############
############

Microservices... isn't all of this very hard now that we have lots of pieces to operate?

############
############

What is serverless?
- no infrastructure provisioning, no management
- pay for value
- auto scaling
- highly available and secure

Serverless service landscape:
- Compute: Lambda, Fargate
- DB: DynamoDB, Aurora Serverless
- Storage: S3
- Messaging: SQS/SNS, Kinesis
- Analytics: Athena, ElastiSearch
- Integration: API Gateway

############
############

AWS Lambda

Make development easier with Lambda
- Support for all runtimes (like Ruby, C++, Rust) with Lambda Layers and Runtime API

Lambda Layers
- let functions easily share code - upload layer once, reference within any function
- promotes separation of responsibility

Custom Runtimes
- bring any compatible language runtime
- powered by Runtime API - codifies runtime calling conventions and integration points
- custom runtimes distributed as "layers"

############
############

Containers

Fargate
- running 10's of millions containers every week
- (*) Log drivers coming soon to Fargate

############
############

When building your containers, decouple operational logic and SDKs from your application logic.

I.e. code that does the following:
- monitoring
- routing
- discovery
- deployment
=> Deploy as sidecar next to container

############
############

New: [AWS Cloud Map](https://aws.amazon.com/cloud-map/)
- increase application availability
- increase developer productivity
- integration with ECS, EKS, Fargate

############
############

New: [AWS App Mesh](https://aws.amazon.com/app-mesh/)
- observability and traffic control
- works across clusters and containers
- leverages Envoy proxy for control plane
- easily deliver config and receive data
- 2-way communication (send intent/directive, receive reporting/metrics)

############
############

Firecracker
-> Created to address the question of "how do I run lots and lots of small containers securely?"

Design goals:
1. security
2. speed by design
3. scale and efficiency

############
############

FAQ: "How do we manage the release process for so many services?"

2-pizza teams - each team gets their own delivery pipeline

How Amazon does dev-ops:
1. decompose for agility (microservices, 2-pizza teams)
2. automate everything
    - internally, Amazon built tool called "Pipelines" which was their CI/CD platform
    - unit tests, integration tests, load tests, synthetics
3. standardized tools
4. belts and suspenders (governance, templates)
    - developed templates for pipelines to make it easier for teams to adopt best practices
    - inspect pipelines to determine if they were following best practices
5. infrastructure as code

############
############

AWS Dev Tools for CI/CD:
* Source code: CodeCommit
* Build: CodeBuild
* Test: CodeBuild + 3rd party
* Deploy: CodeDeploy
* Monitor: X-Ray

############
############

AWS CodePipeline is orchestrator for: Source, Build, Test, and Deploy.

CodePipeline
- supports AWS Config for improved governance
- now supports ECR as source
- now supports blue/green deploys for Fargate/ECS with CodeDeploy

############
############

[AWS X-Ray](https://aws.amazon.com/xray/)
- distributing tracing tool for modern applications
- analyze/debug issues quickly
- end-to-end view of individual services
- identify customer impact: [X-Ray Adds the Ability to Group Traces by Root Cause](https://aws.amazon.com/about-aws/whats-new/2018/12/aws-xray-adds-the-ability-to-group-traces-by-root-cause/)
- support for serverless: new support for API Gateway

############
############

FAQ: "How can I best model and provision my architecture?"

AWS Cloud Development Kit (CDK)
1. provides language bindings for CloudFormation
- i.e. you can now author CloudFormation in Python code
2. higher-level set of components (constructs)
- opinionated patterns for how to implement things

Two options/techniques:
1. AWS Serverless App Model (AWS SAM)
2. Container Constructs for CDK

############
############

Action items:
* Check out [ECS secrets management via SSM Parameter Store](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/specifying-sensitive-data.html)
* How do we send logs to SumoLogic when running in Fargate? See: https://github.com/aws/containers-roadmap/issues/39
* Start using [AWS X-Ray](https://aws.amazon.com/xray/)
* Consider switching to Cloud9 or VS Code + AWS Toolkit (to make it easier to author/debug Lambda)
* Amazon CDK -- get on it!
