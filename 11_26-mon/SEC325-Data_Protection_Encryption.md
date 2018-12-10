SEC325 - Data Protection: Encryption, Availability, Resiliency, and Durability

Protecting data means ensuring confidentiality, integrity, and availability. In this session, we discuss the full range of data protection capabilities provided by AWS along with a deep dive into AWS Key Management Service (AWS KMS). Learn about data protection strategies for ensuring data integrity and availability using AWS native services that provide durability, recoverability, and resiliency for customer data on AWS. In addition, learn how to define an encryption strategy to protect data cryptographically, including managing KMS permissions, defining key rotation, and best practices for using the AWS Encryption SDK with KMS for custom software development.

Ken Beer - General Manager - Key Management Service, AWS
Peter M. O'Donnell - Solutions Architect, AWS

############
############

Tenets for data protection:
* Confidentiality
* Integrity
* Availability

"Least privilege" as a security best practice
- Start with the minimum set of privileges, and then grant additional ones as necessary

############
############

AWS IAM

Think of IAM in terms of "noun, verb, object", e.g. identity is the noun, API action is the verb, and resource is the object.

IAM components:

1. Identities
- Identity of actors, known as principals
- IAM can manage identities or you can federate through your own Identity Provider (IdP)

2. API Actions

3. Resources

4. Conditions
- For example: time of day, originating IP

IAM user policy:
- Effect:  Allow/Deny
- Action:  API calls
- Resource:
- Condition:

IAM resource policy:
- Effect:  Allow/Deny
- Principal:
- Action:  API calls
- Resource:
- Condition:

############
############

Access control of data has different characters vs. access control of resources (which have ARNs):
- Management of resource actions via AWS IAM
- Snapshots are for durability in-region, snapshot copies for durability out of region
- Maintain copies of data in separate AWS accounts for maximum isolation
- Access to data itself governed by database; e.g. MySQL/Postgres can integrate with IAM to generate/use credentials
- Essential to carefully manage database credentials - including rotation

############
############

Protecting data with AWS Secrets Manager

AWS Secrets Manager:
- integrated with IAM and services
- controlling access to secrets is often same as controlling access to data
	- too many humans handling secrets creates risk/vulnerability
- automated secret rotation provides security/availability
    - Secrets Manager integrates with RDS to update both clients and database server with new credentials

AWS Secrets Manager features:
* Rotate secrets safely
* Built-in extensible integrations with Lambda
* Scheduled, versioned rotation
* Fine-grained access control
* Encrypted storage
* Logging and monitoring
* Integrated with RDS out-of-the-box

############
############

Techniques for achieving data protection with AWS services:

Elastic Block Service (EBS)
* Confidentiality - tag-based IAM policies
* Integrity - block integrity is auto provided
* Durability - share snapshots between accounts and copy snapshots between regions

Elastic File System (EFS)
* Confidentiality - IAM policies for mounting; POSIX permissions for files & directories
* Integrity - file integrity is auto provided
* Durability - share snapshots between accounts and copy snapshots between regions

S3
* Confidentiality - IAM policies and bucket ACLs
* Integrity - Object integrity is auto provided
* Durability - S3 cross-region replication; versioning allows for recovery of deleted objects

############
############

Securing S3

Role - IAM policy
S3 VPC endpoint (VPCe) policy - policies directly attached to endpoint
S3 bucket - Bucket policy

You can enforce default encryption for S3 bucket:
- any object placed in this bucket must be encrypted with a specific key
- works with S3-managed or KMS master keys
- ensures encryption if the client calling S3 PUT command forgets to set encryption as header parameter

############
############

Encryption as data protection mechanism

Symmetric cryptography: AES-256, 3DES

With symmetric cryptography, you have a single key to encrypt/decrypt data.
- Need to protect your symmetric key
- Encrypt the symmetric key with a "master key" to produce an encrypted data key.

At some point, plain text keys need to exist somewhere (i.e you can't keep encrypting master key).

Where should you store your plain text master key?  Options include:

1. On-prem in your HSM
- Pros: you control the device, authentication, authorization; also, it looks familiar to auditors
- Cons: latency, availability, durability, scalability are all YOUR responsibility; limited integration with cloud managed services

2. In-cloud in your HSM (e.g. AWS Cloud HSM)
- Pros: you control authentication, authorization; lower latency to apps in the cloud; AWS manages the device, making it easier to have higher availability/durability/scalability; it looks familiar to your auditors (for example, during FIPS-142 audit reviews)
- Cons: limited integration with cloud managed services

3. In-cloud in managed HSM (e.g. AWS KMS)
- Pros: you control authentication, authorization; lower latency to apps in cloud; AWS owns all availability/durability/scalability; deep integration with cloud managed services
- Cons: looks unfamiliar to auditors

############
############

Security controls enforced by KMS

* When operational with key material provisioned:
- no AWS operator access to HSM; no software updates allowed

* After reboot and in a non-operational state:
- no key material on host
- software can only be updated when: 1) after multiple AWS employees have reviewed the code, 2) under quorum of multiple authorized KMS operators

* 3rd party verified evidence
- FIPS 140-2 level 2 overall with Level 3 physical security, design assurance, cryptography
- SOC 1 - Control 4.5: customer master keys used for cryptographic operations in KMS are logically secured so that no single AWS employee can gain access to key material

############
############

Each Customer Master Key (CMK) has a resouce policy to define permissions
-> leverages IAM policies for implementation

Sample permissions that can be applied to Customer Master Key:
- can only be used for encrypt/decrypt by <these users/roles> in <these accts>
- can be used by app A to encrypt and only used by app B to decrypt (quasi-asymetric key)
- can be managed only by this set of admin IAM users or IAM roles
- can be used by <these ext accts> but only for encrypt/decrypt, not admin tasks

############
############

Where to encrypt your data

1. Client-side encryption: encrypt data before you give it to an AWS service
- Can use AWS Encryption SDK to perform client-side encryption
- Where to get keys? On-prem, KMS, CloudHSM

2. Server-side encryption: ask AWS service to encrypt data after you upload it
- You tell service you want data encrypted with KMS key

############
############

New feature announcement: AWS KMS custom key store
- allows you to access keys stored in Cloud HSM via KMS
- to implement, AWS essentially created a "connector" that ties Cloud HSM to KMS endpoint, effectively allowing better cloud service integration for custom keys

KMS now has three options for key types:
1. AWS managed keys
2. Customer managed keys
3. Custom key stores

############
############

Summary:
* Data protection in AWS is mostly about access control; crucial to understand IAM, resource, and network VPC policies
* If you allow AWS to manage physical security of your keys in FIPS 140-2 validated HSMs, encryption and key management become yet another access control exercise.

############
############

Action items:
* Start using S3 VPC endpoint for backend services that read/write to S3 (so requests don't go over open internet)
* Understand if we use single master KMS key, or if we create multiple encrypted keys used by various services.
* Verify that EFS does provide snapshot feature. Can those snapshots be shared between accounts and copied to other regions (ala EBS)?
