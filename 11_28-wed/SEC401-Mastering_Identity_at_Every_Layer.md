SEC401 - Mastering Identity at Every Layer of the Cake

Most workloads on AWS resemble a finely crafted cake, with delight at every layer. In this session, we help you master identity at each layer of deliciousness: from platform, to infrastructure, to applications, using services like AWS Identity and Access Management (IAM), AWS Directory Service, Amazon Cognito, and many more. Leave with a firm mental model for how identity works both harmoniously and independently throughout these layers, and with ready-to-use reference architectures and sample code.

Quint Van Deman - Principal BDM, Identity Svcs, AWS

############
############

Layers of identity:

1. AWS - access to management console, CLI/API
-> Builders

2. Infrastructure - OS, databases, others
-> Operators

3. Application - end user applications
-> Users

In practice, layers aren't really clearly defined though (more like tiramisu instead of a layer cake).

############
############

Understanding planes of access

Ex #1:
data plane - VPC connection (e.g SSH, RDP)
control plane - AWS API (e.g API call)

Ex #2:
data plane - Dynamo "GetItem"
control plane - Dynamo "CreateTable"

############
############

Challege: allow builders to be strongly authenticated/credentialed across a fleet of AWS accounts

Options:

1. SAML to IAM
Directory: you
IdP: you
Credential exchange: AWS

2. AWS SSO
Directory: you or AWS
IdP: AWS
Credential exchange: AWS

3. Custom Broker
Directory: you
IdP: you
Credential exchange: you

############
############

How to decide which option to choose?

1. Can you make decisons based on groups & attributes?
- If no, go with custom broker
2. You can make decisions based on groups & attributes. Do you have existing SAML?
- If yes, go with SAML to IAM  
3. No existing SAML.  Do you have less than 20 AWS accounts? 
- If yes, use AWS SSO
- Otherwise, go with SAML to IAM

############
############

IAM delegated admin
- certain IAM policy actions (e.g. PutUserPolicy, AttachRolePolicy) require full admin permissions
=> Use self-service role creation with guardrails

New feature:
Admin can grant IAM management permissions but specify "permission boundary"

Admin step 1: Create permission boundary (managed policy)
Admin step 2: Allow role creation, but only within boundary
Admin step 3: Allow dev to pass role ("iam::PassRole")
User step: app dev creates role with delegated permissions

############
############

CloudFormation macros for IAM policy creation:
https://github.com/awslabs/aws-cloudformation-templates/pull/115

############
############

For the infrastructure: IAM-based auth

Shell session:
$ aws ssm start-session --target <instance_id>

NOTE: doesn't require SSH/port 22 open.

############
############

$ aws rds generate-db-auth-token --hostname <hostname>

- Need to enable IAM database authentication with your RDS instance
- Also need to enable some grants to the Postgres user (see video for details)

############
############

You can use temporary IAM credentials for access such as:
- Systems Manager (SSM) session manager
- RDS IAM database connection
- Redshift IAM database connection

############
############

IAM roles:
- AWS credentials that are auto delivered and auto rotated

AWS Secrets Manager
- supports auto rotation of credentials

Secrets Manager workshop:
https://github.com/aws-samples/aws-secretsmgr-workshop

############
############

AWS Cognito
- JWTs for your APIs
- Secure Token Service (STS) for accessing AWS API

Clean integration with:
* API Gateway
* ALB

CUP token (Cognito User Pool)
STS token (Cognito Identity Pool)

############
############

Service-to-service authentication

1. Service-to-service in AWS using IAM
2. Service-to-service in AWS using Cognito (OAuth)
- store client_id/client_secret in Systems Manager; use Cognito to generate bearer token

############
############

Action items:
1. SSH into EC2 to see rotating credentials (from EC2 role)
2. Play around with SSM sessions
3. Start using Secrets Manager, play around with workshop on Github [https://github.com/aws-samples/aws-secretsmgr-workshop]
