---
tags:
  - IP
  - TCP
  - HTTP
  - LoopbackIP
---

## What is Protocol?
> Protocol means a system of rules and regulation that govern something.
- A kind of **Office procedure** or **official way something must be done**

# IP - Internet protocol
Messages over IP are often communicated in "**packets**" 
	- which is small bundles of information (2^16 bytes)
	- Each packet has 2 components: **header** and **data**
**Why do we need Loopback IP?**
When a computer refers to IP address 127.0.0.1, it is referring to itself.
→ it allows for client and server on the same host to communicate using the TCP/IP protocol

# TCP - Transmission control protocol
TCP is a utility built **on top of IP**.
- **TCP** will guarantee transmission of packets in an ordered way.


# HTTP - Hypertext transfer Protocol
HTTP is a protocol that is an abstraction built **on top of TCP**.
**Pattern**: Request - Response -> specifically for client-server.




Understand IP:
- IPv4: 4 fields → 8 bits/field → 32 bits → 32/8 = 4 bytes → represent 2^32 addresses
- IPv6: 8 fields → 16 bits/field → 128 bits → 128/8 = 16 bytes → represent 2^128 values

The first field of IPv4 used for determining the IP class:
- **class A, B, C**: 1 → 223: are used on the Internet
- **class D**: 224 → 239: is reserved for Multicast addresses
- **class E**: 240 → 255: isn’t used currently

**Public IP:** can be accessed over the Internet
**Private IP**: can’t reach the Internet
- Private IP → protocol (NAT - network address translation) → Public IP

**IP subnetting**: is a technique that allows the allocation of a certain number of IP.
For example `192.168.0.2/24`
- `/24` means the first 24 bits represent the **network** portion
- the remaining 8 bits → the **host** portion
- So it can allocate from IP `192.168.0.3` → `192.168.0.254`