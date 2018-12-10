DEV321 - What's New with AWS CloudFormation

AWS CloudFormation is one of the most widely used tools in the AWS ecosystem, enabling infrastructure as code, deployment automation, repeatability, compliance, and standardization. In this session, we cover the latest improvements and best practices for AWS CloudFormation customers in particular, and for seasoned infrastructure engineers in general. We discuss new features and improvements that span many use cases, including programmability options, cross-region and cross-account automation, operational safety, and additional integration with many other AWS services.

Manu Suresh - Software Development Engineer
Luis Colon - Senior Developer Advocate, AWS CloudFormation
Anil Kumar - Sr. Product Manager

############
############

They have redesigned the AWS CloudFormation console to have a better UX/UI
- launched a few weeks ago
- responsive across various screen sizes
- designed to accomodate forthcoming new features
- can switch back to previous version

CloudFormation resource types support:
- over 300 resources supported
- added 65 new resource types this year alone
- goal to have "day one" support for services as they are released

Extensibility: Custom resources
- create/specify via AWS::CloudFormation::CustomResource or Custom::String
- used to define custom resources in your templates
- include resources that are not included in the CloudFormation resource types

############
############

What about native support for non-AWS resource types?
-> Alexa::ASK::Skill - creates an Alexa skill that enables customers to access new abilities

Use infrastructure as code (IaC) techniques to build/operate Alexa skills:
- Use CloudFormation features to create/update skills
- Access other related AWS resources to support Alexa skills

############
############

Managing enterprise complexity
- Detect config drift
- VPC Private Link support
- New StackSets enhancements
- Seamless handling of secrets

############
############

Drift detection
- allows you to detect if config changes were made to the stack resources outside of CloudFormation via console/API/SDK
- use the diff viewer in the console to pinpoint the changes
- available via CLI/API
- supports most commonly used resources (51 types)
- auto drift alerts via AWS Config rule
- remediate by updating live config values to match template values
- looking ahead: support more resource types, prevent false positives, handling edge cases

############
############

AWS Private Link support
- keeps all network traffic within AWS network (without hitting internet)
- create CloudFormation endpoint

############
############

AWS accounts are becoming infrastructure boundary
=> need easier way to share CloudFormation stacks across accounts

StackSets extend functionality of stacks by enabling you to create/update/delete stacks across multiple accounts & regions with a single operation
- custom execution roles and admin roles
- more fine-grained control of stack instance updates
- extended limits
- ex: temporary accounts

############
############

Amazon.com use of CloudFormation

One of the primary use cases is to auto provision AWS accounts

Provisioning AWS accounts for development
- accounts are used by developers to create environments to test new features
- provides a secure, reliable environment for prototypes
- 100: average number of short-lived accounts created daily
- 375: highest number of short-lived accounts created in a day
- $1,000: max budget allowed (account auto closed when it reaches that limit)
- 2: number of active short-lived accounts that each developer can have at a time

Sandbox environments
- cost control: enforce budget limits on costs of resources for experiments
- isolation: experiments cannot run in same environment used for production
- auto clean up

Short-lived account policy requirements
- CloudTrail enabled on every account
- IAM roles in every account for audit, compliance and incident response
- AWS Budgets config in all accounts to prevent runaway spend
- SNS topic is endpoint for notifications of overspend from AWS Budgets

############
############

How to create short-lived accounts?

Before: they used workflows:
1. create new account
2. create cross-account role
3. enable CloudTrail
4. create read-only IAM role
5. create AWS Budget

Then to use, assume cross-acccount role on the newly created account; call AWS APIs to provision resources.

After: with StackSets
- each stack instance configs all of the following resources in the new account: CloudTrail, cross-account IAM roles, AWS Budgets

Steps:
- using CloudFormation StackSet Admin Role in admin account-
1. Assume execution role
2. Create stack instance

Two accounts:  admin account, new short-lived account

Developer productivity gains:
- 3x faster to deploy new AWS resource when policy requirements change
- 8x less code when provisioning through StackSets

############
############

Improved handling of secrets
- best practice: don't hardcode secrets or even config in CloudFormation templates
- recommendation (last year): support for Parameter Store String and String List params
- recommendation (this year): support for Secure String and Secrets Manager support

Example:
- store latest version of AMI as string in Parameter Store
- reference that string in CloudFormation template

New:
- dynamic references and secure strings

############
############

CloudFormation Macros:
- enables devs to write short-hand abbreviated instructions that expand auto when deployed
- add utility functions, iteration/looping, strings, and others
- ensure resources are defined to comply to your standards
- easy to share and reuse across stacks
- created/defined as Lambda functions
- deployed as Lambda function
- macros can be used as a wrapper for more extensive set of instructions

############
############

Faster code assistance with AWS CloudFormation Linter
- integrated with IDEs via plugins
- quick feedback on code errors, warnings
- robust validation powered by resource specification
- CloudFormation Linter is OSS - extend by building your own validation rules
- use in headless mode for automated testing in CI/CD

Download AWS CloudFormation Linter here:
github.com/awslabs/cfn-python-lint

```
$ pip install cfn-lint
```

############
############

Higher level language modeling
- When doing IaC with CloudFormation, you use declarative syntax
    - provides level of abstraction above API, CLI calls
    - more approachable
    - comes at the expense of not providing full language constructs (although macros close the gap)
- However domain-specific higher level language options exist:
    - more opportunities for coders of JS/TS, Python, Ruby

Levels of IaC:

1. Scripted
- automate with SDKs/CLI; step-by-step imperative instructions; error handling and state updates left to dev

2. Declarative
- CloudFormation (with and without macros)
- Terraform

3. DSLs
- Troposphere (Python), SparkleFormation (Ruby), GoFormation (Go)
- generates CloudFormation code

4. Imperative
- AWS CDK (in dev preview)
- Pulumi (3rd party)

############
############

AWS Cloud Development Kit (CDK)
- initially supports TypeScript
- Construct(s) [written in TS] -> run through compiler -> produce CloudFormation templates

Download the CDK here:
github.com/awslabs/aws-cdk

############
############

Community

Expect more OSS projects to improve the developer experience

#CloudFormation Slack channel
-> DM @luiscolon1 via Twitter to get added

############
############

Action items:
* Start playing around with Cloud Formation - can we use it to bring up ECS cluster?
* Drift detection via AWS Config alerts
* Check out AWS Budgets
* Understand "dynamic references" in CloudFormation templates
* AWS CDK!
