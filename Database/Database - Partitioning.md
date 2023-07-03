---
tags: partitioning, sharding, range-based, hash-based
---

> To scale out our DB, we need to partition it. 
> Therefore, we need to develop a partitioning scheme that would divide and store our data into different DB servers.

**Drawback: → low availability**

# 1. Range-based Partitioning
- we can store bash on the hash key’s first value
- For example, store URLs
	- store all URLs start with “a” in one partition, “b” in another partition, and so on.

**Disadvantages:**
- unbalanced DB servers.

# 2. Hash-based Partitioning
- Take a hash of the object that we are storing
- we calculate which partition to use based upon the hash

What is Sharding? 
Sharding is a very important concept that helps the system to keep data into different resources according to the sharding process. The word "Shard" means "a small part of a whole". Hence, Sharding means dividing a larger part into smaller parts. In DBMS, Sharding is a type of Database partitioning in which a large Database is divided or partitioned into smaller data, also known as shards. These shards are not only smaller, but also faster and hence easily manageable.

Need for Sharding: 
Consider a very large database whose sharding has not been done. For example, let's take a Database of a college in which all the student's records (present and past) in the whole college are maintained in a single database. So, it would contain a very very large number of data, say 100, 000 records. Now when we need to find a student from this Database, each time around 100, 000 transactions have to be done to find the student, which is very very costly. Now consider the same college students' records, divided into smaller data shards based on years. Each data shard will have around 1000-5000 student records only. So not only the database become much more manageable, but also the transaction cost of each time also reduces by a huge factor, which is achieved by Sharding. Hence this is why Sharding is needed.

**Features of Sharding:**
- Sharding makes the Database smaller + faster
- Sharding makes the Database much more easily manageable
- Sharding can be a complex operation sometimes
- Sharding reduces the transaction cost of the Database