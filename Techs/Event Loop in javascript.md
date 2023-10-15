---
tags:
  - Event-Loop
  - microtask
  - macrotask
  - javascript
---
# Concept
JS is a single-threaded programming language, it has a single call stack and it can do one thing at a time → that’s what single thread means.

There're 3 parts:
- **Call stack (LIFO)**: when you step into a function, you put s.th onto the stack, when you return func, you pop off the top of stack.
- **Callback Queue (FIFO)**: microtask, macrotask queue
- **Web APIs:** where register event handlers

![](../assets/event-loop-1.png)
![](../assets/cb-queue.png)

> There's an endless loop, where **JS engine** waits for tasks, executes them and then sleeps, waiting for more tasks.
- **Microtask** queue (Job queue): **high** priority task queue
	- Promise callback will be appended to microtask
- **Macrotask** queue (Task queue): have a **low** priority queue for tasks **after** microtask
	- AddEvent()
	- setTimeout(), setInterval()



If we use await, it will suspend the func execution.
It's important to note that while the `async` function is suspended during the `await` expression, the rest of the program and other tasks continue to execute. This behavior enables asynchronous programming without blocking the main execution thread.

### General algorithm
> The order of execution: **CallStack -> microtask -> macrotask**
1. while len(stack) > 0:
	- Execute all tasks in stack
2. While len(microtask) > 0:
	- Execute them -> starting with the oldest task
3. while len(macrotask) > 0:
	- execute them


## Microtask
Promise handlers `.then`/`.catch`/`.finally` handler becomes a microtask.
Promise handling is always asynchronous, as all promise actions pass through the internal **"promise jobs"** queue, also called **"microtask queue"** (V8 term).
```javascript
let promise = Promise.resolve();
promise.then(() => alert("promise done!"));
alert("code finished"); // this alert shows first
```
But now we understand that `unhandledrejection` is generated when the microtask queue is complete: the engine examines promises and, if any of them is in the "rejected" state, then the event triggers.

## Example

```javascript
console.log(1);
setTimeout(() => console.log(2));
Promise.resolve().then(() => console.log(3));
Promise.resolve().then(() => setTimeout(() => console.log(4)));
Promise.resolve().then(() => console.log(5));
setTimeout(() => console.log(6));
console.log(7);
```
### Explanation
```javascript
console.log(1);
// Macrotask and microtask queues are empty, as of now

setTimeout(() => console.log(2));
// append to macrotask [log(2)]

Promise.resolve().then(() => console.log(3));
// append to microtask [log(3)]

Promise.resolve().then(() => setTimeout(() => console.log(4)));
// append to microtask [log(3), timeout(()=>log(4))]

Promise.resolve().then(() => console.log(5));
// append to microtask [log(3), timeout(()=>log(4)), log(5)]

setTimeout(() => console.log(6));
// append to macrotask [log(2), log(6)]

console.log(7);
// output 7 immediately
```
**Summary:**
- Stack: [log1, log7]
- Microtask: [log(3), timeout(()=>log(4)), log(5)]
- Macrotask: [log(2), log(6)]

**Step 1**: Print: 1, 7
**Step 2:** Execute microtask
- Print 3
- append to macrotask [log(2), log(6), log(4)]
- Print 5
**Step 3:** Execute macrotask
- Print 2,6,4

-> Result: 1, 7, 3, 5, 2, 6, 4