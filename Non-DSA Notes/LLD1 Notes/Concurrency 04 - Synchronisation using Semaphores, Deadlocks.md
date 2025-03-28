# Concurrency-4  Synchronization with Semaphores
----
## Agenda
- Synchronisation using Semaphores
    - Producer Consumer Problem using Semaphores
    - Producer Consumer Problem using Concurrent Data Structure (Queue)
    - Print In Order LeetCode Problem
- Deadlocks
- Additional Topics
    - wait(), notify() methods
    - Producer Consumer Using wait() & notify()
- Coding Projects (Optional)
    - Traffic Intersection Control
    - Resource Pooling in Library
- LeetCode Problems
- Additional Resources

## Synchronisation using Semaphores

Semaphores will start from basic in prod consumer problem after this.Its just to undertsand release() and acquire().

### Time To Try - Print In Order (LeetCode)
Try to solve the following problem using Semaphores Concept.
- [Print In Order - LeetCode](https://leetcode.com/problems/print-in-order/description/)

**Solution**
```java
import java.util.concurrent.Semaphore;

class Foo {
    Semaphore semaSecond = new Semaphore(0); // Lock second() until first() is done
    Semaphore semaThird = new Semaphore(0);  // Lock third() until second() is done

    public Foo() {}

    public void first(Runnable printFirst) throws InterruptedException {
        printFirst.run();   // Execute first()
        semaSecond.release(); // Signal second() that first() is done
    }

    public void second(Runnable printSecond) throws InterruptedException {
        semaSecond.acquire(); // Wait for first() to complete
        printSecond.run();    // Execute second()
        semaThird.release();  // Signal third() that second() is done
    }

    public void third(Runnable printThird) throws InterruptedException {
        semaThird.acquire(); // Wait for second() to complete
        printThird.run();    // Execute third()
    }
}


```

### **Summary of `acquire()` and `release()` in `Semaphore`**  

1️⃣ **`acquire()`**  
   - **Purpose:** Decreases the semaphore count and **blocks the thread if no permits are available**.  
   - **Usage:** Ensures a thread **waits until a required condition is met** before proceeding.  
   - **Example:** In the `Foo` class, `second()` calls `acquire()` on `semaSecond`, ensuring it runs **only after `first()` completes**.  

2️⃣ **`release()`**  
   - **Purpose:** Increases the semaphore count and **signals waiting threads to proceed**.  
   - **Usage:** Used to **unlock** or **signal** that a task has completed, allowing dependent operations to execute.  
   - **Example:** In `first()`, `release()` is called on `semaSecond` to **unblock `second()`**, ensuring proper execution order.  

✅ **Key Takeaway:** `acquire()` acts as a **checkpoint** to enforce order, while `release()` **unlocks the next stage of execution**. 🚀


### Producer Consumer Problem : A T-Shirt Store Example

The Producer-Consumer problem is a classic synchronization problem where two processes, the producer and the consumer, share a common, fixed-size buffer or store. The producer produces items and adds them to the buffer, while the consumer consumes items from the buffer. Semaphores are synchronization primitives that can be used to solve this problem efficiently.

**Problem Description**
Let's use a T-shirt store as an analogy for the Producer-Consumer problem. The T-shirt store has a limited capacity to store T-shirts. Producers can create T-shirts and add them to the store, and consumers can buy T-shirts from the store. The challenge is to ensure that the store doesn't overflow with T-shirts or run out of stock.



**Java Implementation-1 Using Semaphores**
(simplified implementation than what is covered in class, here we don't maintain an actual queue for T-Shirts, just the count)

```java
import java.util.concurrent.Semaphore;

public class TShirtStore {
    private static final int STORE_CAPACITY = 5;
    private static Semaphore mutex = new Semaphore(1); // Controls access to critical sections
    private static Semaphore empty = new Semaphore(STORE_CAPACITY); // Represents empty slots in the store
    private static Semaphore full = new Semaphore(0); // Represents filled slots in the store
    private static int tShirtCount = 0;

    static class Producer implements Runnable {
        @Override
        public void run() {
            try {
                while (true) {
                    empty.acquire(); // Wait for an empty slot
                    mutex.acquire(); // Enter critical section

                    // Produce a T-shirt
                    System.out.println("Producer produces a T-shirt. Total T-shirts: " + ++tShirtCount);

                    mutex.release(); // Exit critical section
                    full.release(); // Signal that a T-shirt is ready to be consumed
                    Thread.sleep(1000); // Simulate production time
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    static class Consumer implements Runnable {
        @Override
        public void run() {
            try {
                while (true) {//In Java (and most programming languages), while(true) is an infinite loop that will run continuously.
//It ensures the Producer keeps producing T-shirts indefinitely (as long as there is space in the store).
                    full.acquire(); // Wait for a T-shirt to be available
                    mutex.acquire(); // Enter critical section

                    // Consume a T-shirt
                    System.out.println("Consumer buys a T-shirt. Total T-shirts: " + --tShirtCount);

                    mutex.release(); // Exit critical section
                    empty.release(); // Signal that a slot is available for production
                    Thread.sleep(1500); // Simulate consumption time
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        Thread producerThread = new Thread(new Producer());
        Thread consumerThread = new Thread(new Consumer());

        producerThread.start();
        consumerThread.start();
    }
}
```

Semaphores

- **mutex**: Controls access to the critical sections (mutex stands for mutual exclusion).
- **empty**: Represents the number of empty slots in the store, initially set to the store's capacity.
- **full**: Represents the number of filled slots in the store, initially set to 0.


**Producer:**

- The producer acquires an empty slot using empty.acquire() and enters the critical section with mutex.acquire().

- It produces a T-shirt, increments the count, releases the mutex, and signals that a T-shirt is ready for consumption using full.release().

**Consumer:**

- The consumer acquires a filled slot using full.acquire() and enters the critical section with mutex.acquire().
- It consumes a T-shirt, decrements the count, releases the mutex, and signals that an empty slot is available for production using empty.release().

**Mutex**
Semaphore mutex = new Semaphore(1); // Acts like a mutex (Binary Semaphore)
A semaphore initialized to 1 behaves like a lock (similar to synchronized).

It has two states:
Permit Available → Thread can enter the critical section.
Permit Not Available → Thread must wait.
When a thread calls acquire(), it takes the permit (count becomes 0).
When it calls release(), it returns the permit (count becomes 1 again).

mutex.acquire() ensures that only one thread enters the increment() method at a time.
mutex.release() allows the next waiting thread to enter.

**Simulated Production and Consumption:**
`Thread.sleep()` is used to simulate the time it takes to produce and consume T-shirts.
**Execution**: When you run this program, you will observe the producer producing T-shirts and the consumer buying T-shirts. The store's capacity is maintained, and semaphores ensure proper synchronization between the producer and the consumer.

This example demonstrates how semaphores can be used to solve the Producer-Consumer problem efficiently, preventing issues such as overproduction or stockouts.
Great question! Let's break it down clearly.  



---


**Java Implementation -2 using Semaphores**
**Producer.java**
```java
public class Producer implements Runnable{
    private Queue<Object> queue;
    private int maxSize;
    private String name;
    private Semaphore producerSemaphore;
    private Semaphore consumerSemaphore;

    Producer(Queue<Object> queue, int maxSize, String name, Semaphore ps, Semaphore cs){
        this.queue = queue;
        this.maxSize = maxSize;
        this.name = name;
        this.producerSemaphore = ps;
        this.consumerSemaphore = cs;
    }
    @Override
    public void run() {
        while(true){
            try {
                producerSemaphore.acquire();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            if(queue.size()<this.maxSize){
                    System.out.println(this.name + " adding to queue, Size " + queue.size());
                    queue.add(new Object());
                }
            consumerSemaphore.release();
        }
    }
}
```
**Consumer.java**
```java
package Multithreading.ProducerConsumer;

import java.util.Queue;
import java.util.concurrent.Semaphore;

public class Consumer implements Runnable{
    private Queue<Object> queue;
    private int maxSize;
    private String name;
    private Semaphore producerSemaphore;
    private Semaphore consumerSemaphore;

    Consumer(Queue<Object> queue, int maxSize, String name, Semaphore ps, Semaphore cs){
        this.queue = queue;
        this.maxSize = maxSize;
        this.name = name;
        this.producerSemaphore = ps;
        this.consumerSemaphore = cs;
    }

    @Override
    public void run() {
            while(true){
                try {
                    consumerSemaphore.acquire();
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
                if(queue.size()>0){
                        System.out.println(this.name + " removing from queue, Size " + queue.size());
                        queue.remove();
                    }
                producerSemaphore.release();
            }
    }
}
```


**Client.java**

Here we create multiple producers and multiple consumers.
```java
public class Client {
    public static void main(String[] args) {
        Queue<Object> objects = new ConcurrentLinkedQueue<>();
        int maxSize = 6;
        Semaphore producerSemaphore = new Semaphore(maxSize);
        Semaphore consumerSemaphore = new Semaphore(0);

        Producer p1 = new Producer(objects,6,"p1",producerSemaphore,consumerSemaphore);
        Producer p2 = new Producer(objects,6,"p2",producerSemaphore,consumerSemaphore);
        Producer p3 = new Producer(objects,6,"p3",producerSemaphore,consumerSemaphore);

        Consumer c1 = new Consumer(objects,6,"c1",producerSemaphore,consumerSemaphore);
        Consumer c2 = new Consumer(objects,6,"c2",producerSemaphore,consumerSemaphore);
        Consumer c3 = new Consumer(objects,6,"c3",producerSemaphore,consumerSemaphore);
        Consumer c4 = new Consumer(objects,6,"c4",producerSemaphore,consumerSemaphore);
        Consumer c5 = new Consumer(objects,6,"c5",producerSemaphore,consumerSemaphore);

        Thread t1 = new Thread(p1);
        Thread t2 = new Thread(p2);
        Thread t3 = new Thread(p3);
        Thread t4 = new Thread(c1);
        Thread t5 = new Thread(c2);
        Thread t6 = new Thread(c3);
        Thread t7 = new Thread(c4);
        Thread t8 = new Thread(c5);

        t1.start();
        t2.start();
        t3.start();
        t4.start();
        t5.start();
        t6.start();
        t7.start();
    }
}
```
**Java Implementation -3 using Concurrent Data Structure**
Here is another implementation in which you can use a ConcurrentLinkedQueue in place of semaphores to ensure concurrency is handled well.
**Producer.java**
```java
import java.util.Queue;

public class Producer implements Runnable{
    private Queue<Object> queue;
    int maxSize;
    String name;

    public Producer(Queue<Object> queue,int maxSize, String name){
        this.queue = queue;
        this.maxSize = maxSize;
        this.name = name;
    }

    @Override
    public void run() {
        //Each producer wants to continuously produces
        // T-Shirts and add them to the queue if there is space available

        while(true){
            synchronized (queue){
                if(queue.size()<this.maxSize){
                    System.out.println("Adding - "+ queue.size());
                    queue.add(new Object());
                }
            }
        }
    }
}
```
**Consumer.java**
```java
import java.util.Queue;

public class Consumer implements Runnable{
    private Queue<Object> queue;
    int maxSize;
    String name;

    public Consumer(Queue<Object> queue,int maxSize, String name){
        this.queue = queue;
        this.maxSize = maxSize;
        this.name = name;
    }

    @Override
    public void run() {
        //Each producer wants to continuously produces
        // T-Shirts and add them to the queue if there is space available
        while(true){
            synchronized (queue) {
                if (queue.size() > 0) {
                    System.out.println("Removing - "+ queue.size());
                    queue.remove();
                }
            }
        }
    }
}
```

**Main.java**
```java
public class Main {
    public static void main(String[] args) {
        //Shared Object
        Queue<Object> q = new ConcurrentLinkedQueue<>();
        int maxSize = 6;

        Producer p1 = new Producer(q,maxSize,"p1");
        Producer p2= new Producer(q,maxSize,"p2");
        Producer p3  = new Producer(q,maxSize,"p3");

        Consumer c1 = new Consumer(q,maxSize,"c1");
        Consumer c2 = new Consumer(q,maxSize,"c2");
        Consumer c3 = new Consumer(q,maxSize,"c3");
        Consumer c4 = new Consumer(q,maxSize,"c4");
        Consumer c5 = new Consumer(q,maxSize,"c5");

        Thread t1 = new Thread(p1);
        Thread t2 = new Thread(p2);
        Thread t3 = new Thread(p3);
        Thread t4 = new Thread(c1);
        Thread t5 = new Thread(c2);
        Thread t6 = new Thread(c3);
        Thread t7 = new Thread(c4);
        Thread t8 = new Thread(c5);

        t1.start();
        t2.start();
        t3.start();
        t4.start();
        t5.start();
        t6.start();
        t7.start();
        t8.start();
    }
}
```


## DeadLocks

A deadlock in OS is a situation in which more than one process is blocked because it is holding a resource and also requires some resource that is acquired by some other process.

### Conditions for a deadlock
* `Mutual exclusion` - The resource is held by only one process at a time and cannot be acquired by another process.
* `Hold and wait` - A process is **holding** a resource and **waiting** for another resource to be released by another a process.
* `No preemption` - The resource can only be released once the execution of the process is complete.
* `Circular wait` - A set of processes are waiting for each other circularly. Process `P1` is waiting for process `P2` and process `P2` is waiting for process `P1`.

![Deadlock](https://scaler.com/topics/images/deadlock-in-os-image1.webp)

Process P1 and P2 are in a deadlock because:
* Resources are non-shareable. (Mutual exclusion)
* Process 1 holds "Resource 1" and is waiting for "Resource 2" to be released by process 2. (Hold and wait)
* None of the processes can be preempted. (No preemption)
* "Resource 1" and needs "Resource 2" from Process 2 while Process 2 holds "Resource 2" and requires "Resource 1" from Process 1. (Circular wait)

* 

### ✅ **Deadlock in Java Example**

A **deadlock** occurs when two or more processes are blocked, each waiting for resources held by the other, resulting in an infinite waiting state. 

### ⚡ **Deadlock Conditions (Coffman Conditions)**  
Deadlock occurs when the following four conditions are met:
1. **Mutual Exclusion:** Only one process can access a resource at a time.
2. **Hold and Wait:** A process is holding at least one resource and waiting to acquire additional resources held by other processes.
3. **No Preemption:** Resources cannot be forcibly taken from a process.
4. **Circular Wait:** A set of processes are waiting on each other in a circular chain.

---

## ✅ **Example Code to Demonstrate Deadlock in Java**

Here’s a simple Java program to simulate a deadlock scenario using two threads and two resources.

### **Code Explanation:**  
- **Thread 1**: Locks `Resource1` and waits for `Resource2`  
- **Thread 2**: Locks `Resource2` and waits for `Resource1`  
- Since both are holding one resource and waiting for the other, a deadlock occurs.

```java
public class DeadlockExample {

    // Resources
    private static final Object resource1 = new Object();
    private static final Object resource2 = new Object();

    public static void main(String[] args) {
        
        // Thread 1 - Locks resource1 and tries to lock resource2
        Thread t1 = new Thread(() -> {
            synchronized (resource1) {
                System.out.println("Thread 1: Locked Resource 1");

                try { Thread.sleep(100); } catch (InterruptedException e) { e.printStackTrace(); }

                System.out.println("Thread 1: Waiting to lock Resource 2");
                synchronized (resource2) {
                    System.out.println("Thread 1: Locked Resource 2");
                }
            }
        });

        // Thread 2 - Locks resource2 and tries to lock resource1
        Thread t2 = new Thread(() -> {
            synchronized (resource2) {
                System.out.println("Thread 2: Locked Resource 2");

                try { Thread.sleep(100); } catch (InterruptedException e) { e.printStackTrace(); }

                System.out.println("Thread 2: Waiting to lock Resource 1");
                synchronized (resource1) {
                    System.out.println("Thread 2: Locked Resource 1");
                }
            }
        });

        t1.start();
        t2.start();
    }
}
```

---

## ✅ **Explanation of the Deadlock Scenario**
1. **Thread 1** locks **Resource 1** and waits for **Resource 2**.  
2. **Thread 2** locks **Resource 2** and waits for **Resource 1**.  
3. Both threads are now stuck in a deadlock as neither can proceed.  

---

## ✅ **How to Detect and Prevent Deadlock**

### **1. Detection Using Thread Dumps**
- You can generate a thread dump using:
```bash
jstack <process_id>
```
- Look for "BLOCKED" or "WAITING" states.

---

### **2. Prevention Techniques**
- **Avoid Circular Wait:** Impose a strict ordering of resources using a numbering system and ensure processes request resources in that order.
- **Avoid Hold and Wait:** Acquire all resources before starting work or release held resources before requesting new ones.
- **Allow Preemption:** Forcefully take resources from a process under certain conditions.
- **Use Timeout:** Use `tryLock()` with timeouts to avoid indefinite blocking.
  
#### Example with `tryLock()` using `ReentrantLock`:
```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class DeadlockPrevention {

    private static final Lock lock1 = new ReentrantLock();
    private static final Lock lock2 = new ReentrantLock();

    public static void main(String[] args) {

        Thread t1 = new Thread(() -> {
            while (true) {
                if (lock1.tryLock()) {
                    try {
                        System.out.println("Thread 1: Locked Resource 1");
                        Thread.sleep(50);

                        if (lock2.tryLock()) {
                            try {
                                System.out.println("Thread 1: Locked Resource 2");
                                break;
                            } finally {
                                lock2.unlock();
                            }
                        }
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        lock1.unlock();
                    }
                }
            }
        });

        Thread t2 = new Thread(() -> {
            while (true) {
                if (lock2.tryLock()) {
                    try {
                        System.out.println("Thread 2: Locked Resource 2");
                        Thread.sleep(50);

                        if (lock1.tryLock()) {
                            try {
                                System.out.println("Thread 2: Locked Resource 1");
                                break;
                            } finally {
                                lock1.unlock();
                            }
                        }
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        lock2.unlock();
                    }
                }
            }
        });

        t1.start();
        t2.start();
    }
}
```

---

In the **deadlock prevention example** using `tryLock()`, we avoid deadlocks by preventing threads from getting stuck indefinitely. Let's break down how it helps:

---

## ✅ **How `tryLock()` Helps Prevent Deadlocks**
1. **Non-Blocking Nature**  
    - Unlike `lock()` which blocks indefinitely if the resource is not available, `tryLock()` **attempts to acquire the lock**.
    - If the lock is unavailable, it **immediately returns `false`** instead of blocking.

2. **Avoid Circular Wait**  
    - In the deadlock example, both threads check if they can acquire the second lock using `tryLock()`.  
    - If one thread cannot acquire the second lock, it **releases the first lock** using `unlock()` and retries.  
    - This breaks the **circular wait** condition that causes deadlocks.

3. **Graceful Exit**  
    - The thread doesn't remain blocked forever.  
    - It releases the resource it is holding and retries after a brief period.  
    - This ensures that resources are freed up for other threads to proceed.

---

## ✅ **Step-by-Step Explanation**
Consider this part of the code:
```java
if (lock1.tryLock()) {
    try {
        System.out.println("Thread 1: Locked Resource 1");
        Thread.sleep(50); // Simulate some work

        if (lock2.tryLock()) {
            try {
                System.out.println("Thread 1: Locked Resource 2");
            } finally {
                lock2.unlock(); // Release Resource 2
            }
        } else {
            System.out.println("Thread 1: Could not acquire Resource 2, retrying...");
        }
    } catch (InterruptedException e) {
        e.printStackTrace();
    } finally {
        lock1.unlock(); // Release Resource 1
    }
}
```

### ✅ **What's Happening Here?**
1. **Thread 1 acquires Resource 1.**
2. It tries to acquire Resource 2 using `lock2.tryLock()`.
3. - **If Successful:** It performs its task and releases both locks.  
   - **If Failed:** It prints a message, releases Resource 1, and retries from the beginning.
4. **Thread 2** follows a similar process.

---

## ✅ **How it Prevents Deadlock in Practice**
- If **Thread 1** locks **Resource 1** and **Thread 2** locks **Resource 2**, both will try to acquire the other resource.  
- However, because `tryLock()` **doesn't block** if the lock is unavailable, one of the threads will **fail to acquire the second resource**.
- The failing thread will release its first lock and try again.  
- Eventually, one of the threads will succeed in acquiring both resources.

---

## ✅ **Additional Improvements with Timeouts**
You can use a timeout with `tryLock()` for even better control:
```java
if (lock1.tryLock(1, TimeUnit.SECONDS)) {
    try {
        if (lock2.tryLock(1, TimeUnit.SECONDS)) {
            try {
                System.out.println("Thread acquired both resources");
            } finally {
                lock2.unlock();
            }
        }
    } finally {
        lock1.unlock();
    }
} else {
    System.out.println("Could not acquire lock. Exiting...");
}
```
- This approach ensures that the thread **won’t wait forever** if locks are not available.
- After **1 second**, the thread will stop trying and exit gracefully.

---


### Tackling deadlocks

There are three ways to tackle deadlocks:
* Prevention - Implementing a mechanism to prevent the deadlock.
* Avoidance - Avoiding deadlocks by not allocating resources when deadlocks are possible.
* Detecting and recovering - Detecting deadlocks and recovering from them.
* Ignorance - Ignore deadlocks as they do not happen frequently.

#### 1. Prevention and avoidance

Deadlock prevention means to block at least one of the four conditions required for deadlock to occur. If we are able to block any one of them then deadlock can be prevented. Spooling and non-blocking synchronization algorithms are used to prevent the above conditions. In deadlock prevention all the requests are granted in a finite amount of time.

In Deadlock avoidance we have to anticipate deadlock before it really occurs and ensure that the system does not go in unsafe state.It is possible to avoid deadlock if resources are allocated carefully. For deadlock avoidance we use Banker’s and Safety algorithm for resource allocation purpose. In deadlock avoidance the maximum number of resources of each type that will be needed are stated at the beginning of the process. 

#### 2. Detecting and recovering from deadlocks

We let the system fall into a deadlock and if it happens, we detect it using a detection algorithm and try to recover.

Some ways of recovery are as follows:

* Aborting all the deadlocked processes.
* Abort one process at a time until the system recovers from the deadlock.
* Resource Preemption: Resources are taken one by one from a process and assigned to higher priority processes until the deadlock is resolved.

#### 3. Ignorance

The system assumes that deadlock never occurs. Since the problem of deadlock situation is not frequent, some systems simply ignore it. Operating systems such as UNIX and Windows follow this approach. However, if a deadlock occurs we can reboot our system and the deadlock is resolved automatically.

#### 4. Tackling deadlocks at an application level
* Set timeouts for all the processes. If a process does not respond within the timeout period, it is killed.
* Implementing with caution: Use interfaces that handle or provide callbacks if locks are held by other processes.
* Add timeout to locks: If a process requests a lock, and it is held by another process, it will wait for the lock to be released until the timeout expires.

## Additonal Topics 
### Inter-thread Communication using wait() and notify()
[wait() & notify() - Recording Link](https://www.scaler.com/meetings/i/backend-lld-concurrency-callables-continued-3/archive)
Certainly! In Java, the wait() and notify() methods are part of the built-in mechanism for inter-thread communication and synchronization. These methods are used to coordinate the activities of multiple threads, allowing them to work together effectively. Let's break down these concepts for beginners:


#### wait() Method:
- The wait() method is called on an object within a synchronized context (i.e., within a method or block synchronized on that object).
- It causes the current thread to release the lock on the object and enter a state of waiting.
Purpose:
- wait() is used when a thread needs to wait for a certain condition to be met before proceeding.

For example, if a thread is waiting for a shared resource to be available, it can call wait() until another thread notifies it that the resource is ready.
Example:

```java
synchronized (sharedObject) {
    while (!conditionMet) {
        try {
            sharedObject.wait(); // Releases the lock and waits for notification
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    // Continue with the critical section
}
```
#### notify() Method:
- The notify() method is called on an object within a synchronized context.
- It wakes up one of the threads that are currently waiting on that object.
Purpose:

- notify() is used to signal that a condition (for which threads are waiting) has been met and that one of the waiting threads can proceed.
- It is essential to note that notify() only wakes up one waiting thread. If there are multiple waiting threads, it is not determined which one will be awakened.
Example:

```java
synchronized (sharedObject) {
    // Perform some operations and change the condition
    conditionMet = true;

    // Notify one of the waiting threads
    sharedObject.notify();
}
```

**Important Points:**
- Both wait() and notify() must be called within a synchronized context to avoid illegal monitor state exceptions.
- The calling thread must hold the lock on the object on which it is calling wait() or notify().
- The wait() method releases the lock, allowing other threads to access the synchronized block or method.
- The notify() method signals a waiting thread to wake up, allowing it to reacquire the lock and continue execution.

**Example Scenario:**
Consider a scenario where multiple threads are working on a shared resource. If a thread finds that the resource is not yet available (e.g., a buffer is empty), it can call wait() to release the lock and wait until another thread populates the buffer and calls notify() to signal that the resource is ready for consumption.

In summary, wait() and notify() are fundamental methods for thread synchronization in Java, enabling threads to communicate and coordinate their activities efficiently.

**Producer Consumer using Wait() and Notify()**
[Recording Link](https://www.scaler.com/meetings/i/backend-lld-concurrency-callables-continued-3/archive)

**Implementation -1 (Simplified)**
Here is a simplifed version of Producer Consumer as discussed in above LIVE Class.
**ProducerConsumer.java**
```java
public class ProducerConsumer {

    public void produce() throws InterruptedException {
        synchronized (this){
            System.out.println("Produced - T-shirt");
            //release the lock on the shared resource and wait till some other invokes this
            wait();
            System.out.println("Going to produce another T-Shirt");
        }
    }

    public void consume() throws InterruptedException {
       Thread.sleep(1000);
       Scanner sc = new Scanner(System.in);

       synchronized (this) {
           System.out.println("Take t-shirt? ");
           sc.nextLine();
           System.out.println("Recieved T-shirt");
           notify();
           Thread.sleep(3000);
       }
    }
}
```

**PCDemo.java**
```java
public class PCDemo {
    public static void main(String[] args) {
        ProducerConsumer pc = new ProducerConsumer();

        Thread t1 = new Thread(()->{
            try {
                pc.produce();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        Thread t2 = new Thread(()->{
            try {
                pc.consume();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });

        t1.start();
        t2.start();
    }
}
```

**Implementation-2**
A more robust Implementation is as follows:
```java
import java.util.LinkedList;

class SharedBuffer {
    private final LinkedList<Integer> buffer = new LinkedList<>();
    private final int capacity;

    public SharedBuffer(int capacity) {
        this.capacity = capacity;
    }

    public void produce() throws InterruptedException {
        synchronized (this) {
            while (buffer.size() == capacity) {
                // Buffer is full, wait for consumer to consume
                wait();
            }

            // Produce an item and add to the buffer
            int newItem = (int) (Math.random() * 100);
            buffer.add(newItem);
            System.out.println("Produced: " + newItem);

            // Notify the consumer that an item is available
            notify();
        }
    }

    public void consume() throws InterruptedException {
        synchronized (this) {
            while (buffer.isEmpty()) {
                // Buffer is empty, wait for producer to produce
                wait();
            }

            // Consume an item from the buffer
            int consumedItem = buffer.removeFirst();
            System.out.println("Consumed: " + consumedItem);

            // Notify the producer that a slot is available in the buffer
            notify();
        }
    }
}

class Producer implements Runnable {
    private final SharedBuffer sharedBuffer;

    public Producer(SharedBuffer sharedBuffer) {
        this.sharedBuffer = sharedBuffer;
    }

    @Override
    public void run() {
        try {
            while (true) {
                sharedBuffer.produce();
                Thread.sleep(1000); // Simulate production time
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

class Consumer implements Runnable {
    private final SharedBuffer sharedBuffer;

    public Consumer(SharedBuffer sharedBuffer) {
        this.sharedBuffer = sharedBuffer;
    }

    @Override
    public void run() {
        try {
            while (true) {
                sharedBuffer.consume();
                Thread.sleep(1500); // Simulate consumption time
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class ProducerConsumerExample {
    public static void main(String[] args) {
        SharedBuffer sharedBuffer = new SharedBuffer(5);

        Thread producerThread = new Thread(new Producer(sharedBuffer));
        Thread consumerThread = new Thread(new Consumer(sharedBuffer));

        producerThread.start();
        consumerThread.start();
    }
}
```
In this example:

- `SharedBuffer` is the shared buffer where the producer produces and the consumer consumes items.
- The Producer class produces items and adds them to the buffer.
- The Consumer class consumes items from the buffer.
- The main method creates instances of the shared buffer, producer, and consumer, and starts their respective threads.
- This solution uses the `wait()` and `notify()` methods to ensure that the producer waits when the buffer is full and the consumer waits when the buffer is empty, allowing for proper coordination and synchronization between the two threads.

## Coding Projects 
### Coding Problem 1 : Traffic Intersection Control
Implement a program that simulates a traffic intersection control system using Java Semaphores. The intersection has two roads, each with its own traffic signal. The traffic lights control the flow of traffic through the intersection.
**Requirements:**
- There are two roads, Road A and Road B, crossing at the intersection.
- Each road has its own traffic signal (Semaphore) controlling the traffic flow.
- The traffic lights for Road A and Road B have a cycle of Green, Yellow, and Red signals.
Only one road should have a green light at a time, while the other road has a red light.
The intersection should allow a smooth transition between green lights for both roads.

**Constraints:**
- The time duration for each signal (Green, Yellow, Red) can be adjusted based on the program's design.
- Use Semaphores to control access to the traffic signals and ensure a safe transition.
Each road signal should run in a separate thread.
- Implement a way to visually represent the current state of the traffic signals and indicate which road has the green light.


Sample Output:

Road A: Green
Road B: Red

[... Some time passes ...]

Road A: Yellow
Road B: Red

[... Some time passes ...]

Road A: Red
Road B: Green


**Notes:**
- The program should demonstrate proper synchronization to ensure that only one road has a green light at any given time.
- You may choose to implement additional features such as a pedestrian signal or a button for road switching.
- Consider the safety and efficiency of the intersection control system.
- This problem statement reflects a real-world scenario where semaphores can be used to control access to shared resources (in this case, the green light for each road) in a concurrent environment. Students can implement the solution to gain hands-on experience with semaphore-based synchronization in a practical setting.


**Solution:**

```java
import java.util.concurrent.Semaphore;

class TrafficIntersectionControl {
    private Semaphore roadASemaphore = new Semaphore(1); // Semaphore for Road A's traffic signal
    private Semaphore roadBSemaphore = new Semaphore(0); // Semaphore for Road B's traffic signal

    // Simulate time passing
    private void sleep(int seconds) {
        try {
            Thread.sleep(seconds * 1000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }

    // Switch the traffic lights for Road A and Road B
    private void switchLights() {
        System.out.println("Switching lights...");

        try {
            roadASemaphore.acquire(); // Acquire the semaphore for Road A
            roadBSemaphore.release(); // Release the semaphore for Road B
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }

    // Simulate traffic on Road A
    private void trafficOnRoadA() {
        while (true) {
            System.out.println("Road A: Green");
            sleep(5); // Green light duration

            System.out.println("Road A: Yellow");
            sleep(2); // Yellow light duration

            System.out.println("Road A: Red");
            switchLights(); // Switch to Road B
        }
    }

    // Simulate traffic on Road B
    private void trafficOnRoadB() {
        while (true) {
            try {
                roadBSemaphore.acquire(); // Acquire the semaphore for Road B
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }

            System.out.println("Road B: Green");
            sleep(5); // Green light duration

            System.out.println("Road B: Yellow");
            sleep(2); // Yellow light duration

            System.out.println("Road B: Red");
            switchLights(); // Switch to Road A
        }
    }

    public static void main(String[] args) {
        TrafficIntersectionControl control = new TrafficIntersectionControl();

        // Create and start threads for traffic on Road A and Road B
        Thread roadAThread = new Thread(control::trafficOnRoadA);
        Thread roadBThread = new Thread(control::trafficOnRoadB);

        roadAThread.start();
        roadBThread.start();
    }
}
```

#### Coding Problem - 2 Real-Life Problem Statement: Resource Pooling in a Library
Imagine a library that has a collection of books, and multiple students want to borrow and return books from the library. Implement a program that uses Java Semaphores to control access to the books, ensuring that the available resources (books) are used efficiently.

**Requirements:**
The library has a fixed number of books (resources) available for borrowing.
Students can borrow books from the library and return them after reading.
The library enforces a limit on the maximum number of students who can borrow books simultaneously.
When a student returns a book, another student can borrow it if there is an available slot.

**Constraints:**
Use Semaphores to control access to the shared resource (books).
Each student should run in a separate thread.
The program should handle the borrowing and returning of books concurrently.
Implement a way to visually represent the current state of the library, indicating which books are borrowed and available.

**Example Output:**

Student 1 borrows Book A
Library: [Book A is borrowed, Book B is available, Book C is available]

Student 2 borrows Book B
Library: [Book A is borrowed, Book B is borrowed, Book C is available]

Student 1 returns Book A
Library: [Book A is available, Book B is borrowed, Book C is available]


…
**Notes:**
Ensure that the library's state is properly synchronized to avoid race conditions.
You may choose to implement additional features, such as a waitlist for students.
Consider scenarios where a student may need to wait if all books are currently borrowed.
This problem statement reflects a real-world scenario where semaphores can be used to control access to a limited set of resources, ensuring that they are utilized efficiently and concurrently by multiple entities. Students can implement the solution to gain practical experience with semaphores in a resource pooling scenario.

**Sample Code**
```java
import java.util.concurrent.Semaphore;

class Library {
    private static final int MAX_STUDENTS = 2;
    private static final int MAX_BOOKS = 3;

    private Semaphore availableBooks = new Semaphore(MAX_BOOKS, true);
    private Semaphore studentSlots = new Semaphore(MAX_STUDENTS, true);

    // Simulate time passing
    private void sleep(int seconds) {
        try {
            Thread.sleep(seconds * 1000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }

    // Borrow a book from the library
    private void borrowBook(String book, int studentId) {
        try {
            availableBooks.acquire(); // Acquire a book
            System.out.println("Student " + studentId + " borrows " + book);
            sleep(2); // Simulate reading time
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }

    // Return a book to the library
    private void returnBook(String book, int studentId) {
        System.out.println("Student " + studentId + " returns " + book);
        availableBooks.release(); // Release the returned book
    }

    // Simulate a student using the library
    private void student(int studentId) {
        while (true) {
            try {
                studentSlots.acquire(); // Acquire a student slot
                String bookToBorrow = "Book " + (studentId % MAX_BOOKS + 1);
                borrowBook(bookToBorrow, studentId);
                returnBook(bookToBorrow, studentId);
                studentSlots.release(); // Release the student slot
                sleep(1); // Wait before the next operation
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }

    public static void main(String[] args) {
        Library library = new Library();

        // Create and start multiple threads for students using the library
        for (int i = 1; i <= MAX_STUDENTS; i++) {
            int finalI = i;
            new Thread(() -> library.student(finalI)).start();
        }
    }
}
```

## LeetCode Multithreading Problems (HomeWork)
- [Dining Philoshpers - LeetCode](https://leetcode.com/problems/the-dining-philosophers/)
- [Fizz Buzz Multithreaded - LeetCode](https://leetcode.com/problems/fizz-buzz-multithreaded/) 
- [Building H20 - LeetCode]( https://leetcode.com/problems/building-h2o/)
- [Traffic Light Control](https://leetcode.com/problems/traffic-light-controlled-intersection/description/)

## Additonal Resources
- [Udemy - Concurrency, Multithreading and Parallel Computing in Java](https://www.udemy.com/course/multithreading-and-parallel-computing-in-java/)
- [Memory Management in Operating Systems - Thrasing, Paging etc Interview Topics](https://github.com/kanmaytacker/fundamentals/blob/master/os/notes/04-memory-management.md)

-- End -- 


