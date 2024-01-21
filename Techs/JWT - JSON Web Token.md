---
tags:
  - JWT
---
# Overview
[Why is JWT popular? - YouTube](https://www.youtube.com/watch?v=P2CPd9ynFLg)

- It's JSON based
- 3 parts: Header + Payload + Signature
	- Header:
	- Payload: where you store the claims
		- Claims are statements about entity: user, ...
	- Signature: 2 algorithms: symetric and asymetric
		- HMAC SHA256 use a shared key for both signing and verification


When should not use JWT?
- should not contain highly sensitive data
- aren't ideal for managing user sessions since they're stateless
	- revoking JWT access can be challenging
