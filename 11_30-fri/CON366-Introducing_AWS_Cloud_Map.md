CON366 - Introducing AWS Cloud Map

In this session we introduce AWS Cloud Map, a new service that lets you build the map of your cloud. It allows you to define friendly names for any resource such as S3 buckets, DynamoDB tables, SQS queues, or custom cloud services built on EC2, ECS, EKS, or Lambda. Your applications can then discover resource location, credentials, and metadata by friendly name using the AWS SDK and authenticated API queries. You can further filter resources discovered by custom attributes, such as deployment stage or version. AWS Cloud Map is a highly available service with rapid configuration change propagation.

Alexandr Moroz - Senior Product Manager
Mitchell Hashimoto - Founder and CTO, HashiCorp

############
############

NOTE:  Pretty impressive getting Mitchell Hashimoto to be one of the speakers for this breakout session.  Seeing his name on the speaker list was definitely the main reason why I wanted to attend this talk.

############
############

Problem: Complexity of modern architectures
- wide variety of resources
- complexity is increasing
- multiple versions and stages exist
- infrastructure grows exponentially
- unhealthy resources are replaced

Solution:  Service discovery
- Finding location of service provider

Example (service directory):
myapp: {ip1, ip2}

############
############

Two main patterns:
1. Server-side discovery
2. Client-side discovery

############
############

Server-side discovery pattern
- connections proxied
- discovery is abstracted away
- availability and capacity impact

############
############

Client-side service discovery pattern
- client queries service registry directly
- "service registry"

############
############

Examples of service discovery solutions:
- [etcd](https://coreos.com/etcd/)
- [Eureka](https://github.com/Netflix/eureka)
- [ZooKeeper](https://zookeeper.apache.org/)

############
############

[AWS Cloud Map](https://aws.amazon.com/cloud-map/)
- dynamic map of cloud
- convenient name for all cloud resources
- discover resources with specific attributes
- ensure only healthly resources are discovered
- use highly avilable DNS and regional API

Cloud Map Registry
- namespace (ex. "cloudmapdemo.com")
- service (ex. "backend")
- service instance: individual instances (set of IP addresses)

############
############

```
$ aws servicediscovery create-public-dns-namespace --name cloudmapdemo.com
$ aws servicediscovery create-service --name frontend
```

`create-http-namespace` for services without IPs (like DynamoDB)
=> these use/return ARNs instead of IPs

```
$ aws servicediscovery discover-instances --namespace-name shared --serviuce-name logs --query-parameters ready=yes
```
=> returns list of instances

Supports DNS resolution
```
$ dig +short A frontend.cloudmapdemo.com
```

############
############

Attribute-based discovery

When registering services, you can attach attributes and then query on these attributes when doing resolution
i.e. Stage=prod, Version=2.0, Ready=yes

############
############

Uses Route53 health checks for IP-based resources to ensure resources are healthy
- if check fails, resource is omitted from list of instances

############
############

Control traffic with custom health checks
- you control when to start/stop traffic routing
- create service with "HealthCheckCustomConfig"
- Use "UpdateInstanceCustomHealthCheck" API to set status to HEALTHY | UNHEALTHY

############
############

Cloud Map service integrations out of the box:
- ECS: service discovery via DNS and API (8 attributes auto registered)
- EKS
- AWS App Mesh
- Istio: Cloud Map Operator from Tetrate
- Consul

############
############

[Consul](https://www.consul.io/)
- most widely deployed service discovery solution in AWS today

Goal: extend service discovery to hybrid and multi-region
- Example:
    * Cloud Map in AWS
    * Consul for on-premise
    * Integration between the two environments

############
############

Action items:
* Question: how do we enable Cloud Map integration for ECS tasks - is this a property of the service definition? How do you specify attributes?
