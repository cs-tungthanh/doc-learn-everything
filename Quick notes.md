
Cloud computing is on-demand delivery of CPU, storage, app, other IT resources through cloud platform.
- On-demand: pay as you go pricing
- On-premise: fixed cost, live forever


**throughput**: the number of records we can process per seconds.

Kỹ thuật phản ứng:
- Đưa ra quan điểm bản thân
- Tại sao có quan điểm đó
- Bằng chứng/chứng cứ cho quan điểm đó
- Câu chốt hạ khẳng định

Reliability: continue to work correctly even when thing go wrong.
The system should continue to work correctly even in the face of adversity (hardware/software faults and even human error).
- The app perform functionality that user expected
- It can tolerate the user making mistake or using the software in unexpected ways.

Scalability:
- If the system grows in a particular way what are our options for coping with the growth
- How can we add computing resources to handle the additional load.


| always            | usually    | often              | sometimes          |
|-------------------|------------|--------------------|--------------------|
| religiously       | invariably | on a regular basis | from time to time  |
| regularly         | routinely  | frequently         | now and then/again |
| without fail      | generally  | mostly/inevitably  | occassionally      |
| without exception |            |                    |                    |
| all the time      |            |                    |                    |
| come what may     |            |                    |                    |
| constantly        |            |                    |                    |
| day in day out    |            |                    |                    |

Tips: If adv of frequency is long it usually goes at **beginning** or **end** of phase. (not in a middle)
- I get up at 7 am on a regular basic.
- From time to time, I get up at 9 am.
- I go to work every Monday without fail.
- I get myself a coffee
- I grab a coffee/breakfast (take quickly)
- I do the cleaning/washing/cooking.
- I hit the book every evening(to study)
- I waste time scrolling on Tiktok

> [!NOTE] Vocabulary
> - succinctly (adv) S.T that need to be said clearly without unnecessary word
> - lump (v) to group indiscriminately
> 	- All children are lumped together in one class
> - indiscriminately (adv) 1 cách bừa bãi
> - profound (a) sâu sắc
> 	- showing a clear and deep understanding of serious matter
> 	- a profound effect
> - intermediary (n) trung gian (who carries messages between people who are unwilling or unable to meet)
> 	- there may be more intermediary levels
> - ambiguity(n) mơ hồ
> 	- Avoid ambiguity
> - delusional/delusive(a) believing thing that are not true (ảo tưởng)
> 	- delusion(n) 
> 	- delusively(adv) 
> 	- why not be delusional to the things that you can attract to the thing that you want?
> 	- delusional thinking








> Keywords: B-Tree, WAL

A B-Tree index must write every piece of data at least twice: once to the WAL and once to the tree page itself.
In order to make the DB resilience to crashes, it is common for B-tree implementation to include an additional data-structure on disk (a **write-ahead log** - **WAL** known as a **redo log**)
- This is an append-only file to which every B-Tree modification must be written before it can be applied to the pages of the tree itself.
- When the DB comes back up after a crash, this log is used to restore the B-Tree back to a consistent state


**LSM Tree** - Log-structured Merge-Tree
**SSTables** - Sorted string table
- **SSTable** is a file-based data structure 
- **LSM** utilizes **SSTable** as the underlying storage format for its level.


### Comparing B-Tree and LSM-Tree
- LSM trees are typically faster for writes.
	- Reads are typically slower because they have to check several different data structure and SSTables at different stages of compaction.
- B-Trees are thought to be faster for reads.