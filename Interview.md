1. What happen when you enter a website link in browser?


# Basic Notes:

- 1 byte = 8 bits
- **Character**:
    - ASCII is a 7-bit code -> 128 characters.
    - Character sets used today in the US are generally 8-bit sets-> 256 different characters.
    - Unicode is an encoding for mandarin, Arabic, etc languages... - typically 2 bytes per character.
- **Integers** are typically stored with either 4 or 8 bytes

### Why JSON?
- human-readable - lightweight format - simple - the data can only be text
- **simple for machine to parse and create**
- It can be accessed by multiple platforms.
- JSON is a language-independent data format. This means that a JSON document can be used with any programming language.

### Why XML
**Why concurrency?**

Optimize processes and increase throughput using the same resource

The Tree is a data structure that is consisting of **nodes** (called vertices or points) and **edges** (lines)

B-Tree is a generalization of a self-balancing binary search tree

- in which each node can hold more than one search key and have more than 2 children.
- Every node has at most m children
- Every non-leaf node (except the root) has a least `m/2` children.
- All leaf nodes appear at the same level and carry information.


# Personal questions
- giới thiệu bản thân - điểm mạnh - điểm yếu
- Why do you want to quit your last job?
- **kì vọng gì ở Cty mới - need to prepare**
- khó khăn và cách giải quyết

## Candidate questions
- introduce your team
- Can you give me an overview of the projects that you are tackling?
- How about your team or something like how we work, Do you have any peer review conversations in the process of development?
- If I have a chance to join your team, what type of project I will tackle?

# Common
- JWT là gì - dùng để làm gì - ứng dụng
- How is the sorted set in Redis implemented?
- Why do we need a TCP handshake? - the main idea is to use ACK to know if the receiver has received the message.
- Restful vs GraphQL vs gRPC
    - Why using JSON
    - List some status responses
- what is a cooperatively / preemptively thread
- Can you tell me anything that you know about Docker?
- Stack and heap
    - The stack is fast. The heap is slow.
- Draw a sequence diagram for the login feature
- Deadlock-life lock
- Asymmetric and symmetric algorithms
- Designing e-commerce with login, viewing products, updating the product with 1000req/sec
- Kiểm tra 1 nums có thể chia 3 mảng con có tổng ===
- Cho 1 mảng có các rule B→C, C→A Tìm từ mach với rule đó → BCA
- HTTP vs HTTPS + SSL
- TCP vs UDP
    - Why do we need three ways handshake - purpose
    - Compare
    - When to use
- Difference between RUN, CMD, and ENTRYPOINT in Dockerfile.
- mutex, semaphore

```go
 type TreeNode struct {
      Val int // why not be a pointer
      Next *TreeNode // why is a pointer
 }
```

# Database
- Database scaling → sharding - replica
    - when - how
    - master-master vs master-slave
- MVCC: Multi-version concurrency control
- SQL Basic
    - HAVING - WHERE: difference
    - biễu diễn JOIN: bằng lời và bằng 2 vòng tròn giao nhau
    - Function, procedure, trigger…
    - SQL Planing
- mongo có table ko → ko có table + ko cần define scheme trc
    - when to use
    - **Why is it fast than mysql**
    - document based?
- How to design DB → make sure data consistency, data redundancy, and satisfy ACID.
    - consider CAP
        - NoSQL offer available better
        - SQL offers good data consistency.
- Transaction - ACID, locking
    - Cho ví dụ deadlock in transaction → cách giải quyết:
        - ví dụ transaction T1 transfer money from user 1 → user 2
        - tại thời điểm đó T2 transfer money from user 2 → user 1 → gây deadlock
        - —> giải quyết sort theo user id để update vào db
    - Locking types
- Indexing
    - Clustered index + Non-clustered index
    - Composite index: the order indexing.
    - Benefits - drawbacks
- Optimistic với Pessimistic lock: when we will acquire the lock: benefits + drawbacks
- Why do we need to use a pointer (pass-by-reference) instead of pass by value
    - → we want to modify the pointer
    - → the struct is very large and a deep copy is expensive
- How to optimize query
	- partition
	- index
	- denomarlize 
# Golang
- Go thì hỏi Goroutine, Select.
- Goroutine là gì, benefits, drawback
- why go is fast, concurrency modeling in go
	- statically typed -> type of each var is known at compiled time
	- lightweight Goroutines : lightweight processes called Goroutines
			- channels - powerful mechanism to communicate and synchronize between goroutines
			- **No Threading Overhead:** Unlike traditional threads, goroutines don't require heavy OS involvement for creation and management.
	- Garbage collection: automatically manage memory allocation and deallocation.
- Go: set n > number of process → what happens + how to properly set n value
- (Slice and Map act as references)
- map in Golang quản lý bộ nhớ s
- Why do we want to use concurrency
- interface{} vs []interface{}

```go
func foo1(v interface{}) {}
func foo2(v []interface{}) {}

foo1([]int{1,2}) -> valid
foo2([]int{1,2}) -> invalid
```

- Embedded interface
    - → It gives the ability to borrow behavior from multiple classes (similar to multiple inheritances)

# Algorithms + Data structure
- What is B-Tree
    - The difference with Hash Table
- Heap - priority queue
    - build heap
    - when to use
- Consistent hashing
- Leaky bucket algorithms
- Bloom filter

# OOP
1. Ý tưởng oop như thế nào, cho 1 ví dụ khi mình dùng oop. Thế nào là lập trình đối tượng? Cho biết các tính chất đặc thù của lập trình hướng đối tượng?
2. 4 tính chất của oop
    - Khi nào cần kế thừa, định nghĩa interface để làm gì,

Class is a template of object - nothing data

Object is instance of class - have full data

The interface is an abstract class - can’t create an instance

1. what is the difference between a compiler and an interpreter?
2. data type: list and set
    1. So sánh độ phức tạp: insert, delete…
    2. Ứng dụng khi nào (coi mấy ứng dụng của set thường được dùng khá nhiều)
3. sort algorithms: merge, quick, heap, buble
    - heap sort dùng khi nào -> Trả lời : vào priority queue, để làm định thời cpu, các giải thuật định thời cpu, vào thuật toán đồ thị như Dijkstra, prim,.m (heap hỏi nhiều)
4. coi DSF, BSF kỹ
    - pause condition

- Coi cách tính complexity: time + space: lật sách coi các tính độ phức tạp quick sort đồ lại.
- Phân biệt ArrayList , Linkedlist và Vector?
- Sự khác nhau giữa ArrayList – Array, Linkedlist – Arraylist, Set – List, Override – Overload?
- Khái niệm tham trị và tham chiếu? - nên kiếm ví dụ khi nào cần dùng cái nào
- Coi lại bộ nhớ stack và bộ nhớ heap (sài đệ quy thì sài bộ nhớ nào - trong C thì loại biến nào nằm trên heap)

### Đệ quy:

- Khi nào thì nên dùng dệ quy khi nào dùng vòng lặp
- Bất lợi dùng đệ quy là gì
- Viết hàm tính giai thưa bằng 2 cách nhận xét cách nào tốt hơn

Coi lại mấy ứng dụng của stack

- vd: co the dung stack de reverse link list

Cho 2 cái link list được sort sẵn head_list_1 -> 1 -> 2 ->7 -> 12 head_list_2 -> 3 ->7 -> 11 -> 14

1. Merge 2 list lại đúng thứ tự tăng dần
2. Đảo ngược list - chỗ này kiếm thêm hình như có TH đặc biệt
3. nhận xét các độ phức tạp: insert, delete, traverser.
4. Tìm số ở giữa link list

- Tìm max
- Tìm số lớn nhất thứ 2

## Dynamic Programming - hard problem

> Characteristics of DP: (top-down memoization and bottom-up tabulation)
> 
> 1. Overlapping Subproblem
> 2. Optimal Substructure Property

NodeJs handles requests using an Event loop inside NodeJs environment.

- the event loop is an event-listener that function inside the NodeJS env and it is always ready to listen, process, and output for an event
- asynchronous means all function in JS that are processed without blocking any other request
    - > They will be processed in the background
        
- synchronous say that the functions were executed in the sequence it was defined

# What happens when you type an URL into a web browser

[What happens when you type a URL into your browser? — The big picture (with Cloud) | by Christopher Le | Jun, 2023 | Medium](https://medium.com/@locvicvn1234/what-happens-when-you-type-a-url-into-your-browser-the-with-aws-ec2-8d53dca6decc)


# Docker
**Container**: a standard way to package your application code, configuration and dependencies into a single resource
- solve isolation problem
- conflict dependencies, configuration
-

Pod: a group of containers deployed together on the same host