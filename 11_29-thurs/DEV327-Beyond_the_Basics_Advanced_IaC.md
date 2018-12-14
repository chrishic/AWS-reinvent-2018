DEV327 - Beyond the Basics: Advanced Infrastructure as Code Programming on AWS

In addition to the basic infrastructure as code capabilities provided by AWS CloudFormation, AWS now offers various programmability constructs to power complex provisioning use cases. In this talk, we present several advanced use cases of declarative, imperative, and mixed coding scenarios that cloud infrastructure developers can leverage. Examples include demonstrating how to create custom resources and leveraging transforms, like the AWS Serverless Application Model (AWS SAM), to create both simple and complex macros with AWS CloudFormation.

Luis Colon - Senior Developer Advocate, AWS CloudFormation
Chuck Meyer - Sr. Dev Advocate - AWS CloudFormation

############
############

Agenda:
- Declarative vs imperative coding
- Deep dive: AWS CloudFormation Macros
- Deep dive: Imperative AWS CloudFormation

############
############

Declarative: saying *WHAT* you want
- pros: barrier to entry is low (less knowledge required than with higher level languages)
- cons: no support for full execution control, looping, or advance techniques like object-oriented inheritance, threading, etc.

Imperative: saying *HOW* to do it
- cons: barrier to entry is relatively high (need to know language syntax, different API libraries, etc.)
- pros: added flexibility (for example, able to leverage language-specific editors)

############
############

CloudFormation: Declarative programming

- YAML/JSON used to "declare" the desired state
- some imperative programming is possible
    - via `user_data`
    - functions like `Fn::Condition`, `Fn::FindInMap`

############
############

CloudFormation Programming Options

Declarative:
* basic YAML/JSON
* basic transforms
    - Include
    - SAM
* advanced transforms
    - macros
    - others: Templating frameworks such as Jinja and Mustache

Imperative:
* AWS Cloud Development Kit (CDK) (Typescript)
* Troposphere (Python)
* SparkleFormation (Ruby)
* GoFormation (Go)

In all cases, we are still generating JSON/YAML vs direct API calls
- leverage stabilization, dependency graphs, rollbacks, etc

############
############

CloudFormation Macros
- implemented as Lambda functions
- shorthand abbreviated instructions that expand automatically when deployed
- add utility functions (e.g loops, strings)
- ensure resources are defined to comply to your standards
- easy to share/reuse across stacks (macros get their own ARNs)

Key benefit: once deployed, macro users can be isolated from all imperative programming details

############
############

The presenters then showed code samples showing how to implement the following constructs using CloudFormation macros:
1. Iterator
2. Execute arbitrary Python
3. Add string functions (lowercase, uppercase, substr, etc)
4. Global variables

* For walkthoughs of the examples, watch from 15:12 through 27:55 of the [video recording](https://www.youtube.com/watch?v=EJVNuR2GRBc)

############
############

Imperative programming options

Hierarchy:
Resources -> Components -> Stacks -> Environments -> Application

* AWS Cloud Development Kit (CDK) (Typescript)
- modeling infrastructure programmatically
- componentized

* Troposphere (Python)
- mature project on Github
- generates both JSON and YAML

* SparkleFormation (Ruby)

* CloudForm (Javascript / Typescript)

* GoFormation (Go)

############
############

Action items:
* Start playing with [AWS Cloud Development Kit](https://github.com/awslabs/aws-cdk) - our IaC story should be CloudFormation driven by CDK code
