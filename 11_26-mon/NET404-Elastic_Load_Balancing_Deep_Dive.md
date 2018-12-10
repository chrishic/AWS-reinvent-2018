NET404 - Elastic Load Balancing: Deep Dive and Best Practices

Elastic Load Balancing (ALB & NLB) automatically distributes incoming application traffic across multiple Amazon EC2 instances for fault tolerance and load distribution. In this session, we go into detail on ELB configuration and day-to-day management. We also discuss its use with Auto Scaling, and we explain how to make decisions about the service and share best practices and useful tips for success. Finally, Netflix joins this session to share how it leveraged the authentication functionality on Application Load Balancer to help solve its workforce identity management at scale.

Pratibha Suryadevara - GM, Load Balancing, AWS
Will Rose - Senior Security Engineer, Netflix

############
############

OSI Network Model

Layer 4 [Transport Layer]
- supports TCP
- incoming client connection bound to server connection
- no header modifications
- source IP address preserved in header or proxy protocol prepends source

Layer 7 [Application Layer]
- HTTP/HTTPS
- connection terminated at load balancer and pooled to backend server
- headers may be modified

############
############

Application Load Balancer (ALB)
- Layer 7
- content based routing allows request to be routed to different apps behind a single load balancer
- support for web sockets and HTTP/2
- Additional CloudWatch metrics
- Load balancer API deletion protection
- Improved performance for real-time and streaming applications
- Improved ELB API
- Native IPv6 (at ELB) - can have IPv4 on backend targets

############
############

API Model

Load Balancer =>  Listener0, Listener1, ...

Listeners route to target groups. Target groups can be:
1. EC2 instance
2. IP addresses
3. ECS service
4. Lambda function

Rules on listeners determine what target group to route to

IP as a target:
- use any IPv4 address from load balancer's VPC CIDR for targets within load balancer's VPC
- use any IP address from certain ranges to IPs in other VPCs and/or on-premise

############
############

Routing

1. Content-based routing
- Although you can use single ALB to host many services, as a best practice, consider using separate load balancers for each service (to reduce your blast radius if load balancer fails)

2. Redirects in ALB
- HTTP to HTTP
- HTTP to HTTPS
- HTTPS to HTTPS

3. Fixed response

############
############

"Slow start" feature
- allows adding new targets without overwhelming them with a flood of requests
- load balancer linearly increases number of requests sent to new target
- allows targets to warm up before receiving fair share
- NOT turned on by default
- useful for apps that depend on cache warming for optimal performance

############
############

Security with Load Balancers

Security facets:

1. TLS certificates
Two options for certificates:
	a. Upload your certificate and use IAM to make available to LB
	b. Use AWS Certificate Manager (ACM) to generate/manage certificate; integrated with LB

2. Predefined security policies (i.e. TLS 1.2)

3. WAF
- block/allow requests based on conditions such as IP address
- protection against specific attacks like SQL injection

4. Server Name Indication (SNI)
- host multiple TLS secured applications, each with their own certificate

############
############

Authentication in ALB
- need to authenticate users accessing applications fronted by LB

Authentication choices:
1. Perform authentication directly in the application
2. Authenticate with enterprise IdPs with SAML
3. Integrate with Cognito for social identities (such as Facebook, Twitter accounts)

############
############

Netflix Identity Platform
- workforce identity-as-a-service
- federate all the things
	- Have a separate authoritative IdP - apps delegate authentication to that IdP
	- Originally used SAML, then moved to OpenID/oAuth2
- dev self-service
- single SSO experience

Landscape:
- Hundreds of apps, growing daily
- Languages and frameworks galore
- With great freedom, comes great variability (complexity)

Possible solutions:

1. Use client libraries to federate
- Always playing catchup to new languages and new frameworks
- OSS options of varying quality
- Developer friction (need to write application code to integrate authentication)

2. Use Authentication Proxies
- Additional critical infrastructure to maintain
- Additional infrastructure cost to operate
- Potential bottlenecks

3. What about using ALBs?
- Authentication == undifferentiated heavy lifting
- Use ALB with OpenID connector (OIDC)

HTTPS listener supports "authenticate-oidc" action:
1. Unauthenticated request set to ALB
2. ALB redirects client to OpenID provider
3. User authenticates with OpenID provider
4. User has authentication session with ALB - requests now pass through the "authenticate-oidc" action and use "forward" action to be sent to target group with identity headers

Identity headers:
* X-Amzn-OIDC-Identity: will.rose@domain.com
* X-Amzn-OIDC-Access-Token: jkdfjd

Adoption:
- native Spinnaker integration
- fully self-service with few clicks
- supports all clients and applications
- recommended approach for all new applications

############
############

Availability

Cross-zone load balancing:
- ALBs are always launched in at least 2 AZs
- LB absorbs impact of DNS caching (load balancer in zone A can send request to target in zone A OR zone B)
- Eliminates imbalances in backend instance utilization

Health checks:
- Customize frequency and failure thresholds

############
############

Scalability & integration

- Use Auto Scale Groups (ASG) for backend applications
- EKS: use ALB Ingress Controller

############
############

Monitoring

1. Metrics
- CloudWatch metrics for each load balancer
- All metrics at 1 minute granularity

2. Access logs
- records each request processed by ELB
- delivered to S3 bucket (either every 5 minutes or every 60 minutes depending on configuration)

############
############

Pricing
- based upon Load Balancer Capacity Units (LCU) used

Load Balancer Capacity Units (LCU)
- LCU is calculated by measuring the dimensions on which the ALB processes your traffic:
- 4 dimensions:
    * new connections
    * active connections
    * bandwidth
    * rules processing

############
############

Steps for migration to ALB:
1. Create new ALB
2. Register targets
3. Update DNS to point to new ELB CNAME

############
############

Network Load Balancer (NLB)
- layer 4
- built with Hyperplane (SDN platform)
- high performance, millions of requests per second
- static IP support
- ideal for applications with long running connections
- support for both network and application target health checks

Static IP
- auto assigned to single IP per AZ
- assign an EIP per AZ to get static IP
- helps with whitelisting for firewalls

Preservation of Source IP
- preserves client IP to backends (only for EC2, ECS targets)
- can be used for logging and other applications
- removes need for proxy protocol
- support for proxy protocol V2 when load balancing to IP addresses

Technique: 
1. Use external facing NLB for incoming requests
2. Route to firewall
3. Firewall forwards to internal facing load balancer
=> This setup preserves source IP helping firewalls with features like Geo-IP blocking

Network health checks: 
- based on overall response time of target, will fail unresponsive targets within milliseconds

NLB metrics:
- more granular CloudWatch metrics, e.g. flow counts, reset counts, etc.

############
############

Recommendations:
- For TCP in VPC, use NLB
- For all other cases in VPC, use ALB
- Only use Classic LB for Classic EC2 networking

############
############

Action items:
* Deprecate use of Classic ELBs
* Research Server Name Indication (SNI) for ALB
* Check out Netflix "Spinnaker" project for deployment
