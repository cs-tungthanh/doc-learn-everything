---
tags:
  - SSL
  - TLS
  - HTTPS
---
Ref:

- [https://www.cloudflare.com/learning/ssl/what-is-https/](https://www.cloudflare.com/learning/ssl/what-is-https/)
- [https://www.digicert.com/what-is-an-ssl-certificate](https://www.digicert.com/what-is-an-ssl-certificate)
---
**Symmetric** **key means**: that to decrypt information, one must have the same key that was used to encrypt the message.
**Asymmetric key:** generate a public and private key
- If data is encrypted with one of the two keys, you must use the remaining key to decrypt it.
---
**HTTP**: protocol is used for viewing web pages on the internet.
- In standard **HTTP,** all info is sent in plaintext, which means it is vulnerable to being intercepted.
- **HTTPS** encrypts all info → making the data impossible to read.

**HTTPS** is an implementation of TLS encryption _**on top of the HTTP protocol**_, which is used by all websites as well as some other web services.
- HTTP 1.0: every request will require a new TCP connection
- HTTP 1.1: have `keep-alive` to reuse the same TCP connection - not solve HOL
- HTTP 2.0: streams compress header - eliminate HOL but it still remains on the TCP layer
    - HTTP 2.0 introduced the concept of HTTP “streams”: an abstraction that allows multiplexing different HTTP exchanges onto the same TCP connection. Each stream doesn’t need to be sent in order.

**HOL blocking (head of line)** - when the number of allowed parallel requests in the browser is used up, subsequent requests need to wait for the former ones to complete.

---

**The certificate** that is used for proving that the web server is trusted → client can exchange sensitive messages to that server

---

**SSL (Secure Socket Layer)** is a standard technology for establishing an **encrypted link** between a server and a client (browser).
More specification: SSL is a security protocol that describes how algorithms should be used.
**SSL** refers to secure Socket layer, used to encrypt connection
**TLS** refers to Transport Layer security

- Public SSL certificates are issued by CA
	- is a task for Load Balancer
**CA:** Central Authentication Service is a single sign-on protocol for the web.

**How does it work?**
- Step 1: Establish an SSL connection by using a process called **SSL Handshake**
    - After this, 3 keys are used to set up: **public, private, and session keys.**
- Step 2: Data exchange with the session key.

More details about **SSL** **Handshake**
1. The **browser** connects (request) to a web server secured with SSL-HTTPS.
2. The **server** sends a copy of the **certificate,** including its public key.
3. **Browser** check certificate root against a list of trusted CAs → make sure that cert is unexpired, unprovoked,…) → create a session key → encrypted with server’s public key → send.
4. The **server** will decrypt it with its private key. —> get the session key → send back acknowledgment with the session key to start an encrypted session.
5. The **server and browser** now encrypt all transmitted data with the session key.

![](../assets/https-ssl.png)