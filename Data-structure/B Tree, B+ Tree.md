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