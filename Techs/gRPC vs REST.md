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



|GraphQL|REST|
|---|---|
|Chỉ là 1 ngôn ngữ truy vấn APIs|Là 1 khái niệm, 1 loại kiến trúc phần mềm định nghĩa 1 số ràng buộc, quy tắc cần tuân theo khi design web services|
|Chỉ deploy 1 endpoint duy nhất và client có thể quyết định lấy resource nào họ cần|Deploy nhiều endpoints và mỗi endpoints thông thường trả về 1 resource duy nhất|
|Sử dụng kiến trúc hướng tới phía client|Sử dụng kiến trúc hướng tới phía server|
|Ko có cơ chế caching đc tích hợp sẵn → phải dùng lib bên ngoài|có tính năng cache mặc định|
|Ko hỗ trợ API versioning|support|
|only JSON|XML, JSON, YAML, …|
|Có định nghĩa kiểu dữ liệu rõ ràng và document tạo tự động|No|

