# Overview


# Highlighted Feature
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


## SNS 
- One to Many subscriber
- SNS Topic
- up to 12.500.000 subscription per topic
### SNS Encryption
- in-flight encryption using HTTPS 
- At rest encryption using KMS
- Client-side encryption if client wants to perform by itself
- 