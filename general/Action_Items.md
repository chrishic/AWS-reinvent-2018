## Table of Contents

<!-- MarkdownTOC depth=4 -->

- [Action Items](#action-items)
    - [Containers & Serverless](#containers--serverless)
    - [Admin, Configuration & Management](#admin-configuration--management)
    - [Observability](#observability)
    - [Infrastructure as Code](#infrastructure-as-code)
    - [Security](#security)
    - [Networking & Infrastructure](#networking--infrastructure)
    - [Development](#development)
    - [Data & Analytics](#data--analytics)
    - [General](#general)
- [Design Principles](#design-principles)
- [Resources](#resources)
    - [Articles](#articles)
    - [Books](#books)
    - [Papers](#papers)
    - [Tools](#tools)

<!-- /MarkdownTOC -->

---

## Action Items

### Containers & Serverless

* Can we get memory used / max mem in each service container so that we can better tune memory limits in task definitions? We get CloudWatch metrics for memory utilization on a per service basis with ECS, right?

* Check out [Firecracker](https://aws.amazon.com/blogs/opensource/firecracker-open-source-secure-fast-microvm-serverless/)

* Start looking at Go (for Lambda functions)

* Research and experiment with AWS Step Functions for coordinating orchestration

* Review the new "serverless lens" of the Well Architecture Framework

* Check out gremlin.com/community - "Gremlin with AWS / ECS" ([enterprise trial](gremlin.com/break-containers))

* How do we send logs to SumoLogic when running in Fargate? See: https://github.com/aws/containers-roadmap/issues/39


### Admin, Configuration & Management

* Play around with SSM sessions

* Start using Secrets Manager, play around with [Secrets Manager workshop on Github](https://github.com/aws-samples/aws-secretsmgr-workshop)

* Check out AWS Budgets


### Observability

* Add distributed tracing to our applications. Consider: AWS X-Ray, NewRelic.

* Start using [AWS X-Ray](https://aws.amazon.com/xray/)

* Look into CloudWatch events as message bus


### Infrastructure as Code

* Start playing around with CloudFormation - can we use it to bring up ECS cluster?

* Start playing with [AWS Cloud Development Kit](https://github.com/awslabs/aws-cdk) - our IaC story should be CloudFormation driven by CDK code

* Drift detection via AWS Config alerts - seems very useful for KBS (need to start using CloudFormation)

* Understand "dynamic references" in CloudFormation templates


### Security

* Understand if we use single master KMS key, or if we create multiple encrypted keys used by various services.  Consider using application-specific encryption keys for each service that needs to perform encryption/decryption.

* Start using AWS Secrets Manager for all RDS credentials used by applications so we get automated secret rotation (Secrets Manager integrates with RDS to update both clients and database server with new credentials)

* SSH into EC2 to see rotating credentials (from EC2 role)

* Check out [ECS secrets management via SSM Parameter Store](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/specifying-sensitive-data.html)


### Networking & Infrastructure

* Start using S3 VPC endpoint for backend services that read/write to S3 (so requests don't go over open internet)

* Understand redirects in ALBs

* Research Server Name Indication (SNI) for ALB

* Verify/understand how you can attach multiple secondary CIDRs to your primary CIDR to expand VPC size


### Development

* Evaluate VisualStudio Code IDE - strongly consider switching from Sublime to VSCode

* Checkout "AWS Toolkit for VS Code"

* Stop using environment variables to configure CLI -- use profiles via `aws configure` instead

* Start using auto complete for AWS CLI (`$ complete -C aws_completer aws`)


### Data & Analytics

* Checkout Amazon QuickSight for visualizations - they have [added support for dashboard embedding and APIs](https://aws.amazon.com/about-aws/whats-new/2018/11/amazon-quickSight-adds-support-for-dashboard-embedding-and-APIs)

* Do a deep dive on Kinesis


### General

* Check out the "Now Go Build" video series on AWS YouTube channel


## Design Principles

* When making database decisions, think long and hard about RDBMS vs DynamoDB - in general, DynamoDB should be the usual choice
    * When RDBMS is chosen, favor Aurora over Postgres

* Deprecate use of Classic ELBs

* Event-driven computing is the important architecture pattern for the future. Look for design opportunities to make systems more event-driven, and less request/response driven.

* We need to do better with observability of our software; need to add distributed tracing to our applications. Observability should consist of:
    - application logs
    - CloudWatch metrics
    - distributed tracing

* "You can't legislate against failure, focus on fast detection and response" ~ chris pinkham


## Resources

### Articles
1. [ACM interview with Werner Vogels](https://queue.acm.org/detail.cfm?id=1142065)

### Books
1. [Release it, 2nd edition](https://pragprog.com/book/mnee2/release-it-second-edition)
2. [Drift into Failure](https://www.amazon.com/Drift-into-Failure-Components-Understanding-ebook/dp/B009KOKXKY)
3. [Chaos Engineering](https://www.oreilly.com/library/view/chaos-engineering/9781491988459/)

### Papers
1. [The Network is Reliable](https://queue.acm.org/detail.cfm?id=2655736) - ACM Queue 2014 - Bailis
2. [Paxos Made Simple](https://www.microsoft.com/en-us/research/publication/paxos-made-simple/) - Leslie Lamport - 01 Nov 2001

### Tools
1. Netflix [Spinnaker](https://www.spinnaker.io/) project for deployment
