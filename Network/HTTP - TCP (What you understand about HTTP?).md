---
tags: HTTP, TCP
---

# **What is HTTP, TCP/IP?**
## **HTTP** 
- HTTP -> transfer Hypertext -> it is a request response protocol
- HTTP is located at layer 7 (**Application layer**) -> use port 80
## **TCP/IP**
- transfer protocol
- located at layer 4 — **Transport Layer**
- Make sure the data is transfer between client and server in order, non-losing data, reliable transfer (checksum, ack..)
HTTP is single communication.
TCP: is combination of 3 factors: SYN, SYN-ACK, ACK
Before HTTP can exchange request and response, they must establish a TCP connection first and then HTTP replies on the TCP standard in order to do its job.


![[../assets/HTTP.png]]

# Overview
**HTTP** is built on top of TCP.
**HTTP** uses a TCP connection to transfer request and response between client and server.
**HTTP Methods:** GET, POST, PUT, DELETE, not common (HEAD, OPTIONS, CONNECT, TRACE, PATCH)
**HTTP Status code:**
- 200: OK
- 204: No content
- 301: Moved Permanently -> URI of resource has been changed.

Browser normally keeps multiple TCP connections to the same server and send request to it in parallel.

## HTTP versions
> Currently, I have investigated 4 versions of HTTP
### HTTP/1
Every request to the same server requires a separate TCP connection.
- we can send only 1 request per TCP connection, so all requests to be processed sequentially one by one per connection.
- Limitations: For each HTTP request/response pair we has to create a new TCP connection
	-> time-consuming for TCP handshaking

### HTTP/1.1
- Introduce: keep-alive.
- Features: Pipelining and Persistent connection(keep-alive).
- Data: Human readable.
- **Limitations**: HOL blocking.
- 3 types of connection: short-lived, persistent (default), pipelining connection.
- keep-alive: connection could be reuse for more than a request.
	- Don't need to initiate TCP handshake -> reduce request latency.

### HTTP/2
- Introduce: HTTP stream, Compressed headers.
- Features: **multiplexing** request within a frame, **SERVER PUSH**
- Data: Binary structure
- It compresses headers
- **SERVER PUSH**: Allow a server populate data in a client cache
- **Limitations**: only solve problem HOC at HTTP layer, still exist **HOC** at **TCP Layer**
- Don't need to be sent or received stream in the order
	- Example: stream 1 header, stream 2 header, stream 1 data, stream 2 data

### HTTP/3
- Introduce a new protocol QUIC (using UDP connection).
- Using QUIC instead of TCP

## HTTP Flow
1. Open TCP connection.
2. Send a HTTP message.
3. Read response sent by server.
4. Close or reuse the connection for further requests.


## Pipelining (HTTP/1.1)
If pipelining is activated -> several requests can be sent without waiting for the first response to be fully received.
> The response must be received in the same order as to requests. -> It is tricky to implement (Many Proxy did not handle it properly).

Subsequent requests on the same connection must wait for the previous requests to complete 
If a request is blocked for any reason for packet loss -> all subsequence requests are also impacted.
### Note
- **HEAD, GET** can **always be pipelined**.
- **POST** should **NOT** be pipelined.
- **PUT, DELETE** can be pipelined or **NOT depending** on whether requests in the sequence depend on the effect of other.
- HTTP pipeline requires both server and client to support it.
- Using pipeline with Proxy server is not recommended because the HOL may really slow down server responses.
### Example HOL blocking
- A client send 4 pipelined GET through a single connection to a proxy, 3 of them have in cache and 1 must send to the destination server, so in this case the proxy server has to wait for the web server response and send 4 responses to the client
- If client open 4 connections to a proxy without using pipelining, the proxy can send the 3 cached responses to client in parallel before the response from server received.

- Pipelining
	- allow multiple request to be sent over a singer TCP WITHOUT waiting for the corresponding responses
	- The requests are placed in a queue and sent over to the server over a single connection
	- the server processed the requests in the order they were received and sends back the responses in the same order.

## Multiplexing (HTTP/2)
Request Multiplexing: điều luồng dữ liệu (stream) trong cùng 1 connection.
In HTTP/1.1 it can occur HOL blocking
- It divides HTTP/1.1 messages into frames which are embedded in a stream
- Header and Data frames are separated
- Binary framing
- more performant and robust
- lighter to transport, safer to decode
- great combination with Protocol buffer
- Header compression using HPACK
- Reduce overhead and improve performance
- Send multiple requests and response in parallel over a single TCP connection.
- Reduce latency and improve network utilization.
- Server push.
- One client request, multiple responses.
- reduce round trip latency.

## HOL: Head of Line Blocking
- Imagine there’s a long queue at a office, where people are served by order. The head of line person who is being served, and they can potentially block the whole line of waiting people.
- Now imagine the line of packets that has to be processed in order, and variable processing times, network latency or some packet loss, then you have HOL.

- It occurs when a line of packets is held up in a queue by a first packet.
- Example: 
	- multiple requests in HTTP pipelining 
	- slow consumption messages from a Kafka topic partition
