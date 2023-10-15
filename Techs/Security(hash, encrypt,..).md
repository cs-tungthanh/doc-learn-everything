---
tags:
  - Hash
  - Encrypt
---


# What is the difference between hash and encrypt?
- A hash function is only one way which means it is impossible to decrypt a hash value.

# Keywords
- **PKI - Public Key Infrastructure**
- Hash function
- Digital signature

# Overview
1. **What?**
> A hash function maps data of arbitrary length to a unique fixed-length string (called **digest**)

`HASH(X) = Y`
- It is one to one mapping.
- Given Y you can’t calculate X - one-way function.
- Y: fixed size - the size of X can be arbitrary

2. **When?**
> For checking integrity

Ex: We have a file text and compute its unique digest using a hash function → we send it to a receiver through the Internet and a receiver needs to check that file was not corrupted in the transmission.
Ex: When we download a file from the Internet it uses the same hash functions to verify its integrity.

3. **Which**?
- SHA256 hash function
    - SHA256 produces a 256-bit (32byte) hash value → which means it can have 2^256 hash values.

**What is the digital signature? - prevent the message tampered?**
The problem is How does the receiver know for sure that message was sent by Alice and not the other?
→ Alice needs to sign this message so then the receiver of the message can then verify the signatures.

**How does it work?**
![](../assets/PKI_with_DigitalSignature.png)