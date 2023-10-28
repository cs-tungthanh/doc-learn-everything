---
tags: B-Tree, B+ Tree
---

### Glossary
- d: the minimum degree of the tree

# B-Tree
**B-Tree** is self-balancing tree data that maintains sorted data and allow for efficient insert, delete, search operation.
All those operations can be performed in **O(Log N)** time.

### Features
- All leaves are at the same level -> **balanced**
- All internal nodes (except for root) 
	- have the number of children ranging from d -> 2d
	- have at least 2 children
- A non-leaf node with k children contain k-1 keys.
	- This means if a node has three children (k=3), it will hold two keys (k-1) that segment the data into three parts corresponding to each child node.


# B+ Tree
- B+ Tree is a variant of B-Tree
- It is wisely used in disk-based storage systems.

### Features
- Data Pointers are stored only in leaf nodes.
	- The internal nodes only contain keys and pointers to other nodes.
- All leaf nodes are linked together in linked list.
- In B+ tree, every key appears twice: once in internal node + once in leaf node
- Lookup requires a path from root -> a leaf
	- All data access operations take a consistent time.
 ![](B+Tree.png)

 # Compare B Tree and B+ Tree
 ## Overview lookup process
- Both records and index table are stored <ins>on disk storage</ins>, when querying occur. Firstly, it fetch index table into in-memory to processing as well compare with filter. Then it fetch each index block until find expected records (hold reference to that record) and finally fetch that records from disk and return.
- Each node in Btree is stored inside <ins>a block</ins> and our query will fetch sequentially each block from disk (not all block of index table)
 ### Which B+ tree over B tree?
 - B+ tree doesn't hold document reference in intermediate node, it only store reference in leaf node. This feature lead to several benefits:
	- Intermediate node have more space to store index key, make our tree **shallower**, so the number of index block fetch from disk will be reduced (reduced I/O operation) --> increase performance
	- Leaf node have linked together --> make range query more efficient 