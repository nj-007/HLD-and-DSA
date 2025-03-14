# Concurrency-2  Executors and Callables
---
In this tutorial, we will cover the following concepts.
- Executor Framework 
    - Overview
    - Using Executor Framework
    - Thread Pools
    - Types of Thread Pool
    - Benefits of Executor Framework 
    
- Callables & Future
    - How threads can return data?
- Coding Problems
    - Multi-threaded Merge Sort
    - Download Manager 
    - Image Processing App
    - Scheduled Executor 
    
- Synchronization Problems Introduction
    - Adder - Subtractor


# Overview
Imagine you have a computer program that needs to do several tasks at the same time. For example, your program might need to download files, process data, and update a user interface simultaneously. In the traditional way of programming, you might use threads to handle these tasks. However, managing threads manually can be complex and error-prone.
Java ExecutorService implementations let you stay focused on tasks that need to be run, rather than thread creation and management.

Think of a chef in a kitchen as your program. The chef has multiple tasks like chopping vegetables, cooking pasta, and baking a cake. Instead of the chef doing each task one by one, the chef hires sous-chefs (threads) to help. The chef (Executor Framework) can assign tasks to sous-chefs efficiently, ensuring that multiple tasks are happening simultaneously, and the kitchen operates smoothly.

In Java, the Executor Framework provides a convenient way to implement this idea in your code, making it more readable, maintainable, and efficient. It simplifies the process of managing tasks concurrently, so you can focus on solving the problems your program is designed to address.



## Using Executor Framework 
The Executor Framework in Java provides a high-level and flexible framework for managing and controlling the execution of tasks in concurrent programming. It is part of the `java.util.concurrent` package and was introduced in Java 5 to simplify the development of concurrent applications. The primary motivation behind using the Executor Framework is to abstract away the complexities of thread management, providing a clean and efficient way to execute tasks asynchronously.

Now, let's look at a real-world example to illustrate the use of the Executor Framework. Consider a scenario where you have a set of tasks that need to be executed concurrently to improve performance. We'll use a ThreadPoolExecutor for this example:

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ExecutorDemo {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(10);
        for(int i=0; i<100;i++){
            executor.execute(new NumberPrinter(i));
        }
        executor.shutdown();
    }
}
```
Here `NumberPrinter()` is runnable task as created earlier. The `Executor` interface is used to execute tasks. It is a generic interface that can be used to execute any kind of task. The `Executor` interface has only one method:

```java
public interface Executor {
    void execute(Runnable command);
}
```

The `execute` method takes a `Runnable` object as a parameter. The `Runnable` interface is a functional interface that has only one method. Executors internally use a thread pool to execute the tasks. The `execute` method is non-blocking. It returns immediately after submitting the task to the thread pool. The `execute` method is used to execute tasks that do not return a result. A thread pool is a collection of threads that are used to execute tasks. Instead of creating a new thread for each task, a thread pool reuses the existing threads to execute the tasks. This improves the performance of the application.

### Thread Pool - Deep Dive
- Creating threads, destroying threads, and then creating them again can be expensive.
- A thread pool mitigates the cost, by keeping a set of threads around, in a pool, for current and future work.
- Threads, once they complete one task, can then be reassigned to another task, without the expense of destroying that thread and creating a new one.

A thread pool consists of three components.
1. **Worker Threads** are available in a pool to execute tasks. They're pre-created and kept alive, throughout the lifetime of the application.

2. Submitted Tasks are placed in a **First-In First-Out queue**.  Threads pop tasks from the queue, and execute them, so they're executed in the order they're submitted.

3. The **Thread Pool Manager** allocates tasks to threads, and ensures proper thread synchronization.

### Types of Thread Pool
In Java, the `ExecutorService` interface, along with the `ThreadPoolExecutor` class, provides a flexible thread pool framework. Here are five types of thread pools in Java, each with different characteristics:

## 1. FixedThreadPool

- **Description:** A thread pool with a fixed number of threads.
- **Characteristics:**
  - Reuses a fixed number of threads for all submitted tasks.
  - If a thread is idle, it will be reused for a new task.
  - If all threads are busy, tasks are queued until a thread becomes available.
- **Creation:**
```java
  ExecutorService executor = Executors.newFixedThreadPool(nThreads);
```

## 2. CachedThreadPool

- **Description:** A thread pool that dynamically adjusts the number of threads based on demand.
- **Characteristics**
    - Creates new threads as needed, but reuses idle threads if available.
    - Threads that are idle for a certain duration are terminated.
    - Suitable for handling a large number of short-lived tasks.

```java
ExecutorService executor = Executors.newCachedThreadPool();
```
## 3. SingleThreadExecutor

- **Description:** A thread pool with only one thread.
- **Characteristics:**
    - Executes tasks sequentially in the order they are submitted.
    - Useful for tasks that need to be executed in a specific order or when a single thread is sufficient.
```java
ExecutorService executor = Executors.newSingleThreadExecutor();
```

## 4.ScheduledThreadPool

- **Description**: A thread pool that supports scheduling of tasks.
- **Characteristics**:
    - Similar to FixedThreadPool but with added support for scheduling tasks at fixed rates or delays.
    - Suitable for periodic tasks or tasks that need to be executed after a certain delay.
```java
ScheduledExecutorService executor = Executors.newScheduledThreadPool(nThreads);
```

## 5. WorkStealingPool

- **Description**: Introduced in Java 8, it's a parallelism-friendly thread pool.
- **Characteristics:**
    - Creates a pool of worker threads that dynamically adapt to the number of available processors.
    - Each worker thread has its own task queue.
    - Suitable for parallel processing tasks.

```java
ExecutorService executor = Executors.newWorkStealingPool();
```
These different types of thread pools cater to various scenarios and workloads. The choice of a thread pool depends on factors such as task characteristics, execution requirements, and resource constraints in your application.

# Benefits of Executor Framework

#### 1. Simplifies Task Execution
The Executor Framework makes it easier to execute tasks concurrently. It abstracts away the low-level details of managing threads, so you don't have to worry about creating and controlling them yourself.

#### 2. Efficient Resource Utilization:
When you have many tasks to perform, creating a new thread for each task can be inefficient. The Executor Framework provides a pool of threads that can be reused for multiple tasks. This reuse of threads reduces the overhead of creating and destroying threads for every task.

#### 3. Better Control and Flexibility
With the Executor Framework, you can control how many tasks can run simultaneously, manage the lifecycle of threads, and specify different policies for task execution. This level of control is important for optimizing the performance of your program.

#### 4. Enhanced Scalability
When your program needs to handle more tasks, the Executor Framework makes it easier to scale. You can adjust the size of the thread pool to accommodate more tasks without rewriting a lot of code.

#### 5. Task Scheduling
The framework allows you to schedule tasks to run at specific times or after certain intervals. This is useful for scenarios where you want to automate repetitive tasks or execute tasks at specific points in time.

### Summary
- Managing threads manually can be complex and error-prone.
- It can lead to complex issues like resource contention, thread creation overhead, and scalability challenges.
- For these reasons, you'll want to use an ExecutorService, even when working with a single thread.


----


## Callable and Future

Runnables do not return a result. If we want to execute a task that returns a result, we can use the `Callable` interface. The `Callable` interface is a functional interface that has only one method:

```java
public interface Callable<V> {
    V call() throws Exception;
}
```

The `call` method returns a result of type `V`. The `call` method can throw an exception. The `Callable` interface is used to execute tasks that return a result.
For instance we can use the `Callable` interface to execute a task that returns the sum of two numbers:

```java
Callable<Integer> sumTask = () -> 2 + 3;
```

In order to execute a task that returns a result, we can use the `submit` method of the `ExecutorService` interface. The `submit` method takes a `Callable` object as a parameter. The `submit` method returns a `Future` object. The `Future` interface has a method called `get` that returns the result of the task. The `get` method is a blocking method. It waits until the task is completed and then returns the result of the task. 

```java
ExecutorService executorService = Executors.newCachedThreadPool();
Future<Integer> future = executorService.submit(() -> 2 + 3);
Integer result = future.get();
```

Futures can be used to cancel tasks. The `Future` interface has a method called `cancel` that can be used to cancel a task. The `cancel` method takes a boolean parameter. If the boolean parameter is `true`, the task is cancelled even if the task is already running. If the boolean parameter is `false`, the task is cancelled only if the task is not running. 

```java
ExecutorService executorService = Executors.newCachedThreadPool();
Future<Integer> future = executorService.submit(() -> 2 + 3);
future.cancel(false);
```

---
## Coding Problem 1 : Merge Sort 
Implement multi-threaded merge sort.

**Solution**
**Sorter.java**
```java
import java.util.ArrayList;
import java.util.concurrent.Callable;
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Future;

public class Sorter implements Callable<List<Integer>> {
    private List<Integer> arr;
    private ExecutorService executor;
    Sorter(List<Integer> arr,ExecutorService executor){
        this.arr = arr;
        this.executor = executor;

    }
    @Override
    public List<Integer> call() throws Exception {
        //Business Logic
        //base case
        if(arr.size()<=1){
            return arr;
        }

        //recursive case
        int n = arr.size();
        int mid = n/2;

        List<Integer> leftArr = new ArrayList<>();
        List<Integer> rightArr = new ArrayList<>();

        //Division of array into 2 parts
        for(int i=0;i<n;i++){
            if(i<mid){
                leftArr.add(arr.get(i));
            }
            else{
                rightArr.add(arr.get(i));
            }
        }

        //Recursively Sort the 2 array
        Sorter leftSorter = new Sorter(leftArr,executor);
        Sorter rightSorter = new Sorter(rightArr,executor);

        Future<List<Integer>> leftFuture = executor.submit(leftSorter);
        Future<List<Integer>> rightFuture = executor.submit(rightSorter);

        leftArr = leftFuture.get();
        rightArr = rightFuture.get();


        //Merge
        List<Integer> output = new ArrayList<>();
        int i=0;
        int j=0;
        while(i<leftArr.size() && j<rightArr.size()){
            if(leftArr.get(i)<rightArr.get(j)){
                output.add(leftArr.get(i));
                i++;
            }
            else{
                output.add(rightArr.get(j));
                j++;
            }
        }
        // copy the remaining elements
        while(i<leftArr.size()){
            output.add(leftArr.get(i));
            i++;
        }
        while(j<rightArr.size()){
            output.add(rightArr.get(j));
            j++;
        }
        return output;
    }
}
```

**Main.java**
```java
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class Main {
    public static void main(String[] args) throws Exception {
        List<Integer> l = List.of(7,3,1,2,4,6,17,12);
        ExecutorService executorService = Executors.newCachedThreadPool();

        Sorter sorter = new Sorter(l,executorService);
        Future<List<Integer>> output = executorService.submit(sorter);
        System.out.println(output.get()); //Blocking Code
        executorService.shutdown();
    }
}
```


I'll do a step-by-step dry run of the parallel merge sort implementation. Let's use a small example array to trace through the code execution.

Let's say we have an initial array `[5, 2, 9, 1, 7, 6]` and an executor service has been set up.

### Initial Call

```java
Sorter initialSorter = new Sorter([5, 2, 9, 1, 7, 6], executor);
Future<List<Integer>> result = executor.submit(initialSorter);
List<Integer> sortedArray = result.get();
```

### Dry Run:

1. **Initial State**:
   - `arr = [5, 2, 9, 1, 7, 6]`
   - `n = 6`, `mid = 3`

2. **Division**:
   - `leftArr = [5, 2, 9]`
   - `rightArr = [1, 7, 6]`

3. **Create Sorters**:
   - `leftSorter = new Sorter([5, 2, 9], executor)`
   - `rightSorter = new Sorter([1, 7, 6], executor)`

4. **Submit tasks to executor**:
   - `leftFuture = executor.submit(leftSorter)`
   - `rightFuture = executor.submit(rightSorter)`

5. **Wait for completion with `get()`**:
   - `leftArr = leftFuture.get()`
   - `rightArr = rightFuture.get()`

   Let's trace what happens with `leftSorter`:

   5.1. **Left Task Execution**:
      - `arr = [5, 2, 9]`
      - `n = 3`, `mid = 1`
      - `leftArr = [5]`
      - `rightArr = [2, 9]`
      - Create and submit: `leftSorter([5], executor)` and `rightSorter([2, 9], executor)`

      5.1.1. **Left-Left Task**:
         - `arr = [5]`
         - Base case triggered (size <= 1)
         - Return `[5]`

      5.1.2. **Left-Right Task**:
         - `arr = [2, 9]`
         - `n = 2`, `mid = 1`
         - `leftArr = [2]`, `rightArr = [9]`
         - Create and submit new sorters
         - Both hit base case, return `[2]` and `[9]`
         - Merge: `[2, 9]`
      
      5.1.3. Left task's final merge: `[5]` and `[2, 9]` → `[2, 5, 9]`

   5.2. **Right Task Execution**:
      - `arr = [1, 7, 6]`
      - `n = 3`, `mid = 1`
      - `leftArr = [1]`
      - `rightArr = [7, 6]`
      - Create and submit: `leftSorter([1], executor)` and `rightSorter([7, 6], executor)`

      5.2.1. **Right-Left Task**:
         - `arr = [1]`
         - Base case triggered
         - Return `[1]`

      5.2.2. **Right-Right Task**:
         - `arr = [7, 6]`
         - `n = 2`, `mid = 1`
         - `leftArr = [7]`, `rightArr = [6]`
         - Create and submit new sorters
         - Both hit base case, return `[7]` and `[6]`
         - Merge: `[6, 7]`
      
      5.2.3. Right task's final merge: `[1]` and `[6, 7]` → `[1, 6, 7]`

6. **Final Merge**:
   - `leftArr = [2, 5, 9]`
   - `rightArr = [1, 6, 7]`
   - Initialize `output = []`, `i = 0`, `j = 0`
   - Compare and merge:
     - Compare `2` and `1`: Add `1`, `j++`
     - Compare `2` and `6`: Add `2`, `i++`
     - Compare `5` and `6`: Add `5`, `i++`
     - Compare `9` and `6`: Add `6`, `j++`
     - Compare `9` and `7`: Add `7`, `j++`
     - `j` reaches end of `rightArr`, add remaining `9` from `leftArr`
   - Final output: `[1, 2, 5, 6, 7, 9]`


You've identified an important detail. Let's look at where the intermediate merge results are stored at each level of recursion.

In the `call()` method, the merge operation always stores its result in a local `output` array which is initialized at each level of recursion:

```java
// Merge
List<Integer> output = new ArrayList<>();  // Local output array initialized here
int i=0;
int j=0;
while(i<leftArr.size() && j<rightArr.size()){
    // Merge logic fills this output array
    // ...
}
// Copy remaining elements
// ...
return output;  // This output array becomes the return value
```

At each level of recursion:

1. A new `output` ArrayList is created locally
2. The merging of sorted subarrays populates this local `output` array
3. This `output` array is returned by the `call()` method

The key part is what happens to this returned array:
- For recursive calls, the returned `output` becomes the value obtained from `leftFuture.get()` or `rightFuture.get()`
- These values are assigned back to `leftArr` and `rightArr`:
  ```java
  leftArr = leftFuture.get();    // Stores the result of the left recursive merge
  rightArr = rightFuture.get();  // Stores the result of the right recursive merge
  ```

So the sub-level merge results flow upward through the recursion by:
1. Being returned by each `call()` method
2. Being captured by `Future.get()` calls
3. Being reassigned to the `leftArr` and `rightArr` variables
4. These updated arrays are then merged at the current level

This creates a chain where:
- Base case returns single-element arrays
- These get merged into small sorted arrays
- Those arrays flow up to become inputs to higher-level merges
- Until finally, the top-level merge produces the completely sorted array

This is why the code reuses the `leftArr` and `rightArr` variables to store both the initial division of the array and later the sorted results of the recursive calls.

### Key Observations:

1. This algorithm implements a parallel merge sort using Java's executor service.
2. Each recursive call creates two new `Sorter` tasks that are submitted to the executor.
3. The `Future.get()` calls synchronize the execution, waiting for subtasks to complete.
4. The merge operation combines the sorted subarrays.
5. The base case is reached when an array has 0 or 1 elements.

This implementation can be more efficient than sequential merge sort on multi-core systems because the sorting of subarrays happens in parallel, potentially using multiple CPU cores simultaneously.

Yes, exactly! The `Future.get()` calls **synchronize execution** by blocking the current thread until the corresponding **subtask (Sorter) completes**.

### **How Future.get() Synchronizes Execution**
1. The `executor.submit(leftSorter)` and `executor.submit(rightSorter)` **run the sorting tasks asynchronously**.
2. However, when `Future.get()` is called:
   - The **main thread (or calling thread)** **pauses execution** and **waits** until the child threads complete.
   - Only when both left and right subarrays are **sorted** does execution continue.
3. This ensures that merging happens **only after** both halves are fully sorted.

### **Key Code Section:**
```java
Future<List<Integer>> leftFuture = executor.submit(new Sorter(leftArr, executor));
Future<List<Integer>> rightFuture = executor.submit(new Sorter(rightArr, executor));

leftArr = leftFuture.get();  // Blocks until left sublist is sorted
rightArr = rightFuture.get();  // Blocks until right sublist is sorted
```

### **What Happens Without `Future.get()`?**
If we **do not** use `Future.get()`, the sorting tasks would be submitted but **not necessarily completed** before merging, leading to **incorrect results or errors**.

### **Final Takeaway**
✔️ `Future.get()` **ensures proper synchronization**, making sure that merging happens **only after both halves are sorted**.


## Coding Problem 2 : Download Manager (Homework)
Consider a simple download manager application that needs to download multiple files concurrently. Implement the download manager using the Java Executor Framework.
Requirements:
- The download manager should be able to download multiple files simultaneously.
- Each file download is an independent task that can be executed concurrently.
- The download manager should use a thread pool from the Executor Framework to manage and execute the download tasks.
- Implement a mechanism to track the progress of each download task and display it to the user.

```java
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

class DownloadManager {
    private ExecutorService executorService;

    public DownloadManager(int threadPoolSize) {
        // TODO: Initialize the ExecutorService with a fixed-size thread pool.
    }

    public void downloadFiles(List<String> fileUrls) {
        // TODO: Implement a method to submit download tasks for each file URL.
    }

    // TODO: Implement a method to track and display the progress of each download task.

    public void shutdown() {
        // TODO: Shutdown the ExecutorService when the download manager is done.
    }
}
```
```java
public class DownloadManagerApp {
    public static void main(String[] args) {
        // TODO: Create a DownloadManager instance with an appropriate thread pool size.
        // TODO: Test the download manager by downloading multiple files concurrently.
        // TODO: Display the progress of each download task.
        // TODO: Shutdown the download manager after completing the downloads.
    }
}
```

**Tasks for Implementation**
- Initialize the ExecutorService in the DownloadManager constructor.
- Implement the downloadFiles method to submit download tasks for each file URL using the ExecutorService.
- Implement a mechanism to track and display the progress of each download task.
- Test the download manager in the DownloadManagerApp by downloading multiple files concurrently.
- Shutdown the ExecutorService when the download manager is done.
- Feel free to adapt and extend the code as needed. This example focuses on using the Executor Framework for concurrent file downloads in a download manager application.

**Solution**
```java
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

class DownloadTask implements Runnable {
    private String fileUrl;

    public DownloadTask(String fileUrl) {
        this.fileUrl = fileUrl;
    }

    @Override
    public void run() {
        // Simulate file download
        System.out.println("Downloading file from: " + fileUrl);

        // Simulate download progress
        for (int progress = 0; progress <= 100; progress += 10) {
            System.out.println("Progress for " + fileUrl + ": " + progress + "%");
            try {
                Thread.sleep(500); // Simulate download time
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }

        System.out.println("Download complete for: " + fileUrl);
    }
}

class DownloadManager {
    private ExecutorService executorService;

    public DownloadManager(int threadPoolSize) {
        executorService = Executors.newFixedThreadPool(threadPoolSize);
    }

    public void downloadFiles(List<String> fileUrls) {
        for (String fileUrl : fileUrls) {
            DownloadTask downloadTask = new DownloadTask(fileUrl);
            executorService.submit(downloadTask);
        }
    }

    public void shutdown() {
        executorService.shutdown();
    }
}

public class DownloadManagerApp {
    public static void main(String[] args) {
        DownloadManager downloadManager = new DownloadManager(3); // Use a thread pool size of 3

        List<String> filesToDownload = List.of("file1", "file2", "file3", "file4", "file5");

        downloadManager.downloadFiles(filesToDownload);

        // Display progress (simulated)
        // Note: In a real-world scenario, you might need to implement a more sophisticated progress tracking mechanism.
        for (int i = 0; i < 10; i++) {
            System.out.println("Main thread is doing some work...");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }

        downloadManager.shutdown();
    }
}
```

## Coding Problem 3 : Image Processing 
Many image processing applications like Lightroom & Photoshop use multiple threads to process an image quickly. In this problem, you will build a simplified image repainting task using multiple threads, the repainting task here simply doubles the value of every pixel stored in the form of a 2D array. Take Input a NXN matrix and repaint it by using 4 threads, one for each quadrant.

**Solution**
Repainting a 2D array using four threads can be achieved by dividing the array into quadrants, and assigning each quadrant to a separate thread for repainting. 

This example divides the 2D array into four quadrants and assigns each quadrant to a separate thread for repainting. The ArrayRepainterTask class represents the task for repainting a specific quadrant. The program then uses an ExecutorService with a fixed thread pool to concurrently execute the tasks. Finally, it prints the repainted 2D array.

Below is an example code using the Java Executor Framework

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

class ArrayRepainterTask implements Runnable {
    private final int[][] array;
    private final int startRow;
    private final int endRow;
    private final int startCol;
    private final int endCol;

    public ArrayRepainterTask(int[][] array, int startRow, int endRow, int startCol, int endCol) {
        this.array = array;
        this.startRow = startRow;
        this.endRow = endRow;
        this.startCol = startCol;
        this.endCol = endCol;
    }

    @Override
    public void run() {
        // Simulate repainting for the specified quadrant
        for (int i = startRow; i <= endRow; i++) {
            for (int j = startCol; j <= endCol; j++) {
                array[i][j] = array[i][j] * 2; // Repaint by doubling the values (simulated)
            }
        }
    }
}

public class ArrayRepaintingExample {
    public static void main(String[] args) {
        int[][] originalArray = {
                {1, 2, 3, 4},
                {5, 6, 7, 8},
                {9, 10, 11, 12},
                {13, 14, 15, 16}
        };

        int rows = originalArray.length;
        int cols = originalArray[0].length;

        ExecutorService executorService = Executors.newFixedThreadPool(4);

        // Divide the array into four quadrants
        int midRow = rows / 2;
        int midCol = cols / 2;

        // Create tasks for each quadrant
        ArrayRepainterTask task1 = new ArrayRepainterTask(originalArray, 0, midRow - 1, 0, midCol - 1);
        ArrayRepainterTask task2 = new ArrayRepainterTask(originalArray, 0, midRow - 1, midCol, cols - 1);
        ArrayRepainterTask task3 = new ArrayRepainterTask(originalArray, midRow, rows - 1, 0, midCol - 1);
        ArrayRepainterTask task4 = new ArrayRepainterTask(originalArray, midRow, rows - 1, midCol, cols - 1);

        // Submit tasks to the ExecutorService
        executorService.submit(task1);
        executorService.submit(task2);
        executorService.submit(task3);
        executorService.submit(task4);

        // Shutdown the ExecutorService
        executorService.shutdown();

        // Wait for all tasks to complete
        while (!executorService.isTerminated()) {
            // Wait
        }

        // Print the repainted array
        for (int[] row : originalArray) {
            for (int value : row) {
                System.out.print(value + " ");
            }
            System.out.println();
        }
    }
}
```
## Coding Problem 4: Scheduled Executor
Write a Java program that uses ScheduledExecutorService to schedule a task to run periodically. Implement a task that prints a message “Hello” at fixed intervals of 5s.
```java
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

public class ScheduledExecutorExample {

    public static void main(String[] args) {
        // Create a ScheduledExecutorService with a single thread
        ScheduledExecutorService scheduledExecutorService = Executors.newSingleThreadScheduledExecutor();

        // Schedule the task to run periodically every 5 seconds
        scheduledExecutorService.scheduleAtFixedRate(() -> {
            System.out.println("Hello");
        }, 0, 5, TimeUnit.SECONDS);

        // Sleep for a while to allow the task to run multiple times
        try {
            Thread.sleep(20000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        // Shutdown the ScheduledExecutorService
        scheduledExecutorService.shutdown();
    }
}

```



---


The line:  

```java
// Sleep for a while to allow the task to run multiple times
try {
    Thread.sleep(20000);
} catch (InterruptedException e) {
    e.printStackTrace();
}
```
  
### **Explanation**
- `Thread.sleep(20000);` pauses the **current thread** for **20 seconds (20,000 milliseconds)**.
- This is usually done to **allow background tasks (like threads in an executor) to complete their execution**.
- If the **main thread** exits too early, the application might terminate before worker threads complete their tasks.
- The `try-catch` block handles **InterruptedException**, which occurs if another thread interrupts this sleeping thread.

### **Why is this used?**
1. **Allow background tasks to run**  
   - If you're using a **multi-threaded program (e.g., ExecutorService, TimerTasks, or background threads)**, you might want the main thread to **pause** so that other threads get time to complete their execution.

2. **Avoid premature termination**  
   - If the main thread **exits too early**, worker threads may not finish execution.
   - `Thread.sleep(20000)` **keeps the main thread alive** for 20 seconds before proceeding (or terminating).

### **Example Scenario**
Imagine you have an `ExecutorService` running tasks:
```java
ExecutorService executor = Executors.newFixedThreadPool(4);
executor.submit(() -> {
    for (int i = 0; i < 10; i++) {
        System.out.println("Task running: " + i);
        try {
            Thread.sleep(1000); // Simulating some work
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
});
```
Without `Thread.sleep(20000)`, if the **main thread exits**, the application might stop before the task completes.

### **Alternative Approach**
Instead of `Thread.sleep(20000)`, a **better approach** is:
```java
executor.shutdown();
executor.awaitTermination(20, TimeUnit.SECONDS);
```
This **waits** for all threads to complete within **20 seconds** before shutting down.

Would you like a detailed dry run for this? 🚀



## Synchronisation

Whenever we have multiple threads that access the same resource, we need to make sure that the threads do not interfere with each other. This is called synchronisation.

Synchronisation can be seen in the adder and subtractor example. The adder and subtractor threads access the same counter variable. If the adder and subtractor threads do not synchronise, the counter variable can be in an inconsistent state.

* Create a count class that has a count variable.
* Create two different classes `Adder` and `Subtractor`.
* Accept a count object in the constructor of both the classes.
* In `Adder`, iterate from 1 to 10000 and increment the count variable by 1 on each iteration.
* In `Subtractor`, iterate from 1 to 10000 and decrement the count variable by 1 on each iteration.
* Print the final value of the count variable.
* What would the ideal value of the count variable be?
* What is the actual value of the count variable?
* Try to add some delay in the `Adder` and `Subtractor` classes using inspiration from the code below. What is the value of the count variable now?


**Steps to implement**
- Implement Adder & Subtractor
- Shared counter via constructor
- Create a package called addersubtractor
- Create two tasks under adder and subtractor

Adder
```java
package addersubtractor;
    public class Adder implements Runnable {
        private Count count;
        public Adder (Count count) {
                this.count = count;
              }
         @Override
        public void run() {
            for (int i = 1 ; i <= 100; ++ 1) {
                    count.value += i;
            }
}
```
Subtracter
```java
package addersubtractor;
    public class Subtractor implements Runnable  {
        private Count count;
        public Subtractor (Count count) {
            this.count = count
            }
        @Override
        public void run() {
            for (int i = 1 ; i <= 100; ++ 1) {
                    count.value -= i;
            }
}
```
Count class
```java
package addersubtractor;
    public class Count {
        int value = 0;
    }
```
Now, let’s make our client class here:

```java
public static void main(String[] args) {
    Count count = new Count;
    Adder adder = new Adder (count);
    Subtractor subtractor = new Subtractor (count);
    Thread t1 = new Thread (adder);
    Thread t2 = new Thread (subtractor);
    t1.start();
    t2.start();
    t1.join();
    t2.join();
    
    system.out.println(count.value);
```
Output is some random number every time we run the code.

Now, this particular problem is known to be a data synchronization problem.
This happens because the same data object is shared among various multi-threads, and they both are trying to modify the same data. This is an unexpected result that we have seen, but we will continue this in the next tutorial.


==========

### **Use of `join()` in Threads**
The `join()` method in Java ensures that one thread waits for another to complete before continuing execution. 

In your code:
```java
t1.join();
t2.join();
```
This means:
1. **Main thread waits** for `t1` (Adder) to finish execution.
2. Once `t1` finishes, the **main thread waits** for `t2` (Subtractor) to finish execution.
3. Only after both `t1` and `t2` complete, the main thread resumes execution.

---

### **Why is `join()` Used?**
- Ensures that the main thread **does not proceed prematurely**.
- Prevents the **final count from being printed before updates** by `Adder` and `Subtractor`.

Without `join()`, the program might print the final count **before threads finish**, leading to incorrect results.

---

### **Example Without `join()`**
```java
public class WithoutJoin {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> System.out.println("Thread 1 Running"));
        Thread t2 = new Thread(() -> System.out.println("Thread 2 Running"));
        
        t1.start();
        t2.start();

        System.out.println("Main thread finished");
    }
}
```
#### **Possible Output (Unpredictable)**
```
Main thread finished
Thread 1 Running
Thread 2 Running
```
- Here, "Main thread finished" may print **before** `t1` and `t2` complete.

---

### **Example With `join()`**
```java
public class WithJoin {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> System.out.println("Thread 1 Running"));
        Thread t2 = new Thread(() -> System.out.println("Thread 2 Running"));

        t1.start();
        t2.start();

        t1.join(); // Main thread waits for t1
        t2.join(); // Main thread waits for t2

        System.out.println("Main thread finished");
    }
}
```
#### **Guaranteed Output**
```
Thread 1 Running
Thread 2 Running
Main thread finished
```
- Ensures **both threads finish** before the main thread exits.

---

### **Conclusion**
Using `join()` ensures **synchronization** between threads and prevents unexpected results due to race conditions.

In Java, every program starts execution from the main thread, which is created by the JVM when the main() method runs.

