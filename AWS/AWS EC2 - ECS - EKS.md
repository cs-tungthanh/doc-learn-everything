# 1. EC2

## 1.1. EC2 instance types - 7 types
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
## 1.2. EC2 - Elastic IP 
- When you start/stop an EC2 instance, it can change its public IP that's why we need elastic IP for this case
- Elastic IP is a public IPv4 you own as long as you don't delete it
- You can attach it to one instance at a time
- BEST PRACTICE: try to avoid using Elastic IP
	- Use a random public IP and register a DNS (route 53) name to it
	- Use LB  and don't use public IP

**EBS - Elastic block storage**

## 1.3. EC2 placement groups - strategy
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

## 1.4. EC2 Hibernate
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

## 1.5. Security group
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
- Any timeout (not just for SSH) is related to security groups or a firewall.

SSH
- `chmod 0400 file.pem`
- `ssh -i file.pem ec2-user@public-ip`

In EC2 instance, 
- we should never configure the personal credential for EC2 Instance
- We should attach Roles to EC2: IAM Role,...
	- Create a Role -> Attach policy to that Role -> Attach that Role to EC2
- `aws iam list-users`
- `aws configure`


## 1.6. EC2 Instances Options
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
5. Dedicated Host: book an entire physical machine
6. Dedicated Instance 
7. Capacity Reservation


# 2. ECS
## 2.1. ECS Launch Type (2 types)
### 2.1.1. ECS - EC2 Launch Type
- Launch Docker container on AWS = Launch ECS Tasks on EC2 Cluster
- You must provision and maintain the infrastructure (the EC2 instances)
- EC2 instances must run ECS Agent to register in the EC2 cluster
- AWS takes care of starting/stopping containers
### 2.1.2. ECS- Fargate
- It's all serverless
- You don't provision your infrastructure (no EC2 to manage)
- You just create task definitions
- To scale, just increase the number of tasks 