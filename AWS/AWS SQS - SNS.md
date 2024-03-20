# Overview


# Highlighted Feature
SQS is a managed service
- It scales automatically - don't need ASG
- Invisibility timeout gives more time to the consumer to process the message and prevent duplicate reading of the message
- SNS + SQS Fan out Pattern: 1 message send to SNS topic and fan out to multiple SQS queues
## SQS Long Polling / Short Polling
> Default -> Short Polling when `WaitTimeSeconds=0`

- Ref: [Amazon SQS short and long polling - Amazon Simple Queue Service](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html)
When we consume messages from a queue using short polling
- A queue on AWS is a number of distributed servers
	- So when you make a ReceiveMessage request, AWS will sample a subsets of its server and return messages from those servers
	- so it occur at a time, server A is containing a message but it is not on the subsets of servers that is chosen from request, it will not return but it may be returned for a subsequent request
- Long Polling (maximum 20 secs) allows to have waiting time until message is available on the response 
	- Reduce false empty responses by querying all servers
	- Return messages as soon as they become available.
## SQS FIFO
- cannot affect with IoT trigger
- Limit throughput 300 msg/sec
- 2 Features
	- preserved ORDER
	- Duplicated messages are not introduced into the queue => processed exactly ONE
- 


## SNS 
- One to Many subscriber
- SNS Topic
- up to 12.500.000 subscription per topic
### SNS Encryption
- in-flight encryption using HTTPS 
- At rest encryption using KMS
- Client-side encryption if client wants to perform by itself


# Kinesis
The capacity limits of Kinesis data stream are defined by the number of shards
- When the limit exceeds (by throughput or number of message) => **ProvisionedThroughputExceeded**
	- Solution: Increase number of shards
## Kinesis Data Stream + Kinesis Data Firehose
This is a perfect combo of technology for loading data near real-time data into **S3** and Redshift**.** Kinesis Data Firehose supports custom data transformations using AWS Lambda.

- **Kinesis Data Stream** is not supported to subscribe SNS Topic, but Kinesis Data Firehose supported
- 