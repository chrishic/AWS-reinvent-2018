DAT321 - Amazon DynamoDB Under the Hood: How We Built a Hyper-Scale Database

Come to this session to learn how Amazon DynamoDB was built as the hyper-scale database for internet-scale applications. In January 2012, Amazon launched DynamoDB, a cloud-based NoSQL database service designed from the ground up to support extreme scale, with the security, availability, performance, and manageability needed to run mission-critical workloads. This session discloses for the first time the underpinnings of DynamoDB, and how we run a fully managed nonrelational database used by more than 100,000 customers. We cover the underlying technical aspects of how an application works with DynamoDB for authentication, metadata, storage nodes, streams, backup, and global replication.

Jaso Sorenson - Senior Principal Engineer
jaso@amazon.com

############
############

GetItem

1. User makes call over network
2. Request Router (RR) receives request
3. Authenticates caller
4. RR retrieves item from storage node

############
############

PutItem

1. User makes call over network
2. Request Router (RR) receives request
3. Authenticates caller
4. RR writes item to storage node leader A - leader replicates to storage nodes B & C
5. Acknowledge request after write replicated to 2 storage nodes (leader + 1 secondary)

############
############

DynamoDB uses "Paxos" to elect leader
=> how to achieve consensus among multiple nodes

Leader is always up-to-date. Leader propagates write to peer storage nodes. Leader sends heartbeats every 1.5s to peer nodes. If peers stop receiving heartbeats, they elect new leader.

RR are in multi AZ, storage nodes are in multi AZ.

############
############

Partition Metadata System
- RR needs to consult this for Get/Put to determine location of storage leader for partition.

############
############

Tables
- Every table must have primary hash key.
- Hash values are sorted into partitions.
- Each partition then gets storage nodes across all 3 AZs and leader is elected via Paxos.

Eventual consistency GetItem
- RR is allowed to randomly pick any of the 3 storage nodes to read from (so replica could be lagging leader); have at worst 1/3 odds of getting inconsistent read (since writes have to acknowledge commit to 2 of the 3 nodes)
=> note: usually all nodes are replicated within milliseconds

############
############

Storage node
- Uses B-tree for index
- Replication log

############
############

Auto Admin component (DBA for DynamoDB). Has the following duties:
- ensures that Partition Metadata System is up to date
- responsible for partition repair
	- when storage node detects failure, Auto Admin creates new storage node by copying index/log from good replica
- creates tables
- table provisioning
- split partition (if too much data is in single partition)

############
############

Secondary indexes

Secondary index partitions are independent of base table partitions

Log Propogator
- watches for changes on storage nodes
- then replicates changes to index partitions

You can have up to 5 global secondary indexes
- With a single PUT, you can hit 11 parititions, 33 storage nodes

############
############

Provisioning table capacity

- Read capacity units
	- 1 RCU allows up to 4k of data
- Write capacity units
	- 1 WCU allows up to 4k of data

If you set capacity too low, you get throttled.  Too high, and you overpay.

DynamoDB enforces capacity units via a "token bucket" algorithm

Token bucket is refilled at the RCU/WCU rate.

############
############

Provisioning example
- 300 RCU
- Ignore writes

Each partition gets 100 tokens/sec per storage node (300 read capacity units divided by 3 storage nodes)

Emptied at a rate of 1 token per request
* tokens deducted based on size (i.e. if read is for 8K, then 2 tokens deducted)

Token bucket size is 300 * RCUs
(gives you 5 minutes burst, i.e. 300 = number of seconds in 5 minutes)

Full token bucket is 30000 (100 tokens * 300)

Unbalanced load
- if read traffic is unbalanced among partitions, you can get throttled on hot partition.
=> Adaptive capacity - changes rate of refill of token bucket; change rate is based on Adaptive Capacity Multiplier (e.g. 1.5 instead of 1.0) - applied to EVERY partitiion of your table

############
############

Adaptive Capacity Multiplier

PID Controller
- A proportional–integral–derivative controller (PID controller or three-term controller) is a control loop feedback mechanism widely used in industrial control systems and a variety of other applications requiring continuously modulated control.

PID inputs:
- consumed
- provisioned
- throttling rate
- current mutliplier

PID output:
- new multiplier

PID will notice when consumed capacity is above provisioned, and adjust accordingly.

############
############

Auto Scaling for DynamoDB
- set target utilization (70%)
- minimum provisioned capacity
- maximum provisioned capacity
- you can specify for read and/or write capacity units separately

DynamoDB publishes metrics to CloudWatch:
- CloudWatch alarm emits to SNS (to notify use) + App Auto Scaling
- App Auto Scaling makes "UpdateTable" call to DynamoDB

############
############

Provisioning Recap
- Originally, DynamoDB expected balanced usage
- But in practice, becomes unbalanced over time -> bursting
- To deal with imbalance in key space -> adaptive capacity
- To mitigate cganging workloads -> auto scaling

############
############

Backup and Restore

Two types:
1. Point-in-time
2. On demand backups

- Backups are stored in S3.
- Replication log is sent to S3 (no need to store 3 copies, peers are smart enough to merge into single log)
- Storage nodes make their own independent choices of when to upload replication logs to S3
- Periodically uploads a "snapshot" of b-tree to S3; when to snap/store index is independent among partitions

############
############

Point-in-time restore

For point-in-time restore (PITR), DynamoDB uses combination of snapshot + logs
- find most recent snapshot, then apply all logs since that snapshot

If data in partition gets too large, Auto Admin will decide to split partition
-> Note: this makes restore trickier, since snapshot timeline "branches" at time of partition split

If PITR is NOT enabled, AWS can delete everything before most recent snapshot.

If PITR is enabled, AWS cannot delete previous snapshots. All snapshots/logs are kept for at least the past 35 days.
=> Therefore you get charged for S3 usage with PITR enabled

############
############

On-demand backups

1. Tell storage nodes to upload logs up to current time
2. Backup becomes available once logs have been uploaded to S3

############
############

DynamoDB Streams

- contain all mutations operations (put, update, delete)
- no duplicate events
- in order (by key) [parititions not in sync, so only in order by key, not across complete data set]
- new and old item provided on stream event

DynamoDB Stream Shards
- Kinesis client library
- Records
- Stream checkpointing

Storage node leader sends writes to stream shard
- about 10s of ms latency before item is in stream shard

############
############

Global tables
=> system of tables across multiple regions; basically achieved via replication engine

How it works:
- set up replication across regions from one DynamoDB to another
- stream reader looks at mutations and replicates
- bidirectional (region A replicates mutations to region B; region B replications mutations to region A)

Multi-region replication (N regions)
- stream reader has to ship replication to all other regions

Conflict resolution
- last writer wins
- if tie, a region is denoted as the tiebreaker

############
############

Action items:
* "Paxos Made Simple" - Leslie Lamport - 01 Nov 2001
