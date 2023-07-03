
---
tags: Locking, optimistic, pessimistic, 2PL, 2-phase
---
# Overview
> There are two main mechanisms for concurrency control:
- **Optimistic lock**: apply for case xác suất **low** conflict transaction, very low to **reduce** retry action.
- **Pessimistic lock**: apply for case xác suất **high** conflict transaction để **make sure data consistency** + **giảm thiểu retry**

# Optimistic Locking
- Assume that there is no conflict can occur.
- Assuming most requests, attempts succeed.
- Example: locking + commit: data changed by a transaction can be written to data files before commit occurs → make commit itself very fast - but requires more work when this transaction fails to undo the changes
- Disadvantage: multiple transactions can involve but only one succeeds.
# Pessimistic locking
- Assume that transactions are expected to conflict with each other
- Sacrifices performance or concurrency for safety
- Minimize the chance of deadlock by acquiring all locks needed at the beginning of a transaction
- Disadvantage: at a single point in time - only one transaction can work → others have to wait.
- **Flow: Acquire lock → Update data → Release lock.**

# Scenario example
**Scenario**: **A** and **B** update resource **R**.
**Optimistic**:
- A comes and acquires X lock of R → do something
- B comes and acquires X lock of R → do something
- B has done first (commit) → return X lock of R → System updates X lock to X1 lock of R
- A has done (commit) → return X lock but it has a different version of lock X → A would be aborted and retried again.
---
**Pessimistic**
- A comes and acquires X lock of R
- B comes after and acquires X lock but it is unavailable → B must wait for A
---
# Two phases locking protocol - 2PL
> It differs from the Two-phase commit 2PC.
> It is a **pessimistic** concurrency control protocol

- **Phase 1:** Expanding phase: in this phase, a transaction is allowed to only **acquire** locks (but not release any locks)
- **Phase 2:** Shrinking phase: In this phase, a transaction is allowed to only **release** locks, but not _acquire_ any locks.

### For Optimistic concurrency control
In this method, transactions execute in the following **three** phases:
- Begin
- Read & Modify - temporarily
- Validate & commit/rollback
    - Validate techniques: Version checking, Timestamp ordering.

# Ways to deal with DEAD LOCK
- Prevention → sort by order properly.
- Detection → detect cycle dependence and abort one of them.