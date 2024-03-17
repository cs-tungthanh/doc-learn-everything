**Availability Zone**: 
- 1 AZ can include one or more **data center**
- It's recommend that we should deploy service on at least 2 AZ

**Region**: 
- 1 Region have at least 3 AZ
- Regions are independent with each other

**Edge Locations:**
- is a data center used to deliver content fast to your user
- Services operate at Edge: CDN, Web App Firewall (WAF), Route 53 (DNS Service)


1. Network: VPC, ELB
2. Compute: EC2, ASG
3. Storage: S3, EBS, EFS, FSx, Storage GW
4. Security: IAM
5. Data structure: RDS, Redshift, DynamoDB
6. Data lake: Glue, Athena, Quick sight
7. Apps: SQS, SNS, API GW, Kinesis