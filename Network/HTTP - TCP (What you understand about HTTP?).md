# **What is HTTP, TCP/IP?**
## **HTTP** 
- a hypertext transfer protocol
- located at layer 7 — Application Layer
## **TCP/IP**
- transfer protocol
- located at layer 4 — Transport Layer
- Make sure the data is transfer between client and server in order, non-losing data, reliable transfer (checksum, ack..)
HTTP is single communication.
TCP: is combination of 3 factors: SYN, SYN-ACK, ACK
Before HTTP can exchange request and response, they must establish a TCP connection first and then HTTP replies on the TCP standard in order to do its job.

# Overview
**HTTP** uses a TCP connection to transfer request and response between client and server.
**HTTP Methods:** GET, POST, PUT, DELETE, not common (HEAD, OPTIONS, CONNECT, TRACE, PATCH)
**HTTP Status code:**
- 200: OK
- 204: No content
- 301: Moved Permanently -> URI of resource has been changed

## HTTP
> Currently, I have investigated 3 versions of HTTP
### http/1.0:
- we can send only 1 request per TCP connection, so all requests to be processed sequentially one by one per connection.
- Limitations: For each HTTP request/response pair we has to create a new TCP connection
	-> time-consuming for TCP handshaking

### http/1.1: 
- Features: pipelining and persistent connection(keep alive)
- Data: Human readable
- **Limitations**: HOL blocking
- 3 types of connection: short-lived, persistent (default), pipelining connection.

### http/2:
- Features: **multiplexing** request within a frame, **SERVER PUSH**
- Data: Binary structure
- It compresses headers
- **SERVER PUSH**: Allow a server populate data in a client cache
- **Limitations**: only solve problem HOC at HTTP layer, still exist **HOC** at **TCP Layer**

### http/3:
- Using QUIC instead of TCP

## HTTP Flow
1. Open TCP connection
2. Send a HTTP message
3. Read response sent by server
4. Close or reuse the connection for further requests

## Pipelining (http/1.1)
If pipelining is activated -> several requests can be sent without waiting for the first response to be fully received.
### Note
- **HEAD, GET** can **always be pipelined**.
- **POST** should **NOT** be pipelined.
- **PUT, DELETE** can be pipelined or **NOT depending** on whether requests in the sequence depend on the effect of other.

### Example
- A client send 4 pipelined GET through a single connection to a proxy, 3 of them have in cache and 1 must send to the destination server, so in this case the proxy server has to wait for the web server response and send 4 responses to the client
- If client open 4 connections to a proxy without using pipelining, the proxy can send the 3 cached responses to client in parallel before the response from server received.

## Multiplexing (http/2)
Request Multiplexing: điều luồng dữ liệu (stream) trong cùng 1 connection.
Each request in http1.1 needs a TCP connection -> it causes the HOL (Head of line blocking)
- It divides HTTP1.1 messages into frames which are embedded in a stream
- Header and Data frames are separated
- Binary framing
- more performant and robust
- lighter to transport, safer to decode
- great combination with Protocol buffer
- Header compression using HPACK
- Reduce overhead and improve performance

- Send multiple requests and response in parallel over a single TCP connection 
- Reduce latency and improve network utilization 
- Server push 
- One client request, multiple responses
- reduce round trip latency


- Summarize: in HTTP 1 we can only send one by one request through a single connection
- Provide pipeline technique (batch processing): allow multiple requests to be sent over a TCP connection without waiting for the corresponding responses
- The requests are placed in a queue and sent to the network over a single TCP connection.


- HTTP pipeline requires both server and client to support it.
- Using pipeline with Proxy server is not recommended because the HOL may really slow down server responses.





HTTP -> transfer Hypertext -> it is a request response protocol
-> HTTP is located at layer 7 (Application layer)
-> use port 80
-> TCP/IP is located at layer 4 (Transport layer)
-> Make sure the data is transfer between client and server in order, non-losing data, reliable transfer (checksum, ack..)

  
HOL: Head of Line Blocking
- Imagine there’s a long queue at a office, where people are served by order. The head of line person who is being served, and they can potentially block the whole line of waiting people.
- Now imagine the line of packets that has to be processed in order, and variable processing times, network latency or some packet loss, then you have HOL.

- It occurs when a line of packets is held up in a queue by a first packet.
- Example: 
	- multiple requests in HTTP pipelining 
	- slow consumption messages from a kafka topic partition

HTTP 1.0: only one HTTP request could be sent over a single TCP connection and its response received unless Connection: keep-alive header is used.

HTTP 1.1: 
- Used pipeline -> allow multiple request to be sent over a singer TCP without waiting for the corresponding responses
- The requests are placed in a queue and sent over to the server over a single connection
- the server processed the requests in the order they were received and sends back the responses in the same order.

