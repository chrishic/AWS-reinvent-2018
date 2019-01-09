SRV375 - Lambda Layers and Runtime API

Lambda Layers and Runtime API are two new features which enable developers to publish and share libraries and runtimes that are compatible with AWS Lambda. Use of Layers allows separation of concerns. Publishers can build reliable and hardened software, and share it as Lambda Layers with other AWS developers to consume. Application developers can consume one or more layers in their functions which lets them focus on writing business logic. The Lambda Runtime API codifies the runtime calling conventions and integration points of a Lambda compatible runtime. By leveraging the API, developers can implement fully featured Lambda runtimes that support the Lambda programming model. In combination, these two features allow developers to share their runtimes with others as Layers, and allow function developers to seamlessly use them with their functions. In this session, Marco will deep dive into how to use the Layers and Runtime API capabilities to bring your own runtimes and libraries to Lambda.

Danilo Poccia - Principal Evangelist, Serverless

############
############

NOTE:  This was a last minute add to the session catalog (Lambda Layers and Runtime API were announced during Werner's Thursday keynote session, and this session wasn't added until after that keynote), and I was very lucky to get a reserved seat by checking the catalog for new sessions after the keynote.  This talk was given in a *tiny* room with only 70 chairs, with about 500+ folks lined up in the "walk up" line.  There was plenty of grumbling over the fact that such a tiny room was used for such a popular session.

PRO TIP: Use the re:invent mobile app to frequently check for session catalog updates, especially shortly after keynote sessions.

############
############

Lambda Layers
- centrally manage code/data shared across multiple Lambda functions

Use cases:
- custom code that is used by more than one function
- common libraries, modules, frameworks

Benefits:
- separate dependencies from custom business logic
- function code is smaller and more focused
- speed up deploys

############
############

Using Layers

- Put common components in zip file and upload as Layer
- Layers can be versioned to manage updates (each version is immutable)
- When a version is deleted or permissions revoked, existing functions that use the deleted version will continue to work but you won't be able to associate that deleted version to new functions
- A function can reference up to 5 layers (one of which can optionally be a custom runtime, such as Rust)
- When a function invoked, layers are installed in executable environment in the order you specified
    - overall uncompressed size of function and its layers is subject to usual unzipped deploy package size limit (256MB)
- Layer order is important!  Each layer is a zip file, and they are all extracted in same path (i.e. /opt) - each layer can potentially overwrite previous layer.
    - each runtime has environment variables for specific paths
        - e.g. Node.js = $NODE_PATH, Java = $CLASSPATH
- Layer approach can be used to customize environment
    - e.g. 1st layer can be custom runtime, and 2nd layers adds specific versions of libraries you need
- Layer storage space is applied toward the AWS Lambda Function per-region limit of 75GB
    - if you delete a layer (but is still being used by functions that reference it), then it still counts toward your storage allocation

############
############

Layer Permissions

Layers can be used within:
- AWS account
- shared between accounts
- shared publicly with the community

AWS is publishing a prebuilt/optimized public Layer which includes the `NumPy` and `SciPy` packages

Public layers will be counted toward storage limit (256MB per function)

Currently, there is no repository for layers
- if you want to use a public layer, you need to know the ARN

############
############

Layers API

Updated APIs:
- CreateFunction
- UpdateFunctionConfiguration

New APIs:
- ListLayers
- ListLayerVersions
- PublishLayerVersion
- DeleteLayerVersion
- GetLayerVersion (returns link for you to download layer)
- AddLayerPermission

############
############

Lambda Runtime API

- Simple interface to enable use of any programming language, or specific version of a language (such as unsupported version of Python), for your functions
- You can now select a "custom runtime" in the configuration of your Lambda function using the AWS console
- The function must include (in its code or in a layer) an executable file called "bootstrap"
    - the runtime bootstrap is responsible for communicating between your code and Lambda
    - your code can use any programming language
- Runtime bootstrap uses simple HTTP interface to:
    1. get event payload for new invoke
    2. return back response from function
- Custom runtimes can be shared as layers

############
############

Runtime API - Environment Variables

* `AWS_LAMBDA_RUNTIME_API` (hostname:port)
* `_HANDLER` (script_name.function_name)
* `LAMBDA_TASK_ROOT` (dir that contains fn code)

############
############

Runtime API - Processing Tasks

1. get an event
2. propagate tracing ID
3. create context object
4. invoke function handler
5. handle response
6. handle errors
7. cleanup

############
############

POST /runtime/init/error
GET /runtime/invocation/next
POST /runtime/invocation/{invocation_id}/response
POST /runtime/invocation/{invocation_id}/error

Example endpoint:
http://${AWS_LAMBDA_RUNTIME_API}/2018-06-01/runtime/invocation/next

############
############

Making a layer:

```
$ mkdir nodejs
$ cd nodejs && npm install moment
$ mv package.lock ..
$ zip -9r moment_layer/nodejs
```

Go to Lamdba console -> Layers -> Create layer -> upload zip

############
############

TIP:  In Lambda execution environment, you can use /tmp as cache.  But no guarantees when that state will be destroyed (i.e. you don't know when the Lambda platform will choose to run your Lambda in a different sandbox).

TIP:  If you have global objects in your Lambda function, they will persist between invocations (until sandbox is deleted).

TIP:  Have 2-3 layers at most.  If you have more than that, you start running into issues with launch time.

TIP:  Since you have 75GB limit per AWS region, consider creating multiple "layer" accounts to get beyond that limit. Or perhaps

############
############

Action items:
* Start using [AWS SAM CLI](https://github.com/awslabs/aws-sam-cli) for developing Lambda functions
* Research X-Ray for tracing (especially with Lambda functions)
