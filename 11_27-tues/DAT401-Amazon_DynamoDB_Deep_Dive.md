DAT401 - Amazon DynamoDB Deep Dive: Advanced Design Patterns for DynamoDB

This session is for those who already have some familiarity with DynamoDB. The patterns and data models discussed in this session summarize a collection of implementations and best practices leveraged by Amazon.com to deliver highly scalable solutions for a wide variety of business problems. The session also covers strategies for global secondary index sharding and index overloading, scalable graph processing with materialized queries, relational modeling with composite keys, and executing transactional workflows on DynamoDB.

Rick Houlihan - Principal Technologist, NoSQL

############
############

Agenda:
- Brief History of Data Processing (Why NoSQL?)
- Overview of DynamoDB
- NoSQL Data Modeling
    * Normalized versus De-normalized schema
- Common NoSQL Design Patterns
    * Composite Keys, Hierarchical Data, Relational Data
- Modeling Real Applications

############
############

SQL
- optimized for storage
- normalized/relational
- ad-hoc queries
- scale vertically
- good for OLAP
- note: relational DBs came about because storage was expensive (at the cost of greater CPU usage)

NoSQL
- optimized for compute
- denormalized/hierarchical
- instantiated views
- scale horizontally
- built for OLTP at scale

############
############

DynamoDB
- fully managed NoSQL
- document or key/value store
- highly scalable/performant
	- single tables can handle up to 4 million transactions/sec
- fast and consistent
- access control
- event driven programming

############
############

DynamoDB Tables

Partition key
- mandatory
- determines folder/bucket (data distribution)
- uniquely identifies item
- used for building unordered hash index

Sort key
- optional
- sorts items within that bucket
- use to model 1:N relationships

Partition:Sort Key
- uses 2 attributes to uniquely identify an item

Partitions are 3-way replicated
- you have your choice of eventually consistent reads vs strongly consistent reads
- strongly consistent reads always hit "primary", whereas eventually consistent is allowed to hit any of the three nodes (primary + 2 secondary)

Local Secondary Index
- alternate sort key attribute
- index is local to the partition key

Global Secondary Index
- alternate partition and/or sort key
- index across all partition keys
- use composite sort keys for compound indexes

Capacity (RCUs / WCUs) are provisioned separately for GSI

GSI updates
- async update model (eventually consistent)

############
############

Best practice: key design

To get the most out of throughput:
- partition key element should have a distinct value, and those values are requested fairly uniformly, as randomly as possible (like UUIDs).

Selecting a partition key:
- large number of distinct values
- items uniformly requested
- random distribution

Partition key examples:
- Status (BAD)
- Gender (BAD)
- CustomerId (GOOD)

Selecting a sort key:
- model 1:N and N:N relationships
- efficient/selective patterns (query multiple entities)
- leverage range queries
e.g. Orders and OrderItems

############
############

NoSQL does not mean "no relationships"... it's still about relationships:
- social network
- document management
- IT monitoring
- data trees

############
############

Tenets of NoSQL Data Modeling

* understand the use case
* identify access patterns
- read/write workloads: identify data sources, define query aggregations
* data modeling
- avoid relational design patterns, use one table
- 1 app = 1 table (reduce round trips, simplify access patterns)
- identify primary keys

############
############

DynamoDB Streams and Lambda
- table updates emitted to stream
- Lambda trigger on stream event

Lambda has 2 roles:
1. invocation role - what can it see
2. execution role - what can it do

Common use case: aggregations (counts, metrics, etc)
-> offload CPU by pre-computing

If your load is high enough, don't use Lambda; instead, stand up a dedicated service to read the stream

############
############

Composite Keys - combine two columns into a single one
- Example: Status + Date = StatusDate

############
############

Design pattern: object versioning

- Have a base (current) version (call it v0)
- When new writes happen, create new documents with incremented version
- When done with writes, copy new version to base (v0) document

############
############

DynamoDB Transactions API (NEW!)

TransactWriteItems
- synchronous update, put, delete and check (atomic, automated rollbacks)
- update up to 10 items within a transaction
- supports multiple tables
- complex conditional checks

############
############

Another key design example... list of office locations

Say you have a list of office locations, each defined by attributes:
- Country
- State
- City
- Office code

=> Set your partition key to be "Country + Location", where Location is a concatenated string of "STATE#CITY#OFFICE"
