---
keys: transaction, ACID
---

# Glossary
1. Tn - Transaction nth
2. Cn - a set of search criteria
3. MVCC - Multi-version Concurrency Control

### Why do we need a DB transaction?
1. To provide a **reliable** + **consistent** unit of work - even in case of system failures.
2. To provide isolation between programs that access the DB concurrently.
—> How we can achieve 2 goals → satisfy the ACID properties

### What are the properties of a transaction?
The properties of the transaction can be summarized as ACID Properties.
********************What is a transaction?******************** It is a unit of work that represents a change composed of multiple operations.

### What is ACID?
**ACID** is _a standard set of properties_ that guarantee the DB operations are processed reliably.
There are 4 properties:
1. **A - Atomicity**: we can consider all operations in a transaction as ONE operation
    → either all of the operations are completed successfully or none of them take effect.
    → That means if one of them fails, everything will be rolled back → DB is unchanged.
2. **C - Consistency:** DB state (a storage system) should remain in a valid state after a transaction is executed
    → All data written must be valid according to db rules, constraints, cascades, and triggers.
    The database will move from one consistent state to another if the transaction succeeds and remain in the original state if the transaction fails.
3. **I - Isolation:** means all transactions run at the same time (concurrently) and should not affect each other.
	-> The effect of concurrent transactions: **Dirty read**, **Non-repeatable read**, **Phantom read**
4. **D - Durability:** Once a transaction is committed or is successful → the data are written must stay in persistent storage and **CANNOT BE LOST** event in case of system failures.

# Transaction Isolation
[transaction isolation levels](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html)

### **What do you understand by multi-version concurrency control? - MVCC**
[Achieving Snapshot Isolation - Distributed Systems for Practitioners (educative.io)](https://www.educative.io/courses/distributed-systems-practitioners/B60y29X9J1n)

> **is a technique where multiple physical versions are maintained for a single logical data item.**

MVCC or Multi-version concurrency control is used to avoid unnecessary database locks when 2 or more requests try to access or modify the data simultaneously.

using an MVCC technique.
This ensures that the time lag for a user to log in to the database is avoided. The transactions are recorded when anyone tries to access the content.
MVCC: Multi-version Concurrency Control
- data consistency is maintained by using the MVCC model and lock -> This means that each SQL statement sees a snapshot of data as it was some time ago - regardless of the current state of the underlying data.

lock acquired for querying reading data -- DON’T conflict with -- lock acquired for writing data -> So reading never blocks writing and writing never blocks reading
- Using MVCC provides better performance than locks in PostgreSQL, only 3 distinct isolation levels are implemented. -Read uncommitted behaves as Read committed.

**Snapshot isolation (SI)** is an isolation level that guarantees that all reads made in a transaction see a consistent snapshot of the database from the point it started. The transaction commits successfully if no other transaction has updated the same data since that snapshot. As a result, it is practically easier to achieve _snapshot isolation_

# Phenomena - Effect of concurrent transactions
## 1. **Dirty Read** — only **Read Uncommitted** satisfies it
- A transaction reads data written by a concurrent uncommitted transaction.
- It occurs in a situation where a transaction can see a non-exist state of db.

## 2. **Non-Repeatable read** —  read in the same row with multiple times in a transaction.
- Read ==the same row twice== but ==get a different value== each time within a transaction.
- Ex: T1 reads row A — concurrency — T2 updates row A
- → T1 re-reads row A → it will retrieve a different value

## 3. **Phantom Read** - same search criteria - for range
- _**Two same queries**_ are executed → but the _**rows**_ retrieved by the two **are different**.
- Ex: T1 retrieves a set of rows (that satisfy some search criteria - C1 - **Update id > 10)**
- At the same time, T2 generates some new rows that satisfy C1 - **insert id = 11**
- → If T1 re-executes→ it gets a different set of rows.

A: Transaction 1
B: Transaction 2
In A → `update * from table set name = ‘tung’ where id > 10`;
In B → `insert into tables, name=tung111, auto_incr_id = 11;`

**Without Next-key lock** then it will result:
A sẽ hỏi: sao tui đã bảo update hết name = tung cho những id > 10 rồi sao bây giờ lại có cái row kì cục, `name = tung111` mà `id = 11` (tui thấy nó id 11, thì name nó phải = tung chứ)
—> That’s why we need to have a **next-key lock**

`next-key lock = record lock + gap lock` 
In Tx A, `m select * from table where id > 10`: giả sử ID là PK, thì nó sẽ khóa tất cả các khoảng từ id > 10 nó sẽ gap lock trên cái khoảng này
In Tx B, m ko thể insert vào có id = 11 được lúc chạy, nó sẽ bị pending, chứ ko vào trạng thái granted liền

# Lock types
If that field is not indexing → the whole table will be locked - Cause it needs to table scan to find this value
- **Shared lock - S**: **(Read-only Lock)** tui muốn đọc dữ liệu && đảm bảo dữ liệu này không bị đổi trong quá trình tui đọc
	- permit the transaction that holds the lock to read a row
	- While holding the lock the Transaction doesn’t have permission to update data.
- **Exclusive lock - X**: **(can be both Read and Written)** tui muốn là người duy nhất tiếp cận dữ liệu này (tui có thể read/write tùy ý, nghĩa là thằng khác méo được lấy **shared lock** trên dữ liệu này luôn)
	- permit the transaction that hold the lock to update or delete row
- **Record Lock:** is a lock on **an index record.**
	- For example, `SELECT c1 FROM t WHERE c1 = 10 FOR UPDATE;` prevents any other transaction from _inserting, updating, or deleting_ rows where the value of `t.c1 = 10`
- **Gap Lock:** is a lock on a gap **between** index records
	- `SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE;`
	- → prevent other transactions from inserting the value in the range 10 → 20 into column `c1`
	- A gap might span a single index value, multiple index values, or even be empty.
- **Next-key lock:** is a combination of **a record lock** on the index record and **a gap lock** on the gap before the index record.


# **4 Isolation levels - that are guaranteed by MVCC and lock**
> MVCC: a read operation that uses snapshot information to present query results based on the point of time, regardless of changes performed by other transactions running at the same time.

## 1. Read Uncommitted 
→ transactions aren’t isolated from each other

## 2. Read Committed 
-> Default - only avoid **Dirty read**
- guarantee that any data read is committed at the moment it is read
- holds read/write lock only on the current row
With each SELECT statement
- the snapshot will be reset to the time of each consistent read operation (or the other transactions committed) —- set and get its own fresh snapshot. - consistent non-locking reads
- At this level the **Gap lock is disabled**.

## 3. Repeatable Read
- hold the read/write lock on all rows that it references
- When we select this transaction → **it will create a snapshot at the first read** → so any changes from other transactions will not affect the current state
- → that’s why it can prevent **non-repeatable read → (select a row twice will have the same value despite other transactions being committed )**

## 4. Serializable
-> highest strict - does **not satisfy any phenomena**
- **all transactions are executed in sequence**
- The highest isolation **block dataset** prevents the other user from updating or inserting rows into the dataset until the transaction is complete. → concurrency is slower - run 1 by 1 —> occurs deadlock a lot

|                  	| Dirty Read   	| Non-repeatable Read 	| Phantom Read 	|
|------------------	|--------------	|---------------------	|--------------	|
| Read uncommitted 	| +            	| +                   	| +            	|
| Read Committed   	| Not possible 	| +                   	| +            	|
| Repeatable Read  	| Not possible 	| Not possible        	| +            	|
| Serializable     	| Not possible 	| Not possible        	| Not possible 	|


```sql
The number is the order when run this query
// ---- T1 ---------
1. BEGIN;
4. SELECT * FROM TABLE WHERE id=9 FOR UPDATE; // lock this row for update
6. COMMIT;
// -----end T1 -----

// ----- T2---------
3. BEGIN;
5. UPDATE accounts SET balance=19 WHERE id=9; 
// it will be locked until T1 is committed 
// -> because T1.4 have been locked this row from updating/deleting
10. COMMIT;
// -----end T2 -----

// ----- T3 ---------
7. BEGIN;
8. SELECT * from accounts where id=9; // no issue
9. SELECT * from accounts where id=9 FOR UPDATE; 
		// it will be blocked because T2 has not yet committed
11. COMMIT;
// -----end T3 -----

```

# Distributed Transaction

There are 2 variants of distributed transactions:
1. Master-slave → we want to update a piece of data in multiple nodes → a transaction needs to update all of them in an atomic way
2. Partitioned database: where different pieces of data reside in different nodes and need to be updated atomically.
For instance, a financial application may use a partitioned database for customers' accounts,
- where the balance of user A resides in node n1.
- In contrast, the balance of user B resides in node n2
- → we want to transfer some money from user A to user B. We need to do this in an atomic way so that data is not lost (i.e., removed from user A, but not added to user B, because the transaction fails midway).