# Config aws-cli
1. Create user
2. Create access key
3. Install aws-cli
4. Run `aws configure`

# User
1. IAM Group: can only contain IAM Users
2. IAM User
3. IAM Role
4. IAM Permission

# I. IAM
- IAM Role is a way to give AWS entities permissions to do stuff on AWS
	- is an AWS entity that define a set of permissions
- IAM Roles cannot be assigned to IAM Users

## EC2 Role
	- IAMReadOnlyAccess: allow EC2 instance to read whatever is in IAM

## IAM Security Tools
1. IAM Credentials Report (account-level)
> show in the admin account
- list all your account's user and the status of their vaious credential
3. IAM Access Advisor (user-level)
> show in each user
- shows the service permission granted to a user and when those services were last accessed
- used to revise your policies

## IAM Best Practices
- Don't use the root account except for AWS account setup
	- Use the root account only to create your first IAM User and a few account/service management tasks. For everyday tasks, use an IAM User.
- One physical user = One AWS user
	- If a friend want to use AWS, don't share credential, we must create a user for them
- Assign users to groups and assign permissions to groups -> to make sure that security is managed at group level + should create a strong password policy
- Enforce user the use of MFA
- Create and use Roles for giving permissions to AWS Services
- Must use access keys for CLI/SDK -> **Never share**
- Audit permissions of your account using Credential Report & IAM Access Advisor



# II. EC2

## EC2 instance types - 7 types
> [Compute – Amazon EC2 Instance Types – AWS](https://aws.amazon.com/ec2/instance-types/?trk=361ccc4f-68c4-4038-bf6c-0586bee109dc&sc_channel=ps&ef_id=CjwKCAiAxreqBhAxEiwAfGfndGskDxRSJIWaDTMlyKsiynC2V2Ay0Sqqnzym3EcCvely5rhbrukQ5RoCISsQAvD_BwE:G:s&s_kwcid=AL!4422!3!536392805020!e!!g!!ec2%20instance%20types!11543056243!112002964789)
- General purpose
- Computed Optimized
- Memory Optimized
- Storage Optimized
- Accelerated computing
- HPC optimized - High performance computing
- Instance feature
- Measuring Instance Performance

EC2 User Data to load script to presetting when launching a new EC2 Instance
## EC2 - Elastic IP 
- When you start/stop an EC2 instance, it can change its public IP that's why we need elastic IP for this case
- Elastic IP is a public IPv4 you own as long as you don't delete it
- You can attach it to one instance at a time
- BEST PRACTICE: try to avoid using Elastic IP
	- Use a random public IP and register a DNS (route 53) name to it
	- Use LB  and don't use public IP

**EBS - Elastic block storage**

## EC2 placement groups - strategy
1. **Cluster**: cluster instances into a low-latency group in a single AZ (availability zone)
	- This is going to be high perf but high risk
	- All instances are on the same rack (means same hardware)
	*Pros*: Great network (10Gbps bandwidth)
	*Cons*: If a rack fails, all instances fails at the same time
	*Use case:*
	- Big data job that need to complete fast
	- Apps needs low-latency and high network throughput

2. **Spread**: spread instances across underlying hardware (max 7 instances / group / AZ) - critical apps
	Pros:
		- can span across multiple AZ
		- Reduce risk is simultaneous failure
		- EC2 instance on diff hardware
	Cons:
	- Limit to 7 instance / group
	Use case:
	- App that need to maximize HA
	- Critical apps where each instance must be isolated from failure from each other.
	![](../../assets/Spread-Placement-EC2.png)

3. **Partition**: spread instances across many different partitions within AZ - scale to 100 instances/group (that allow to run apps like Kafka, Cassandra)
	- A partition failure can affect many EC2 but wont affect to another partition
	- EC2 instance get access to the partition info through metadata service.
	- Use case: HDFS, HBase, Cassandra, Kafka.
	- ![](../../assets/Partition-placement-ec2.png)

## EC2 Hibernate
> Boost up your starting process on EC2
- Problem: 
	- when stop EC2, the RAM state would be lost, Data will kept in disk
	- When terminate, all states would be lost
- When start, the following happen:
	1. First start: OS boost % EC2 User Data script is run
	2. Following start: OS boots up
	3. Then: caches get warmed up, and that can take time
- To enable EC2 Hibernate, the EC2 Instance Root Volume type must be an **EBS volume** and must be **encrypted** to ensure the protection of sensitive content.
- 
> Hibernate purpose:
- In-memory (RAM) state is preserved
- Instance boot is much faster
- the RAM state is written to a file in root EBS volume and the EBS must be encrypted
> Use case:
> - Long running processing
> - Saving RAM State
> - Service that take time to initialize

## Security group
> SG: acting as a firewall on EC2 instance.
- they **control how traffic is allowed** into or out of our EC2 instances
- Security groups only contain **allow RULES**. 

- They regulate
	- Access to PORT
	- Authorized IP range: IPv4 - IPv6
	- Control: INBOUND - OUBOUND
		- SSH: port 22
		- HTTP: port 80
- **0.0.0.0/0**: means everything
![](../assets/SecurityGroup-EC2.png)

- I can be attached to multiple instances.
- Locked down to a region/VPC combination
	- If you switch to a diff VPC or region you need to create a new Security group
- Live outside EC2 
**- It's good to maintain one separate security group for SSH Access**.

If there's a connection timeout when connecting to EC2, it would be a security group issue that inbound rule doesn't exist to access.
	Any timeout (not just for SSH) is related to security groups or a firewall.


SSH
- `chmod 0400 file.pem`
- `ssh -i file.pem ec2-user@public-ip`

In EC2 instance, 
- we should never configure the personal credential for EC2 Instance
- We should attach Roles to EC2: IAM Role,...
	- Create a Role -> Attach policy to that Role -> Attach that Role to EC2
```
aws iam list-users
aws configure
```

### EC2 Instances Options
1. On-Demand Instances - pay by what we use
	- Short workload, predictable pricing, pay by second
	- **Recommend** for short term and un-interrupted workloads where you can't predict your app will behave
2. Reserved (1 & 3 years): it's like planning ahead, plan stay for a long time
	1. Reserved Instance - Long workloads
	2. Convertible Reserved Instance - Long workloads with flexible instances
3. Saving plan (1 & 3 years): pay a certain amount per hour for certain period
	- commit to an amount of usage (10$/1hour for 1 or 3 years),
	- long workload
	- locked to a specific instance family & AWS Region
4. Spot instances 
	- short workloads, cheap, 
	- can lose instance if your max price is less than the current spot price (less reliable)
	- Useful for workloads that are resilient to failure:
		- Batch jobs, Data analysis, Image processing, Any distributed workloads,...
	- Not suitable for critical jobs or database
	- It's something like people bid to rent the slot so if at a certain time someone pay more than you you instance would be lose that instance
	- Spot Fleet is a set of Spot Instances and optionally On-demand Instances. It allows you to automatically request Spot Instances with the lowest price.
	- 
1. Dedicated Host: book an entire physical machine
2. Dedicated Instance 
3. Capacity Reservation
4. 

## VPC - Virtual Private Cloud
## ENI - Elastic Network Interface
It is a virtual network interface, providing network capabilities to EC2 instances. Allow them to communicate with other resources within a VPC
> Can create independently and attach to an EC2 instance
> Purpose: to create primary private IPv4, one or more secondary IPv4
- One Elastic IPv4 per private IPv4
- On public IPV4
- One or more Security Group
- A Mac Address
- Bound to a specific AZ
- Can move between EC2 in stances for failover purpose
- *Elastic Network Interface (ENI) can NOT be attached to EC2 instances in another AZ.*
	- Elastic Network Interfaces (ENIs) **are bounded to a specific AZ**. You can not attach an ENI to an EC2 instance in a different AZ.
- ![](../../assets/ENI-MoveIP.png)





## EBS - Elastic Block Storage
> Is a network drive (not a physical drive) -> attach to an instances while they run
> EBS Volumes are a kind of network attached storage

You can attach a Volume to any EC2 Instance in the same Availability Zone, then you can detach without losing data, then attach to another instance, and so on. When you stop an Instance, you don't lose data stored on EBS Volumes -- this is why they are called _persistent storage_.

**Use case:** preserve root volume when instance is terminated.
- Allows your instance to persist data, even after their termination.
- One EBS can be ONLY mounted (Attached) to 1 instance at a time.
- Are bounded to a specific AZ
- It use the network to communicate -> there might be a bit of latency
- Have a provisioned capacity:
	- must specify size, IOPS(IO per second) in advance
**EBS multi-attach:** (only for io1, io2 type)
	- EBS Multi-Attach enables you to attach a single Provisioned IOPS SSD volume to **multiple instances** that are in the **same Availability Zone**

![](../../assets/EBS-ex1.png)
### EBS Snapshot
> To migrate an EBS volume across AZ
> 	- Take a snapshot
> 	- Restore the snapshot to another AZ
> 	- EBS backups use IO and you shouldn't run them while your app is handling a lot of traffic
- We can copy a EBS Snapshot to any regions
- Can create volume from a snapshot

## EFS - Elastic File System
> It is something like volume mounted to an instance
- EFS is a network file system (NFS) that allows you to mount the same file system on EC2 instances that are in different AZs.
	- You have a fleet of EC2 instances distributes across AZs that process a large data set. What do you recommend to make the same data to be accessible as an NFS drive to all of your EC2 instances? -> EFS

- Use EBS
    
- Use EFS
    
- Use an Instance Store
- EFS works with EC2 instance in **multi-AZ**
- HA, scalable, expensive (higher than EBS), pay per use
- Use case:
	- content management, web serving, data sharing, wordpress
	- share website file (static files,..)
	- 

## EC2 Instance Store
- EBS volumes are network drives with good but **limited** performance
- If we need **a high performance hardware disk** -> use EC2 Instance Store
	- Better I/O performance
	- EC2 instance store lose their storage if they're stopped
	- Good for buffer/ cache/ scratch data/ temporary content
	- Risk of data loss if hardware fails
	- Backup and replication are your responsibility
- If an instance stops/terminates the data would be lost
	- Another solution is to set up backup mechanisms for your data. It's all up to you how you want to set up your architecture to validate your requirements.
- You are running a high-performance database that requires an IOPS of 310,000 for its underlying storage. What do you recommend? -> Instance Store
	- 

When you launch an instance on EC2, Amazon has to look for a physical server that will host your instance with enough unallocated capacity to be able to run your instance.
"Instance Store" is disk space **local to the physical host**. What does this imply? Well, if you stop the instance, you will deallocate all those resources so that another customer can use them. This means that you will deallocate the cores, the RAM _and the disks_. Which means the data saved on Instance Store is lost when you stop/terminate the instance, or whenever the physical host running the instance fails for whatever reason ("Everything fails, all the time" -- Werner Vogels, CTO at Amazon). This is why Instance Store is called _ephemeral storage_.

> If you want **_persistent storage_**, then you'd need to use a service called Amazon EBS




## AMI - Amazon Machine Image
- It is actually a snapshot of an existing EC2 instance, AMI are a customization of an EC2 instance.
- We can create AMI from an EC2 instance to save the current state, setting, installed apps,... to an image and can launch it in the future.
- AMIs are built for a specific AWS Region - but you can copy the AMI to target Region and then launch EC2 instances.

# III. Load Balancer
There're 2 type of Load balancer
- At Layer 4 (Transport) - using TCP, UDP to manage traffic
- At Layer 7 (App Layer) - make decision based on info in header
### Sticky session - Session affinity
> **Sticky session** known as **session persistence**, **session affinity**
> - is a method for Load Balancer 
> 	- to identify requests coming from the same client
> 	- to always those request to the same server
- "cookie" used for stickiness and it has an expiration data
- 2 types of cookies:
	- Application-based Cookies
		- Custom cookie: generate by the target 
		- Application cookie: Generated by the LB
	- Duration-based cookies
		- Generated by LB
## ELB - Elastic Load Balancer
> ELB is a managed Load Balancer
- Load Balances are servers 
- Forward traffic to multiple servers downstream.
- Seamlessly handle failures of downstream instances
- Do regular health checks to your instances.
- Provide SSL for your websites.
- Enforce stickiness with cookies
- HA across zones
- Separate public traffic from private traffic
- When enable **Cross-Zone Load balancing**, ELB will distribute traffic across all registered EC2 in all AZs

## 1. ALB Application Load balancer - Layer 7
-> Application Layer: Support for HTTP/HTTPS/Web Socket, Config SSL, 
- **Only support a static DNS name - Don't support Elastic IP**
- ALBs can route traffic to different Target Groups based on URL Path, Hostname, HTTP Headers, and Query Strings.
	- Don't route based on location
## 2. NLB - Network Load Balancer - Layer 4
-> Transport Layer: Forward TCP & UDP traffic to your instances, config SSL
- **Only Network Load Balancer provides both static DNS name and static IP.**
- High performance - million request/second
- Less latency ~100ms (ALB ~400ms)
- NLB has one static IP per AZ and supports assigning EIP - Elastic IP
## 3. GLB - Gateway Load Balancer - Layer 3 IP Packets
- Firewall, Intrusion Detection, Prevention systems, Payload manipulation,...
- Single entry/exit for all traffic
- It's going to be a Load balancer that distributes traffic to your virtual applications.
- Use GENEVE protocol on port 6081
- Target Groups
	- EC2
	- IP Addresses (must be private IPs)
	- 

## SNI - Server Name Indication
- solve problem when a single LB uses multiple SSL Certificates onto the web servers (to serve multiple websites)
- is a newer protocol that requires client to indicate hostname of the target server
- ![](../assets/SNI-example.png)

## Connection Draining - De-registration delay
- a connection is being drained 
- stop to send traffic to instance that is being drained (de-register)
- For example: the value of connection draining is 300 ms (default)
- There're 3 EC2, one of them is being removed so that before removing LB will move the traffic to the remaining instances and make it to in **draining state**.

## Questions
#### How to get Client IP if we're using LB?
- When using ALB to distribute traffic, the IP Adds you'll receive requests from will be ALB's private IP Adds.
	- -> to get Client's IP, ALB adds an additional header called `X-Forwared-For` contain client's IP address.
#### Auto-scaling group with custom policy?
Your boss asked you to scale your Auto Scaling Group based on the **number of requests per minute** your application makes to your database. What should you do?
-> Create **a CloudWatch custom metric** then create a CloudWatch Alarm on this metric to scale your ASG


# IV. Database
## RDS

# Elastic Cache


I'm going to do a deep dive in all of those over time

Let's have a look into each of these ... in detail
- For A, ....



