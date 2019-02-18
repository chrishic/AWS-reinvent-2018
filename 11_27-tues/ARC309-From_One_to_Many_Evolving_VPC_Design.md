ARC309 - From One to Many: Evolving VPC Design

As more customers adopt Amazon VPC architectures, the features and flexibility of the service are encountering the obstacles of evolving design requirements. In this session, we follow the evolution of a single regional VPC to a multi-VPC, multi-region design with diverse connectivity into on-premises systems and infrastructure. Along the way, we investigate creative customer solutions for scaling and securing outbound VPC traffic, securing private access to Amazon S3, managing multi-tenant VPCs, integrating existing customer networks through AWS Direct Connect, and building a full VPC mesh network across global regions.

Androski Spicer - Solutions Architect, AWS

############
############

VPC IP space design

Design/specify your CIDR range:
- don't overlap IP space with on-premise and cloud networks; otherwise, traffic will be dropped at the edge
- consider connectivity to corporate networks
- plan for expansion

/16 = 65,536 IPs
/24 = 256 IPs
/28 = 16 IPs

CIDR range for VPC can never be changed
=> therefore recommendation is start as large as possible

############
############

IPv6

/56 per VPC
/64 CIDR block per subnet

- IPv6 completely independent from IPv4
- Enabled per subnet or per ENI
- Wide support - supported by security groups, route tables, NACLs, VPC peering, Internet Gateway (IGW), Direct Connect, FlowLogs, DNS

############
############

VPC resizing

You can attach multiple secondary CIDRs to your primary CIDR to expand VPC size
- main route table is then updated to include routes to the secondary CIDRs

CIDR blocks CANNOT overlap

############
############

Subnet creation

- You want even distribution of IP space across AZs
- Use at least 2 AZs
- Subnets are AZ specific
- Need to answer questions of "how big?" and "how many?"

NOTE: For each subnet, 5 will not be available for resource use:
10.0.0.0 - network address
10.0.0.1 - reserved by AWS for CVPC router
10.0.0.2 - reserved by AWS
10.0.0.3 - reserved by AWS for future use
10.0.0.255 - network broadcast address

############
############

VPC subnet design

- put your subnets into one of 3 categories: 1. public, 2. private, 3. VPN-only subnet
- use security groups for isolation
- use tags for grouping resources
- use subnets as containers for routing policy
- consider large, mixed-use subnets
- traditional switching limitations do not apply

VPN-only subnet 
- not attached to a NAT; only allow routes back to on-prem via VPN

To get isolation at subnet level, you can use NACLs

############
############

Virtual Gateway (VGW)
- establishes secure connection between remote office and AWS VPC

############
############

NAT Gateway
- Still need Internet Gateway (IGW)
- AZ specific
- Requires Elastic IP (EIP)
- Burst to 10GB/s

############
############

VPC Endpoints (VPCe) give you private access to AWS resources

Two types of VPC endpoints:
1. Gateway (S3, Dynamo)
- added to route table
2. Interface (powered by AWS Private Link)
- accessed via DNS names

A big difference between the VPCe types: 
- interface VPC endpoint can exist for services outside of AWS

VPC endpoints can use IAM policy to enforce security.

############
############

Creating Interface VPC endpoints

Producer:
1. Create VPC endpoint service
2. Whitelist accounts for access
3. Associate VPC endpoint with NLB

Consumer:
1. Create VPC endpoint to service

############
############

VPCs

Why have many VPCs? why not one big VPC?

You should consider multiple VPCs when you need isolation, such as:
- separate PROD, DEV envs
- separate departments: LEGAL, MKTG, SALES
- for compliance with PCI, HIPAA

Hub & spoke VPC peering
- "shared services" model (VPC dedicated as "hub" to be used by remote offices)
- once you peer 2 VPCs, you've joined them into a single larger one

############
############

AWS Direct Connect (DX)
- public and private virtual interfaces (VIFs)
- virtual gateway allows access to VPC

Transit Gateway (NEW!)
- managed service
- regional, native AWS service
- central point that all VPCs communicate with to access another VPC
- no longer need VPC peers
- supports up to 10000 routes
- connects to on-prem via IPSec Tunnel only

Use cases:
- you have multiple VPCs and want to connect them (i.e. Layer 3 hub)
- edge consolidation - share common VPN across all VPCs
- can be peered across regions using AWS backbone

When you create Transit Gateway, it creates a network interface in each zone. You then specify what subnets you want attached to Transit Gateway.

You can whitelist other accounts using cross-account resource sharing APIs.

```
aws ec2 describe-transit-gateways
aws ec2 create-transit-gateway
aws ec2 create-transit-gateway-vpc-attachment
```

############
############

Action items:
* Understand "secondary CIDR" as way of expanding IP space of VPC
