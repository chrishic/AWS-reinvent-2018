SRV409 - A Serverless Journey: AWS Lambda Under the Hood

Serverless computing allows you to build and run applications and services without thinking about servers. Serverless applications don't require you to provision, scale, and manage any servers. However, under the hood, there is a sophisticated architecture that takes care of all the undifferentiated heavy lifting for the developer. Join Holly Mesrobian, Director of Engineering, and Marc Brooker, Senior Principal of Engineering, to learn how AWS architected one of the fastest-growing AWS services. In this session, we show you how Lambda takes care of everything required to run and scale your code with high availability

Holly Mesrobian - Director of Engineering, AWS
Marc Brooker - Senior Principal Engineer, AWS

############
############

Control plane
- Developer tools (Lambda console, SAM CLI)
- Control plane APIs (Config, Resource Mgmt)

Data plane
- Async invoke & events (Pollers, State Mgr, Leasing Svc)
=> SQS, Kinesis all go through this process
- Sync invoke (front end invoke, counting service, worker manager, worker, placement service)

############
############

Lambda System Services

1. Front End Invoke
- orchestrates both sync and async invokes
- steps:
    - authenticate caller
    - verify limits
    - confirm concurrency hasn't been exceeded
    - map function to worker manager

2. Counting Service
- provides region wide view of customer concurrency to help enforce set limits
- uses quorum-based consensus for distributed state

3. Worker Manager
- tracks container idle and busy state and schedules incoming invoke requests to available containers

4. Worker
- provisions secure environment for customer code execution

5. Placement Service
- places sandboxes on workers to maximize packing density without impacting customer experience or cold-path latency

############
############

Load Balancing
- routing function traffic across hosts which are distributed across AZs

############
############

Workflow examples

1. New worker, existing sandbox:
Invoke -> hits ALB -> Front End controller instance -> reserves sandbox with worker manager -> worker manager inits worker (download code, setup) -> front end controller then sends function request to worker

2. Existing worker, existing sandbox:
Invoke -> hits ALB -> Front End controller instance -> reserves sandbox with worker manager -> front end controller then sends function request to worker

3. New function or scaling up:
Invoke -> hits ALB -> Front End controller instance -> reserves sandbox with worker manager -> worker manager issues claim worker request with placement service to create sandbox -> worker manager inits worker (download code, setup) -> front end controller then sends function request to worker

############
############

Auto scaling
- provision function capacity when needed and releasing when not needed

############
############

Handling failures:
- built across multiple AZs
- always have a healthy host
- worker manager notices when worker is unhealthy and stops using it

############
############

Isolation: keeping workloads safe and separate

Layers:
1 - your code
2 - Lambda runtime (python, node, etc)
3 - sandbox (execution environment, linux libraries, /bin, etc)
4 - guest OS (Amazon Linux)
5 - hypervisor
6 - host OS
7 - hardware

Note: layers 1-5 == EC2 instance

Layers 1-3 are allocated uniquely to *each* Lambda function
-> gets cached, so subsequent invokes are fast

Layer 4 is allocated to single AWS account

Layers 6-7 are allocated/shared across all AWS accounts

Sandbox/container implemented via: 
- cgroups
- namespaces
- seccomp: like firewall for kernel (only expose the kernel functionality that your code needs)
- iptables
- chroot: file system isolation
=> Exactly how Docker creates/uses containers

############
############

Firecracker [https://aws.amazon.com/blogs/opensource/firecracker-open-source-secure-fast-microvm-serverless/]
- custom VM built for serverless workloads

Lambda use of Firecracker:
- they run Firecracker on top of EC2 Bare Metal instances
- run many instances of Firecracker on single hardware device

Hardware = physical devices
Host OS = virtualized devices; virtio host in Firecracker
Guest OS = virtio drivers

Hypervisor sits between guest OS and host OS

############
############

Utilization
- goal is to optimize utilization to keep servers busy

But... don't balance load equally across all nodes:
- instead get 99% load on smallest number of nodes
- cache/temporal locality
- ability to autoscale

Bad: pack server with one workload (function) 
- leads to stalls/busy/waits because each process is likely doing the same thing

Better: pack with many various workloads (take advantage of statistical multiplexing)

Best: placement optimization
- pick workloads that pack well together
- example: distribution graph comparing outcomes of rolling 20-sided die vs 2 10-sided dies

############
############

Improving VPC start-up and scaling (now)
- create ENI in your VPC, and then NAT between ENI and Lambda

Improvement coming in 2019: Move to remote NAT that can be shared among workers
- Faster scaling (don't need to create as many ENIs)
- lower latency
- easier to use

############
############

Firecracker Hypervisor vs. others
- lower startup time
- less memory overhead
- increased performance
- better flexibility
=> unlocks higher utilization and scale
