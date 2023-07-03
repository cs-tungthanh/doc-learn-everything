---
tags: gRPC, REST
---

## How to choose the right architecture
Actually, you should consider your specific needs of your application.
- If you need a more flexible and a widely adopted architecture -> REST may be a better choice
- If you want to have the specification below -> gRPC may be better.
	- more performance (low latency).
	- real-time streaming handling. 
	- heavy system in micro-service 
	- More function driven API design

## gRPC
- gRPC is high performance, open-source RPC framework that can be used to build efficient, reliable and secure communication between services.
- It is built on top of HTTP/2 and use protocol buffers for data serialization.

## REST
- REST stands for representational state transfer.
- REST APIs use HTTP method to interact with resources (GET, POST, PUT, DELETE)
- Client-Server is **independent**: the client doesn't need to know how the server is implemented.
- **Stateless**: Each request contains all the information from server needs to process it -> the server don't need to keep the previous requests.



