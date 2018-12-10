DEV322 - What's New with the AWS CLI

We're working on a new major version of the AWS Command Line Interface (AWS CLI), a command-line tool for interacting with AWS services and managing your AWS resources. AWS CLI v2 will include features to improve workflows and make it even easier to manage AWS resources through the AWS CLI. Come hear from the core developers of the AWS CLI as we highlight some of the new features and major improvements in AWS CLI v2.

Kyle Knapp - Software Development Engineer
James Sarywinnie - Senior Software Development Engineer

############
############

Some helpful options with AWS CLI commands:

--output table
--output text
--query {json expr}

############
############

Currently on V1
-> V2 is in active development

Major themes of V2:
- removal of features
- interactive usage
- high level commands

Backwards incompatible changes:
- Dropping support for python2.6, 3.3, 3.4

############
############

Getting Started - install/config

V1:
- Windows installer (MSI)
- Bundled installer
- pip

V2:
- Windows installer (MSI)
- Mac .pkg installer
- Linux installer

Benefits of new V2 approach to installation: 
- gives control over exactly what's installed
- easier getting started process for customers (by dropping pip, no longer need to have python/pip installed)
- expose extensibility through plugins

############
############

Configuration

```
$ aws config
```

Additional configuration options not handled by command-line:
- assume role
- MFA
- process provider (SAML)

Config improvements:
1. Amazon EC2 - no install or config needed
- AWS CLI auto included in Amazon linux
2. Local config
$ aws configure wizard

You can now configure your credentials using CSV download from console:
$ aws configure import --csv creds.csv

See all profiles configured:
$ aws configure list-profiles

See current profile configuration:
$ aws configure list

############
############

Interactive Usage
- think of AWS CLI V1 as the AWS SDK for bash
- many features to support scripting the CLI
- 150+ top level CLI commands
- 4900+ total CLI commands
- 26900+ total parameters

############
############

AWS CLI Wizards
- interactive prompting for values
- similar to web console
- manage AWS resources
- data-driven

$ aws dynamodb wizard <wizard-name>

$ aws iam wizard new-role

$ aws history show --include API_CALL

$ aws lambda wizard new-function

############
############

Discoverability

Auto-complete for V1:
$ complete -C aws_completer aws

Auto-complete for V2:
- improved performance via cached index
- resource value auto-completion
- over 5000+ completions for resource values

############
############

High Level Commands

Ex: "logs" command
$ aws logs tail <log group name> --short --follow
--follow just like "tail -f"

Ex: "ddb" command
$ aws ddb select <table name>
$ aws ddb put <table_name> <file>

############
############

Action items:
* Download/install CLI V2 [https://github.com/aws/aws-cli/tree/2.0.0dev0]
* Stop using environment variables to configure CLI -- use profiles via `aws configure` instead
* Start using auto complete for AWS CLI
-> $ complete -C aws_completer aws
* Check out "AWS Chalice" - for serverless apps
