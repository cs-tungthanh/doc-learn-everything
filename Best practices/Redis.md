---
tags: redis, cache
---

# Overview
- **Redis** is an in-memory database
- Caching -> reduce latency for reading from DB
- Async Jobs -> pub/sub
- Sorted set -> implement leader boards

### Definition
- is a fast, open-source, in-memory key-value datastore 
-> for use as a database, cache, message broker, and queue 
-> popular choices: caching, session management, gaming, leaderboards, real-time analytics, geospatial, rid-hailing (gọi xe), chat, media streaming, and pub-sub app.
### Highlight
- deliver sub-millisecond response times 
-> millions of requests per second for real-time applications: Gaming, Ad-Tech, Financial Services, Healthcare, and IoT.

In contrast to databases that store data on disk or SSDs, Redis stores all of its data in memory (the server's main memory). 
-> avoid time delays
Redis has a vast **variety of data structures**: String, Lists, Sets, Sorted sets, Hashes, Bitmaps, and HyperLogLog.
Redis can be used as 2nd database to make transactions faster.
Redis can be replicated to multiple-slave.

# Use cases
## In-memory → CACHE
- reduce latency for reading DB
- At scale, the cache is distributed among a cluster of Redis Servers
    - Sharding is a common technique to distribute the caching load evenly across the cluster
    - Setting correct TTL (time to live): expired time → store session (share session data among stateless servers)

## Distributed Lock:
> atomic command SETNX (SET IF NOT EXIST)
- are used when multiple nodes in an application need to coordinate access to some shared resource
- client needs to wait and retry if SETNX is not successful
![[../assets/setnx-lock-redis.png]]

## Rate limiter
> Redis can be used as a rate limiter by using the increment command on some counters and expiration times on those counters.
- For each incoming request, the request IP or user ID is used as a key
- The number of requests for the key is incremented using **INCR** command in Redis
- The current count is compared to the allowed rate limit. → if the count is within the rate limit → Request is processed
- The keys are set to expire after a specific time window


## Ranking - Leader board
> using sorted set → a collection of unique elements associated with the score. The elements are sorted with the score


## Sorted Set
[Introduction To Redis Data Structures: Sets (scalegrid.io)](https://scalegrid.io/blog/introduction-to-redis-data-structures-sets/)

> <key/> <score/> <member/>

- key: is the name of the sorted set
- Sorted sets are implemented as a dual data structure: a combination of **a hash and skip list**
	- Hash maps `member` to `scores`: Map k → v
	- Skip list maps `scores` to objects: Map v → (Set k)
- Operator: ZADD, ZRANGE, ZINCRBY, ZSCORE,…
- The time complexity of all operations is O(log n).


## Skip list
Not read: [https://astikanand.github.io/techblogs/advanced-data-structures/skip-list](https://astikanand.github.io/techblogs/advanced-data-structures/skip-list)

> It is a probabilistic data structure. - [Skip List | Set 1 (Introduction) - GeeksforGeeks](https://www.geeksforgeeks.org/skip-list/?ref=lbp)

![[../assets/skip-list-1.png]]

- The bottom layer is an ordinary ordered [linked list](https://en.wikipedia.org/wiki/Linked_list).
- It will be searched from the highest level → after finding the position in the bottom layer it will make a coin flip to make a decision on what the level would be and add this value

![[../assets/skip-list-2.gif]]

### Advantages:
- All operations O(log n).
- easy to implement compared to the Hash table and BST.
- As the number of nodes in the skip list **increases** → the possibility of the **worst-case decreases**.

### Disadvantages:
- It needs a greater amount of memory than the balanced tree.
- Can’t reverse
- searching is slower than a linked list.
- Not cache because they don’t optimize the locality of reference.