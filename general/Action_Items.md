## Action items

* Checkout Amazon QuickSight for visualizations - they have added support for dashboard embedding and APIs
=> https://aws.amazon.com/about-aws/whats-new/2018/11/amazon-quickSight-adds-support-for-dashboard-embedding-and-APIs

* Can we get memory used / max mem in each service container so that we can better tune memory limits in task definitions? We get CloudWatch metrics for memory utilization on a per service basis with ECS, right?

* Start using AWS Secrets Manager for all RDS credentials used by applications so we get automated secret rotation (Secrets Manager integrates with RDS to update both clients and database server with new credentials)

* Start using S3 VPC endpoint for backend services that read/write to S3 (so requests don't go over open internet)

* Understand if we use single master KMS key, or if we create multiple encrypted keys used by various services.  Consider using application-specific encryption keys for each service that needs to perform encryption/decryption.

* Evaluate VS Code IDE - strongly consider switching from Sublime to VSCode

* Checkout "AWS Toolkit for VS Code"

* Check out the "Now Go Build" video series on AWS YouTube channel

* Add distributed tracing to our applications. Consider: AWS X-Ray, NewRelic.

* Start playing around with Cloud Formation - can we use it to bring up ECS cluster?

* Drift detection via AWS Config alerts - seems very useful for KBS (need to start using CloudFormation)

* Play around with SSM sessions

* Start using Secrets Manager, play around with workshop on Github [https://github.com/aws-samples/aws-secretsmgr-workshop]

* Stop using environment variables to configure CLI -- use profiles via `aws configure` instead

* Start using auto complete for AWS CLI
-> $ complete -C aws_completer aws

* Check out Firecracker [https://aws.amazon.com/blogs/opensource/firecracker-open-source-secure-fast-microvm-serverless/]

* Check out gremlin.com/community - "Gremlin with AWS / ECS" (enterprise trial: gremlin.com/break-containers)

* Understand redirects in ALBs

* Research Server Name Indication (SNI) for ALB

* Check out AWS Budgets

* Understand "dynamic references" in CloudFormation templates

* SSH into EC2 to see rotating credentials (from EC2 role)

* Verify/understand how you can attach multiple secondary CIDRs to your primary CIDR to expand VPC size

* Review the new "serverless lens" of the Well Architecture Framework

* Do a deep dive on Kinesis


## New guidelines

* Start favoring Aurora over Postgres

* Deprecate use of Classic ELBs

* "You can't legislate against failure, focus on fast detection and response" ~ chris pinkham

* We need to do better with observability of our software; need to add distributed tracing to our applications. Observability should consist of:
    1. application logs
    2. CloudWatch metrics
    3. distributed tracing


## Resources

* Book - "Release it, 2nd ed" - Michael Nygard
* Book - "Drift into Failure" - Sydney Dekker
* Book - "Chaos Engineering" - O'Reilly

* Paper - "The Network is Reliable" - ACM Queue 2014 - Bailis
* Paper - "Paxos Made Simple" - Leslie Lamport - 01 Nov 2001

* Tool - Netflix "Spinnaker" project for deployment
