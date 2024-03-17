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
- 1 VPC can attach resources on multi-AZ
- 1 VPC is bounded to 1 Region
- 1 AWS Account -> maximum 5 VPC
- 1 VPC -> create multi Subnets. Each Subnet will be on 1 AZ
Purpose: 
- Isolated Network env (dev|stg|prod)
- but if we want to isolate resource visibility, we must use multi account (user cannot see resource on diff env)
### Subnet
> 1 Subnet is bounded to 1 AZ
- Public Subnet: serve user
- Private Subnet: internal services
Example: 3-tier web app we usually create 3 subnets
- 1 public subnet: serve request from user
- 1 private subnet: application service
- 1 private subnet: Database

### ENI - Elastic Network Interface
It is a virtual network interface, providing network capabilities to EC2 instances. 
- Khi ta tạo 1 máy ảo (EC2,..) muốn máy này dùng đc trong VPC ta phải có 1 card mạng ảo 
- 1 EC2 can attach multi ENI
	- when we attach ENI to another EC2 3 Addresses will not change: IP Private, Elastic IP Address (EIP), MAC
	- EIP is static IPv4

Allow them to communicate with other resources within a VPC
> Can create independently and attach to an EC2 instance
> Purpose: to create primary private IPv4, one or more secondary IPv4
- One Elastic IPv4 per private IPv4
- On public IPV4
- One or more Security Group
- A Mac Address
- Can move between EC2 in stances for failover purpose
- Elastic Network Interfaces (ENIs) **are bounded to a specific AZ**. You can not attach an ENI to an EC2 instance in a different AZ.

- Khi chuyển sang một máy chủ mới, một card mạng ảo sẽ vẫn duy trì:
	- Địa chỉ IP Private
	- Địa chỉ Elastic IP address
	- Địa chỉ MAC
- ![](../../assets/ENI-MoveIP.png)





## EBS - Elastic Block Storage - single instance application
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

## EFS - Elastic File System - distributed apps
> It is something like volume mounted to an instance
- **EFS** is a network file system (NFS) that allows you to mount the same file system to n of EC2 **instances** that are in different AZs.
	- You have a fleet of EC2 instances distributes across AZs that process a large data set. What do you recommend to make the same data to be accessible as an NFS drive to all of your EC2 instances? -> EFS


- Use EBS - can only attach to one instance at a time (created on 1 AZ)
- Use EFS - multi instances
![](../assets/EBS_EFS_compare.png)

-
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


**How we can speed up EC2 Instantiating Process?**
- EC2 Instance
	- Using AMI: Install OS deps, your app,... beforehand and launch EC2 instance from the AMI
	- Using EC2 user data: for dynamic configuration
	- Hybrid: AMI and user data
- RDS: restore from snapshot
- EBS: restore from snapshot - the disk will already be formatted and have data

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
Tricks:
- In a **stopped** RDS DB, you'll still pay for storage. If you plan for stopping it for a long time (ex: only use 2 days/month,...), you should snapshot and restore instead
- How do you encrypt an unencrypted RDS DB instance
	- Create a snapshot of the unencrypted RDS DB instance, copy the snapshot and tick **"Enable encryption"**, then restore the RDS DB instance from the encrypted snapshot
- When you have an un-encrypted RDS DB - you **CANNOT** create **encrypted** Read Replicas
- If you need to create long term backups for Aurora DB  => should perform on DEMAND backups 
- If you want to test in your production db -> you can use Aurora Cloning to create test db.
- Which RDS **(NOT Aurora)** feature when used does not require you to change the SQL connection string?
	- Multi-AZ -> SQL conn str will not change
	- Read Replicas -> add new endpoints with their own DNS name. We need to change our application to reference them individually to balance the read load.
- Which of the following statement is true regarding replication in both RDS Read Replicas and Multi-AZ?
	- Read Replica uses Asynchronous Replication and Multi-AZ uses Synchronous Replication
- 
## RDS

### RDS multi AZ - Disaster Recovery
- SYNC replication
- One DNS name - automatic app failover to standby
- Increase Availability
- failover in case of loss of AZ, loss of network, instance or storage failure
- No manual intervention in apps
- Not used for scaling

### RDS Proxy
- Allow Apps to pool and share DB Connections established with the DB.
- Fully serverless, autoscaling, HA (multi-AZ)
- Reduced RDS & Aurora failover time up to 66%
	- Failover is backup operational mode (a procedure to handle fault or failure)
- Never publicly available (must be accessed from VPC)

## Aurora


# Elastic Cache
| Redis                                    | Memcached                                      |
|------------------------------------------|------------------------------------------------|
| Multi AZ with Auto-Failover              | Multi-node for partitioning of data (sharding) |
| Read Replicas to scale reads and have HA | No HA (no replication)                         |
| Data durability by using AOF persistence | Non persistent                                 |
| Backup and restore feature               | No backup and restore                          |
| Support Sets and Sorted sets             | Multi-threaded arch                            |
- Redis is really for HA, backup, read replicas
- Memcached is a pure cache that's distributed. Your data can be lose, no HA


# DNS
![](../assets/URL-DNS.png)
- TLD: Top level domain
- SLD: Second level domain
![](../assets/DNS-Howitwork.png)

## Route 53
- 100% HA (SLA), fully managed and Authoritative DNS
	- Authoritative = customer can update DNS Record
- is Domain Registrar
- Ability to check health of your resources
- 
## DNS Record
- Domain name
- Record Type
	- A: maps a hostname to IPv4
	- AAAA: maps a hostname to IPv6
	- CNAME: maps a hostname to another hostname (only for non-root domain)
	- Alias: maps a hostname to an AWS Resource (root and non-root)
		- example.com -> alb-123.us-east.elb.amazonaws.com
	- NS: Named server
- Value: Ip value
- Routing policy: how route 53 response to queries
	- Simple: 
		- route traffic to a single resource
		- can specify multi-values in the same record - it will be chosen randomly by client
	- weighted
		- we can route 30% traffic to IP 1 and 70% traffic to IP 2
		- Weighted Routing Policy allows you to redirect part of the traffic based on weight (e.g., percentage). It's a common use case to send part of traffic to a new version of your application.
	- Latency
		- redirect to the resource that has the least latency close to us
- TLS: time to live the amount of time the record cached at DNS Resolver.

You have purchased a domain on **GoDaddy** and would like to use Route 53 as the DNS Service Provider. What should you do to make this work
- Create a Public Hosted Zone and update the 3rd party Registrar NS records
- Public Hosted Zones are meant to be used for people requesting your website through the Internet. Finally, NS records must be updated on the 3rd party Registrar.


# Storage Comparison
- S3: Object Storage 
- S3 Glacier: Object Archival
- EBS: Network storage for one EC2 at a time
- Instance Storage: Physical storage for EC2 with high IOPS
- EFS: Network file system for linux instance, POSIX filesystem
- FSx for Window: file system for window
- FSx for Lustre: high performance computing for linux file system 
- Fsx for NetApp ONTAP: High OS compatibility 
- Fsx for OpenZFS:Manage ZFS filesystem
- Storage Gateway: hybrid cloud (on-premises <-Gateway-> AWS Cloud)
	- S3 & FSx File Gateway
		- low-latency access (caching at gateway)
	- Volume gateway
	- Tap gateway
- Transfer Family: FTP, FTPS, SFTP interface on top of S3 and EFS
- DataSync: synchronize data from on-premises to AWS, AWS -> AWS
	- Ex: migrate from S3 -> EFS
	- AWS DataSync is an online data transfer service that simplifies, automates, and accelerates moving data between on-premises storage systems and AWS Storage services, as well as between AWS Storage services.
- SnowBall, Snowball, SnowMoblie: **move** large amount of data to the cloud **physically**
	- Snowball Edge: allows to pre-process data while moving into Snowball
	- **AWS Data Migration:** This service is generally used for smaller-scale data migrations and wouldn't be as efficient for transferring hundreds of Terabytes.
- Database: Mysql, Postgres,...

# FSx - Filesystem
1. Scratch File System
	- **temporary storage**
2. Persistent File System
	- **Long-Term Storage**
	- Replication within mutli-AZ
# S3
S3 bucket names are [globally unique](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html), each bucket is stored in a Region
S3 Glacier: -> Object Archival
- use s3 lifecycle rule to transition data to tap storage(S3 glacier)

- Best practice: 
	- access (EC2,..) in the same region for reducing latency and data transfer costs

- S3 Get Byte Range Fetch 
	- you can fetch a byte-range from an object, transferring only the specified portion
	- Use case: indexing s3 objects in rds
		- create app that get byte range(first n bytes) and store that information in RDS
- S3 Replication
	- same region
	- cross region
	- Only replicate Delete Maker - cannot replicate Delete object
- How to prevent accidentally remove on s3
	- versioning
	- MFA for delete operation
- S3 Event
	- Event: ObjectCreated/Removed/Restore/Replication
		- Direct deliver to: SNS, SQS, Lambda
		- Delivery through EventBridge to aws services
	- **Simple Use Cases:** For straightforward scenarios where you only need to send events to SNS, SQS, or Lambda, direct delivery might suffice.
	- **Complex Workflows:** For more intricate event-driven architectures, advanced features, or broader integrations, EventBridge is often the preferred choice.
	- ![](../../assets/s3_event_2_eventbridge.png)
- S3 Lifecycle Rules
	- Expiration Rule
	- Transition Rule
	- Use case: use Lifecycle Policy to automate old/unfinished parts deletion
	- How can you analyze the optimal number of days to move objects between different storage tiers?
		- Using S3 Analytics
- S3 - Requester Pays
	- ![](../../assets/s3_requester_pay.png)
- S3 Performance
	- Multi-part upload: - parallelize uploads 
		- recommend file >100MB
		- must use for file > 5GB
	- S3 transfer acceleration: optimize transfer operation caused by distance. 
		- increase transfer speed by transferring to AWS Edge location which will forward to S3  in the target region
- S3 Select 
	- ![](../../assets/s3_select.png)
## S3 Encryption
- **SSE-S3** (default)
	- Encryption Happen in AWS
	- Keys are Managed by AWS S3 (owned by AWS) - no control
- **SSE-KMS**:
	- Encryption Happen in AWS
	- Key stored in AWS (KMS) - but we have full-control over the rotation policy of encryption key
- **SSE-C**: encryption with customer-provided key
	- Encryption happens in AWS 
	- **CLIENT** have full control over the encryption keys.
- **Client-side encryption**
	- when company don't trust encryption in AWS they will do its own.

## S3 Access point

![](../assets/s3-access-point.png)
![](../assets/s3_access_point_vpc.png)

### S3 Object Lambda Access Point
We will have a bucket to ingest raw data and create another S3 Object Lambda access point to consume data without creating another bucket

![](../assets/s3_object_ap_lambda.png)
- 
## S3 QA
1. For compliance reasons, your company has a policy mandate that database backups must be retained for 4 years. It shouldn't be possible to erase them. What do you recommend?
	- Glacier Vaults with Vault Lock Policies
2. S3 Object lock - A company has its data and files stored on some S3 buckets. Some of these files need to be kept for a predefined period of time and protected from being overwritten and deletion according to company compliance policy. Which S3 feature helps you in doing this?
	- S3 Object Lock - Retention Compliance Mode
3. Which of the following S3 Object Lock configuration allows you to prevent an object or its versions from being overwritten or deleted indefinitely and gives you the ability to remove it manually?
	- Legal Hold
4. 

## CORS
- Cross origin resource sharing: defines a way for client web applications that are loaded in one domain to interact with resources in a different domain
- Origin = scheme (protocol) + host (domain) + port
- Web browser based mechanism to allow requests to other origins while  visiting origin.
- using: CORS header 
- ![](../../assets/cors-example.png)

- Example in S3
- ![](../assets/s3-cors.png)
	- step1: user get index.html from bucket-html
	- step2: inside index.html that contains image from bucket assets -> so web browser need to get image with origin of html to get image


# CloudFont - CDN  - Layer 7 (app)
- Cache Service: improve performance for both cacheable content (image, video,...)
	- use global network of edge locations (data centers)
- is a global service
	- the cost of data out is vary for each edge location
	- we can reduce the number of edge to reduce cost
- DDOS Protection 
- We can perform passing **CloudFont Invalidation** to force remove cache (file/*, *,...)
- Use CloudFont Geo Restriction to restrict ip by country
- ![](CF-Origins.png)

![](CF_Example.png)

- Because the Edge Location is public so we need create a security group to route traffic to EC2 (it make EC2 become public)
![](CF_ALB_EC2.png)
![](AWS_Accelerator_vs_CF.png)
### OAC - Origin Access Control
- OAC prevents direct, public access to your S3 bucket. Users can only access your content through the designated CloudFront distribution.

You have a static website hosted on an S3 bucket. You have created a CloudFront Distribution that points to your S3 bucket to better serve your requests and improve performance. After a while, you noticed that users can still access your website directly from the S3 bucket. You want to enforce users to access the website only through CloudFront. How would you achieve that?    
- Configure your CloudFront Distribution and create an Origin Access Control (OAC), then update your S3 Bucket Policy to only accept requests from your CloudFront Distribution.
# AWS Global Accelerator - layer 4 (transport)
- Focuses on **routing traffic** to the best endpoint for each user, improving **availability and performance** by reducing latency.
	- Which AWS service can help you to assign a static IP address and provide low latency across the globe? -> **AWS Global Accelerator** 
- **CloudFront:** Focuses on **caching and delivering content** from your origin server, improving **page load times and reducing bandwidth usage**.


# AWS DataSync

![](aws-datasync.png)


# SQS
## Security
- In-flight encryption using HTTPS
- At rest encryption using KMS keys
- Client-side encryption if the clients want to perform en/decryption itself
## Access control
- IAM policies to regulate access to SQS
- SQS Access Policy (similar to S3 bucket policy)
	- useful for cross-account 
	- useful for other service to write into SQS



I'm going to do a deep dive in all of those over time

Let's have a look into each of these ... in detail
- For A, ....




It's up to you as SA to really understand trade-offs for doing and why you're doing things and cost implication of what you're doing
