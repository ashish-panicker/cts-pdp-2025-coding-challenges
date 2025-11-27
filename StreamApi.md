# **PROBLEM 1 — Fraud Detection Stream Pipeline (Multi-Stage Transformations)**

### **Context**

You are building a **fraud detection module** for a fintech platform. You receive millions of transaction records and must compute fraud risk scores using **multi-stage Stream pipelines**, **grouping**, **partitioning**, and **custom collectors**.

---

## **Dataset — `transactions.csv`**

```
txnId,userId,amount,currency,timestamp,ip,location,merchant,channel
T1001,U001,950,USD,2025-01-10T10:15:00Z,192.168.1.4,NY,Amazon,WEB
T1002,U001,12000,USD,2025-01-10T10:16:02Z,10.0.0.2,NY,BestBuy,WEB
T1003,U002,199,EUR,2025-01-10T11:01:14Z,172.16.10.2,Berlin,Amazon,APP
T1004,U002,4500,EUR,2025-01-10T11:03:22Z,172.16.10.2,Paris,Ebay,APP
T1005,U003,50,INR,2025-01-10T11:07:45Z,103.21.44.1,Mumbai,Flipkart,WEB
T1006,U003,90000,INR,2025-01-10T11:20:12Z,8.8.8.8,UnknownLocation,UnknownMerchant,WEB
T1007,U004,240,GBP,2025-01-10T12:10:50Z,192.168.1.10,London,Amazon,APP
T1008,U004,5000,GBP,2025-01-10T12:12:01Z,192.168.1.10,London,Tesco,POS
```

---

## **Task & Instructions**

1. Compute **fraud score** per user:

   * High value transaction?
   * IP anomaly?
   * Location change within short window?
   * Unknown merchant?
2. Use **Stream pipeline**:

   * `groupingBy(userId)`
   * custom `Collector` to aggregate:

     * total amount
     * number of risky transactions
     * average interval between txns
3. Partition users into:

   * **High risk** vs **Low risk**
4. Final output structure:

```
{
  highRiskUsers: List<UserFraudReport>,
  lowRiskUsers: List<UserFraudReport>
}
```

5. Must use:

   * `collectingAndThen`
   * `mapping`
   * `reduce`
   * `flatMap`
   * `Comparator.comparing(...)` with method references
   * **No loops at all**

---

---

# **PROBLEM 2 — Build an In-Memory Search Engine (Functional Inverted Index)**

### **Context**

You need to build a **search index** using Streams + Lambdas that supports:

* Keyword extraction
* Counting frequency
* Ranking documents
* Multi-level grouping

---

## **Dataset — `documents.csv`**

```
docId,title,content
D1,Java Streams,"Streams are powerful. Streams give functional capabilities. Lambda and Stream API are essential for modern Java."
D2,Microservices,"Microservices leverage message queues, Kafka, and event-driven streams."
D3,Cloud,"Cloud native architectures require scalable pipelines, async streams, serverless services."
D4,Security,"Security scanning, encryption, API gateways and identity management are critical."
D5,AI,"AI systems require pipelines, vectors, transformers, streams of data."
```

---

## **Task & Instructions**

Build a functional **inverted index** using Streams:

1. Tokenize text using `flatMap`
2. Normalize words (lowercase, remove punctuation)
3. Build map:

```
Map<String, List<DocumentFrequency>>
```

Where:

```
word -> [(docId, countInThisDoc), ...]
```

4. Generate global word stats:

   * total frequency
   * top-10 words
5. Must use:

   * `Collectors.groupingBy`
   * `filter`
   * `flatMap`
   * `Collectors.counting()`
   * `sorted()`
6. No loops, no mutable external structures.

---

---

# **PROBLEM 3 — Employee Compensation Engine (Advanced Collectors)**

### **Context**

You are designing a real-time compensation analysis engine for HR analytics.
You must use:

* Multi-level grouping
* Partitioning
* Custom collectors
* Mapping to immutable DTOs
* Statistical operations (max, min, average)

---

## **Dataset — `employees.csv`**

```
empId,name,department,role,salary,bonus,rating,location
E001,Alice,Engineering,Sr Developer,150000,10000,4.8,NY
E002,Bob,Engineering,Tech Lead,180000,15000,4.6,NY
E003,Charlie,Engineering,Developer,120000,8000,Austin,TX
E004,Diana,Product,Product Manager,160000,12000,Berlin
E005,Edward,Product,Sr PM,190000,20000,London
E006,Frank,QA,Sr QA,140000,9000,SF
E007,Gina,QA,QA Engineer,110000,5000,SF
E008,Hannah,Design,Designer,90000,4000,SF
```

---

## **Task & Instructions**

1. Compute:

   * Compensation = salary + bonus
   * Weighted score = compensation * rating
2. Group by:

   ```
   department -> role -> List<Employees>
   ```
3. For each department compute:

   * highest salary
   * lowest bonus
   * average compensation
   * top 2 performers
4. Partition into:

   * US employees
   * Non-US employees
5. Use ONLY:

   * `Collectors.groupingBy`
   * `Collectors.collectingAndThen`
   * `Comparator.comparing`
   * `map()`
   * `reduce()`
   * `toMap()`
   * No loops, no traditional for.

---

---

# **PROBLEM 4 — Complex Inventory Pipeline (ForkJoin + Parallel Streams)**

### **Context**

A global e-commerce company needs to reconcile inventory from multiple warehouses.
This must be done **concurrently** using Parallel Streams + ForkJoinPool.

---

## **Dataset — `inventory.csv`**

```
warehouseId,productId,productName,stock,lastUpdated
W1,P1001,Laptop,120,2025-02-10T10:00:00Z
W2,P1001,Laptop,80,2025-02-10T10:00:01Z
W3,P1001,Laptop,5,2025-02-09T22:00:00Z
W1,P1002,Keyboard,500,2025-02-10T10:05:00Z
W2,P1002,Keyboard,510,2025-02-10T10:05:10Z
W3,P1003,Mouse,0,2025-02-10T10:06:00Z
W1,P1003,Mouse,50,2025-02-10T10:06:10Z
W2,P1004,Monitor,20,2025-02-10T08:00:00Z
W3,P1004,Monitor,22,2025-02-10T07:59:00Z
```

---

## **Task & Instructions**

Build a parallel stream pipeline that:

1. Aggregates stock by product:

   ```
   productId -> totalStock
   ```
2. Detects inconsistent updates:

   * lastUpdated differs by > 30 seconds between warehouses
3. Identifies low-stock items:

   * total < 50
4. Produces a final report:

```
{
  aggregatedStock: Map<ProductId, TotalStock>,
  inconsistentProducts: List<ProductId>,
  lowStockProducts: List<ProductId>
}
```

### Requirements:

* Must explicitly configure a **custom ForkJoinPool**
* Must demonstrate CPU-core parallelism
* Must correctly use:

  * `parallelStream()`
  * `Collectors.groupingByConcurrent`
  * `flatMap`
  * `timing analysis` using nanoTime

---

---

# **PROBLEM 5 — Streaming Graph Processor (Adjacency Expansion + Cycles)**

### **Context**

You are building a graph dependency analyzer for microservices.
Input is a list of service → dependencies.

You must:

* Flatten dependencies
* Detect cycles
* Find leaf nodes
* Build sorted dependency chains
* Using **pure Stream API** (no recursion, no loops)

---

## **Dataset — `service_graph.csv`**

```
service,dependsOn
AuthService,UserService
UserService,EmailService
EmailService,NotificationService
NotificationService,
OrderService,UserService
OrderService,InventoryService
InventoryService,WarehouseService
WarehouseService,
PaymentService,OrderService
```

---

## **Task & Instructions**

1. Build a complete dependency adjacency list:

```
service -> all dependencies (recursive)
```

but **using Stream operations only**:

* `flatMap`
* `map`
* `distinct`
* `limit` (to detect cycle)
* `collect(toSet())`

2. Detect cycles such as:

   * A → B → C → A
     (Your data doesn’t contain cycles, but your processor must identify if added later.)

3. Identify leaf services (no dependencies).

4. Sort services by **depth** (chain length).

5. Build a final structure:

```
{
  service: {
     directDependencies: [...],
     allDependencies: [...],
     depth: n,
     isLeaf: true/false
  }
}
```

---