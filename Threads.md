# **THREAD-BASED CODING CHALLENGES (Descriptive + Real-World)**

---

# **SECTION 1 — Thread & Runnable Challenges**

## **Challenge 1 — Parallel File Downloader**

**Description:**
You are given a list of 10 file URLs (strings only, no network call needed).
Create a program that:

1. Starts **one thread per file** using `Runnable`
2. Each thread prints

   * its own name
   * which “file” it's downloading
   * % progress (0–100) simulated
3. Main thread should print a message:
   **“All downloads started, main thread is free.”**

**Tested Concepts:**
 Thread creation
 Naming threads
 Simulated work via sleep
 Thread lifecycle awareness

---

## **Challenge 2 — Ordered Execution Using Join**

**Description:**
Create 3 threads:

* Thread A prints 1 → 5
* Thread B prints 6 → 10
* Thread C prints “Completed”

Enforce this mandatory sequence:

```
A runs → B runs → C runs
```

**Rules:**

* Must use `join()`
* No synchronization, no wait/notify

**Tested Concepts:**
 Coordination using join
 Sequential orchestration
 Thread basics

---

## **Challenge 3 — Real-Time Clock (Runnable)**

**Description:**
Create a `ClockThread` class using Runnable.
It should print current time every 1 sec like:

```
[Clock] 14:43:01
[Clock] 14:43:02
...
```

Stop the clock after **10 seconds** using a shared flag.

**Tested Concepts:**
 Runnable
 Graceful thread stop
 Shared state

---

# **SECTION 2 — wait() & notify() Challenges**

## **Challenge 4 — Producer–Consumer (Single Value Buffer)**

**Description:**
Implement a buffer for integers with the following behavior:

1. Producer generates numbers 1–20
2. Consumer prints each number
3. Only **one** number can exist in the buffer at a time
4. Use `wait()` and `notify()`
5. Both threads must stop gracefully

**Expected Output Example:**

```
Produced: 1
Consumed: 1
Produced: 2
Consumed: 2
...
```

**Tested Concepts:**
 Classic wait-notify
 Shared object locking
 Correct condition checks

---

## **Challenge 5 — Print Even & Odd Numbers in Alternation**

**Description:**
Two threads should print:

```
Thread-1: 1
Thread-2: 2
Thread-1: 3
Thread-2: 4
...
```

Requirements:

* Use wait/notify
* One thread handles **odd**, the other **even**
* Ensure strict alternation until 20

**Tested Concepts:**
 Coordination logic
 Proper monitor lock usage
 Condition-based waiting

---

## **Challenge 6 — Blocking Queue Simulation (Without using java.util.concurrent)**

**Description:**
Implement a thread-safe `MyBlockingQueue<T>` with:

* `put(T item)`
* `take()`

Internally use:

* A list
* A capacity
* `wait()` and `notifyAll()`

Then create **5 producers** and **2 consumers**.

**Tested Concepts:**
 Designing custom blocking structure
 Multi-producer/multi-consumer correctness
 Safe concurrency

---

# **SECTION 3 — Future & ExecutorService Challenges**

## **Challenge 7 — Shopping Cart Price Calculator**

**Description:**
You have 3 independent tasks:

1. Load product price
2. Apply discount
3. Calculate tax

Use:

* `Callable<Double>` tasks
* FixedThreadPool
* `Future` to gather results

Finally combine:

```
finalPrice = price - discount + tax
```

Print output only after all tasks finish.

**Tested Concepts:**
 Callable
 Future.get()
 Parallelism
 Combining async results

---

## **Challenge 8 — Timeout-Aware Task**

**Description:**
Create a `Callable<String>` that sleeps for **5 seconds**.
Main thread should wait only **2 seconds**, then:

* Cancel the task
* Print: “Timeout reached. Task cancelled.”

**Tested Concepts:**
 Future cancellation
 Timeout versions of get()
 Handling long-running tasks

---

## **Challenge 9 — Search Aggregator**

**Description:**
Parallel search in 3 data sources:

* Title search
* Author search
* Keyword search

Each implemented as `Callable<List<String>>`.

Submit tasks to a pool, use Future, then merge all lists.

**Tested Concepts:**
 Multiple Future tasks
 Merging results
 Thread pool tuning

---

# **SECTION 4 — CompletableFuture Challenges (Intermediate → Advanced)**

## **Challenge 10 — Student Progress Report (Async)**

**Description:**
3 async tasks:

* Fetch attendance
* Fetch assignment marks
* Fetch behavior score

Use:

* `CompletableFuture.supplyAsync()`
* `allOf()`
* Combine for final score

**Tested Concepts:**
 Async pipelines
 Custom executors
 Non-blocking join

---

## **Challenge 11 — Web Crawler Simulator Using thenCompose + thenCombine**

**Description:**
Simulate:

1. `fetchPage(url)` → returns HTML
2. `extractLinks(page)` → returns list
3. `fetchMetadata(link)` for each link in parallel
4. Combine all metadata

Pipeline:

```
fetchPage
   → thenCompose(extractLinks)
       → thenCompose(fetch metadata for all links)
```

**Tested Concepts:**
 Dependent async tasks
 Parallel subtasks
 Combining multiple results

---

## **Challenge 12 — Async Order Processor with Exception Handling**

**Description:**

3 tasks:

* Validate order
* Call inventory service
* Call payment service

Rules:

* Each is `CompletableFuture`
* Use `handle()` or `exceptionally()` to recover
* If payment fails, order should be marked “FAILED”
* Otherwise “COMPLETED”

**Tested Concepts:**
 Error handling in async flows
 Real-world microservice simulation
 Branching logic in CompletableFuture chains

---

## **Challenge 13 — Parallel Report Generator with MDC Propagation**

**Description:**
Generate:

1. Sales report
2. Inventory report
3. Customer insights report

All in parallel using:

* Custom executor
* CompletableFuture
* MDC context propagation for logging traceId

Final combination with `thenApply()`.

**Tested Concepts:**
 Parallel computation
 Logging context propagation
 Async result merging

---

# **SECTION 5 — OPEN-ENDED DESIGN CHALLENGES**

## **Challenge 14 — Build Your Own Thread Scheduler**

**Description:**
Create a mini framework that:

* Accepts tasks (`Runnable`)
* Runs them based on assigned **priority**
* High priority must execute before low priority
* Use only `synchronized`, `wait()`, and `notify()`

**Tested Concepts:**
 Designing concurrency primitives
 Custom scheduler logic
 Complex synchronization

---

## **Challenge 15 — Micro-Batching with CompletableFuture**

**Description:**
You receive 50 incoming requests.
Group every **5 requests** into one batch:

* Build 10 batches
* Process each batch in parallel using CompletableFuture
* Use `allOf()` to know when all batches finished
* Return total time taken
