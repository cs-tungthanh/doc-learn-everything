# 1. RDS
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

## 1.1. RDS multi AZ - Disaster Recovery
- SYNC replication
- One DNS name - automatic app failover to standby
- Increase Availability
- failover in case of loss of AZ, loss of network, instance or storage failure
- No manual intervention in apps
- Not used for scaling

## 1.2. RDS Proxy
- Allow Apps to pool and share DB Connections established with the DB.
- Fully serverless, autoscaling, HA (multi-AZ)
- Reduced RDS & Aurora failover time up to 66%
	- Failover is backup operational mode (a procedure to handle fault or failure)
- Never publicly available (must be accessed from VPC)

## 1.3. RDS Type
> RDS provides 2 types: 
> 1. Magnetic (for backward compatibility)
> 	- the most cost-effective storage type
> 	- light or burst I/O requirements
> 	- for backward compatibility
> 1. General Purpose - SSD
> 	- workloads running on medium-sized DB instances
> 2. Provisioned IOPS
> 	- more expensive
> 	- high IOPS: low I/O latency and consistent I/O throughput

# 2. Elastic Cache

| Redis                                    | Memcached                                      |
|------------------------------------------|------------------------------------------------|
| Multi AZ with Auto-Failover              | Multi-node for partitioning of data (sharding) |
| Read Replicas to scale reads and have HA | No HA (no replication)                         |
| Data durability by using AOF persistence | Non persistent                                 |
| Backup and restore feature               | No backup and restore                          |
| Support Sets and Sorted sets             | Multi-threaded arch                            |
- Redis is really for HA, backup, read replicas
- Memcached is a pure cache that's distributed. Your data can be lose, no HA


# 3. S3
## 3.1. Overview
> **Tier** (4): S3 Standard, S3 Infrequent Access, S3 Intelligent, S3 Glacier + Lifecycle policy
> **UseCase**: Great for big objects (up to 5TB), not so great for many small objects, static file, website hosting...
> **Feature**: 
> - scale infinity
> - Versioning, Encryption, Replication, MFA-Delete, AccessLogs,...
> - Batch Operation using S3 Batch
> - S3 Event notification
> 	- S3  can trigger notification to SQS
> - Encryption: SSE-S3, SSE-KMS, SSE-C, client-side, TLS in transit, default encryption
> **Performance**: multi-part upload, S3 transfer acceleration, S3 Select

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
## 3.2. S3 Lifecycle Rules
### Expiration Rule
### Transition Rule
Before you transition objects to **S3 Standard-IA** or **S3 One Zone-IA**, you must store them for at least **30 days** in **Amazon S3**.

Use case: use Lifecycle Policy to automate old/unfinished parts deletion
- How can you analyze the optimal number of days to move objects between different storage tiers?
	- Using S3 Analytics

## 3.3. S3 Encryption
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

## 3.4. S3 Access point

![](../assets/s3-access-point.png)
![](../assets/s3_access_point_vpc.png)

### S3 Object Lambda Access Point
We will have a bucket to ingest raw data and create another S3 Object Lambda access point to consume data without creating another bucket

![](../assets/s3_object_ap_lambda.png)
## 3.5. S3 Types
1. **Standard S3**
2. **S3 Reduced Redundancy Storage (RRS)**
	- store noncritical, reproducible data at lower levels of redundancy than Amazon S3’s standard storage
	- It provides a highly available solution for distributing or sharing content that is durably stored elsewhere, or for **storing thumbnails**, **transcoded media**, or other **processed data** that can be easily reproduced
## 3.5. S3 QA
1. For compliance reasons, your company has a policy mandate that database backups must be retained for 4 years. It shouldn't be possible to erase them. What do you recommend?
	- Glacier Vaults with Vault Lock Policies
2. S3 **Object lock** - A company has its data and files stored on some S3 buckets. Some of these files need to be kept for a predefined period of time and protected from being overwritten and deletion according to company compliance policy. Which S3 feature helps you in doing this?
	- S3 Object Lock - Retention Compliance Mode
3. Which of the following S3 Object Lock configuration allows you to prevent an object or its versions from being overwritten or deleted indefinitely and gives you the ability to remove it manually?
	- Legal Hold
4. Your website is serving on-demand training videos to your workforce. Videos are uploaded monthly in high resolution MP4 format. Your workforce is distributed globally often on the move and using company-provided tablets that require the HTTP Live Streaming (HLS) protocol to watch a video. Your company has no video transcoding expertise and it required you may need to pay for a consultant.How do you implement the most cost-efficient architecture without compromising high availability and quality of video delivery?  
	- A. A video transcoding pipeline running on EC2 using SQS to distribute tasks and Auto Scaling to adjust the number of nodes depending on the length of the queue. EBS volumes to host videos and EBS snapshots to incrementally backup original files after a few days. CloudFront to serve HLS transcoded videos from EC2.
	- B. Elastic Transcoder to transcode original high-resolution MP4 videos to HLS. EBS volumes to host videos and EBS snapshots to incrementally backup original files after a few days. CloudFront to serve HLS transcoded videos from EC2.
	- [x] **Elastic Transcoder to transcode original high-resolution MP4 videos to HLS**. S3 to host videos with Lifecycle Management to **archive original files** to Glacier after a few days. **CloudFront (CDN) to serve HLS transcoded videos** from S3.
	- D. A video transcoding pipeline running on EC2 using SQS to distribute tasks and Auto Scaling to adjust the number of nodes depending on the length of the queue. S3 to host videos with Lifecycle Management to archive all files to Glacier after a few days. CloudFront to serve HLS transcoded videos from Glacier.


## 3.6. CORS
- Cross origin resource sharing: defines a way for client web applications that are loaded in one domain to interact with resources in a different domain
- Origin = scheme (protocol) + host (domain) + port
- Web browser based mechanism to allow requests to other origins while  visiting origin.
- using: CORS header 
- ![](../../assets/cors-example.png)

- Example in S3
- ![](../assets/s3-cors.png)
	- step1: user get index.html from bucket-html
	- step2: inside index.html that contains image from bucket assets -> so web browser need to get image with origin of html to get image

# 4. Redshift

# 4.1. Overview
- Redshift is more like a **data warehouse**.
	- It's a managed data warehouse service for storing and analyzing large dataset.
- **Redshift** is specifically designed to work with columnar data.
- When run analytical queries, Redshift only needs to retrieve the relevant columns, reducing data scanned + speed up processing.

- The cost using in Redshift are based on computing nodes (2 types of node) (at hourly rate)
	- On-demand Node (expensive)
	- Reserved Node (less expensive - billed at discounted hourly rates)
# 4.2. Questions
- Prepare for disaster recovery plan
	- Enable automated snapshots then configure your Redshift cluster to automatically copy snapshot to another AWS Region

- **EMR** provides the tools and workers (processing power) to prepare the materials (clean and transform data).
	- **EMR:** Best for large-scale data processing jobs with open-source frameworks.
- **Athena** allows you to quickly search the pile (data lake) to find specific materials (data subsets) when needed.
	- **Athena:** Ideal for cost-effective ad-hoc analysis on data lakes.
- **Redshift** helps you organize the prepared materials (data warehouse) for efficient construction (analysis) and visualization (dashboards).
	- **Redshift:** Perfect for data warehousing and complex analytical workloads.


