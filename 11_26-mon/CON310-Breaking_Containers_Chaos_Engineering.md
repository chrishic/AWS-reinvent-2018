CON310 - Breaking Containers: Chaos Engineering for Modern Applications on AWS

You may have heard of the buzzwords “chaos engineering” and “containers.” But what do they have to do with each other? In this session, we introduce chaos engineering and share a live demo of how to practice chaos engineering principles on AWS. We walk through chaos engineering practices, tools, and success metrics you can use to inject failures in order to make your systems more reliable..

Adrian Cockcroft - VP Cloud Architecture Strategy, Amazon Web Services
Ana Medina - Chaos Engineer, Gremlin

############
############

What should you do if system fails?
- stop?
- carry on with reduced functionality?

Ask yourself:
- Do you have backup datacenter?
- Have you failed over your apps?
- Have you failed over your entire datacenter?

Examples of events that have led to failure:
- forgot to renew domain name
- didn't update security cert and it expired
- data center was actually flooded

"You can't legislate against failure, focus on fast detection and response" ~ chris pinkham

############
############

Chaos engineering 
- experiment to ensure the impact of failure is mitigated

Failure layers:
- Infrastructure
- Software stack
- Application
- Operations

Mitigation layers:
- application layer replication
- structured db replication
- storage block level replication

Disaster recovery [past] -> Chaos engineering [current]-> Resilient critical systems [future]

Disaster recovery
* RPO - recovery point objective - time interval between recovery point snapshots, i.e. daily backups
* RTO - recovery time objective - time to recover after a failure, i.e. time to locate and restore from backup
-> ISO 22301:2012 Societal security BCM (Glossary)

Chaos engineering history:
* 2004 - Amazon - Jesse Robbins. Master of disaster.
* 2010 - Netflix - Greg Orzell. first implementation of Chaos Monkey
* 2012 - NetflixOSS open sources Simian army
* 2016 - Gremlin Inc founded
* 2017 - Netflix chaos engineering book. Chaos toolkit open source project.

############
############

AWS cell-based design
- Regions
- Availability zones

Regions are completely independent.

Availability zones are between 10km-100k apart from each other:
- separate flood plains, electric supply, etc,
- close enough for synchronous replication
- made up of data center buildings
- data centers diivided internally to isolate and replicate critical services

############
############

Methods of simulating and/or initiating failures:

1. Aurora database cluster fault injection queries
- Example: crash master or replica

2. IAM region restriction
- Simulate regional API issues by changing list of permitted regions

############
############

Gremlin (3rd party)
- failure as a service
- resource, network, state attacks on hosts or containers
- app-level fault injection for serverless
- easy to use API and UI

Chaos engineering process:
1. Form hypothesis
2. Run experiment
3. Abort conditions
3a. If failure, fix and re-run
3b. If success, scale and repeat

Blast radius
- start small (limit to a few hosts), and gradually expand (more+ hosts)

############
############

Observability of systems
- Low to high observability:
    Monolith with logging -> Monolith with tracing & logging -> Microservice that does one thing -> Lambda function with no side effects

Failures can be:
1. independent
2. correlated - harder to model and mitigate knock-on effects
3. epidemic - everything breaks at once

Epidemic failure modes:
- Linux leap second bug
- Sun SPARC cache bit-flip
- Cloud zone or region failure
- DNS failure
- Security configuration syntax error

Quarantine needed for epidemic failures:
- Linux leap second bug => maintain ability to deploy on Windows
- Sun SPARC cache bit-flip => use variety of CPU implementations
- Cloud zone or region failure => cross-zone or region replication
- DNS failure => multiple domains and providers
- Security configuration syntax error => limit scope of deployments
=> DIVERSITY needs to be key part of quarantine

############
############

Summary:
- Cloud provides the automation that leads to chaos engineering
- Manual DR will be replaced by chaos engineering
- Testing failure mitigation will become automated continuos chaos engineering (instead of being a one-time-a-year event)

[PRESENT] Chaos engineering - experiment to ensure the impact of failure is mitigated
[FUTURE] Resilient critical systems - automated detection and correction of failures

############
############

Action items:
* Book - "Release it, 2nd ed" - Michael Nygard
* Book - "Drift into Failure" - Sydney Dekker
* Book - "Chaos Engineering" - O'Reilly
* Paper - "The Network is Reliable" - ACM Queue 2014 - Bailis
* gremlin.com/community - "Gremlin with AWS / ECS" (enterprise trial: gremlin.com/break-containers)
