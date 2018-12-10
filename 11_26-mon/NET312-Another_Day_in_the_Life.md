NET312 - Another Day in the Life of a Cloud Network Engineer at Netflix

Making decisions today for tomorrow's technology—from DNS to AWS Direct Connect, ELBs to ENIs, VPCs to VPNs, the Cloud Network Engineering team at Netflix are resident subject matter experts for a myriad of AWS resources. Learn how a cross-functional team automates and manages an infrastructure that services over 125 million customers while evaluating new features that enable us to continue to grow through our next 100 million customers and beyond.

Donavan Fritz - Cloud Network SRE, Netflix
Joel Kodama - Cloud Network SRE, Netflix

############
############

Inter-VPC Communication

Methods of inter-VPC communication:

1. Internet
- NO bi-directional private IP communication
- NO security group references
- NO bandwidth constraints

2. VPC peering
- allows bi-directional private IP communication
- allows security group references
- NO bandwidth constraints

3. AWS Direct Connect
- allows bi-directional private IP communication
- NO security group references
- HAS bandwidth constraints (i.e. 5 Gb interface)

############
############

Preferred solution:
- Use VPC peering
- AWS Direct Connect to provide private IP access to VPCs from office locations
=> provides for a single unified network across all accounts and regions

With VPC peers, you need to setup a route for each VPC peer.

############
############

They've created a python lib (cldnet) for doing infrastructure as code (IaC)

```
import cldnet
vpc0 = cldnet.create_vpc(account, region, name, prefix_length=16)
vpc1 = cldnet.create_vpc(account, region, name, prefix_length=16)
vpc0.peer_to(vpc1)
vpc0.resize(prefix_length=16)
```

=> Easily create full mesh of VPC peers within region with code.

############
############

VPC Inter-Region Communication

Methods of VPC inter-region communication:

1. inter-region VPC peering
- HAS network level encryption
- NO bandwidth constraints
- NO security group references
- summarizable inter-region VPC routes

2. AWS Direct Connect
- NO network level encryption
- HAS bandwidth constraints (i.e. 5 Gb interface)
- NO security group references
- NO summarizable inter-region VPC routes

############
############

Titus
=> Netflix container orchestrator built on top of Docker & Mesos; running millions of containers per week

Titus network requirements:
- IP per container (fidelity of IP address)
- fast deploys

Hierarchy of components:
	EC2 -> ENI -> Container -> IP

On container startup, need to make the following AWS API calls:
-CreateNetworkInterface
-AttachNetworkInterface
-AssignPrivateIPAddress
-ModifyNetworkInterfaceAttribute

=> Lots of overhead; Titus does pre-setup to allocate ENIs (but not assign IPs)

############
############

Possible solutions to dealing with having large numbers of IPs:
- Co-tenant
- Dedicated subnet
- Dedicated VPC
- Dedicated account (gives you IP isolation, AWS API isolation, ~295K IP addresses)

############
############

DNS service discovery

-> Netflix uses Eureka

Eureka:
- OSS project
- REST API
- Can mark services active or out of service (for blue/green deploys)
- Syncs its state with Route53, but they are running into scale problems

Possible solutions:

1. Current solution
- Auto remove stale DNS records
- Allow "out of service"
- NO protection against AWS API rate limits

2. Route53 auto naming
- Does NOT auto remove stale DNS records
- Does not allow "out of service"
- Provides protection against AWS API rate limits

Neither of those two possibilities were favorable, so they built their own custom DNS service => EurekaDNS.

############
############

EurekaDNS

Methods for implementing authoritative name server for static IP addresses:

1. Network load balancer (NLB)
- Static + IPv4
- NO IPv6, UDP

2. Elastic IP
- Static + IPv4 + UDP
- NO IPv6

3. ENI
- Static + IPv4 + IPv6 + UDP

DNS delegation hierarchy:
[service].[region].[env].[root]

############
############

DNS steering
- Netflix has service replicas in 3 different regions
- DNS lookups for www.netflix.com must resolve to one of those regions

Possible solutions:

1. Route53 geo-based routing
- little control over balancing of requests
- not optimized for latency

2. Route53 latency-based routing
- gives fastest response to clients
- BUT can still lead to unbalancing especially for regions in the "geographic middle"

3. Prefix steering
- route requests based upon network prefix (IP address) of CLIENT
- keep mapping of network prefix to DNS region on backend
- allows you to route clients to regions with higher latency, but still low enough such that user does not notice (i.e. user in NY routed to Europe region, instead of us-east region)
- gives you MUCH better control over balancing of requests

To help determine the effect of switching to latency-based routing, Netflix updated their client code to periodically "probe" (measure) latency to each of the available regions and report back to Netflix.

############
############

Action items:
* Check out Netflix Eureka and EurekaDNS
