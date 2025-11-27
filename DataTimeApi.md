# **Problem 1 — Distributed System Event Ordering with Mixed Timezones**

Your microservices platform spans **3 regions**:

* `us-east-1` → America/New_York
* `eu-central-1` → Europe/Berlin
* `ap-south-1` → Asia/Kolkata

Each service logs events using its *local* `ZonedDateTime`.

You receive three event logs:

```
Service A: 2025-03-10T01:05:12-04:00[America/New_York]
Service B: 2025-03-10T08:35:10+01:00[Europe/Berlin]
Service C: 2025-03-10T13:20:55+05:30[Asia/Kolkata]
```

NY has just entered **DST** on March 9.
Berlin will enter DST later in March.

### **Task**

Write Java code to:

1. Normalize all events to **Instant**
2. Order them chronologically
3. Output both:

   * event order in UTC
   * event order in *original timezone labels*

### **Challenges**

* Handling DST transition in New York correctly
* No assumptions about fixed offsets
* Using `ZoneId` → never offset arithmetic
* Reconciling partial-second precision

---

# **Problem 2 — Multi-Leg International Flight with DST Crossing**

A traveler flies:

1. **Tokyo → Los Angeles**

   * Departure: `2025-11-02 10:00 JST`
   * Flight Duration: `9h 50m`
   * LA enters DST end (fallback) on **Nov 2 at 2 AM**, repeating 1 AM hour.

2. **Los Angeles → Lima**

   * Layover: `2h 30m`
   * Flight Duration: `8h 20m`
   * Lima has **no DST**.

### **Task**

Using Java Date-Time API:

* Compute final arrival time in Lima **accurately**, respecting the DST fallback in LA.
* Show each segment's arrival in local times.
* Show all times in UTC and highlight ambiguous timestamps.
* Identify if any event falls in repeated hour.

### **Challenges**

* DST fallback → LA has two 1 AM timestamps
* Avoiding “wrong offset” mistakes
* Layover must use arrival time’s timezone
* Must use `withZoneSameInstant()`, not `of()`

---

# **Problem 3 — Financial Trading Window with Multiple Exchanges**

You maintain an algorithmic trading platform using Java.

Trading windows:

| Exchange | Timezone         | Start | End   |
| -------- | ---------------- | ----- | ----- |
| NYSE     | America/New_York | 09:30 | 16:00 |
| LSE      | Europe/London    | 08:00 | 16:30 |
| TYO      | Asia/Tokyo       | 09:00 | 15:00 |

Given a UTC timestamp:

```
2025-03-28T14:25:00Z
```

### **Task**

Write Java code to determine:

1. Which exchanges are open at this moment?

2. Compute overlap windows between exchanges for that **specific date**, including:

   * DST in New York (already active)
   * DST in London (active from March last Sunday)
   * Tokyo (no DST)

3. Handle that some exchanges start/end on minute boundaries (not whole hours).

### **Challenges**

* Must use correct IANA rules (Europe/London DST starts last Sunday of March)
* Perform interval comparison using `LocalTime` + `ZonedDateTime`
* Cannot assume fixed UTC differences
* Need accurate overlap calculations

---

# **Problem 4 — Subscription System with Proration Across Mixed Calendars**

You design a subscription billing engine.

* Billing cycle starts:
  `2025-01-31` (customer timezone: `America/Sao_Paulo`)
* Customer upgrades plan on:
  `2025-02-28T18:40-03:00`
* Next billing cycle for Sao Paulo shifts between DST and non-DST across the months.
* The system needs to compute **prorated charges** based on:

  * Actual number of days in the cycle
  * DST transitions
  * Month lengths (Feb vs Jan vs Mar)
  * Leap year rules

### **Task**

Using Java:

1. Compute the billing cycle boundaries for January → February → March
2. Compute the prorated portion from Feb 28 → Feb cycle end
3. Use `Period`, not Duration (calendar-based)
4. Handle Brazil’s DST rules correctly (note: abolished after 2019 → must use historical timezone rules)

### **Challenges**

* Real-world Brazil timezones require using historical zone data
* Month-end alignment when original date overflows
* Must use `YearMonth`, `Period`, and `ZonedDateTime`

---

# **Problem 5 — Scheduling a Global Release with 50+ Regions**

Your product launches globally on:

```
2025-06-01T00:00:00Z  (firm UTC deadline)
```

You must generate the **local release timestamp** for each of 50+ regions including:

* Europe/Paris
* Europe/London
* America/New_York
* America/Los_Angeles
* Asia/Kolkata
* Australia/Sydney
* Pacific/Honolulu
* Africa/Cairo
* America/Santiago
* Europe/Moscow
* UTC± offsets
* Regions with abolished DST

### **Task**

Write Java code to:

1. Convert the single UTC timestamp into **all regional local times**
2. Group regions with *identical local timestamps* despite different zones
3. Flag regions where DST transition occurs **within ±2 hours** of release
4. Generate a sorted list by local datetime
5. Produce a release table like:

```
Region                   Local Time                 Offset
-----------------------------------------------------------
Asia/Kolkata             2025-06-01 05:30           +05:30
Europe/Paris             2025-06-01 02:00           +02:00
America/New_York         2025-05-31 20:00           -04:00
...
```

### **Challenges**

* Using `ZoneId.getAvailableZoneIds()`
* Handling zones with same offsets but different DST rules
* Sorting by local time but grouping by offset
* Maintaining correctness during summer DST
* Avoiding accidental use of `LocalDateTime` without zone context

### **DATASET 1 — Distributed System Logs (CSV)**

**Filename:** `distributed_logs.csv`

```
service,region,timestamp,description
A,us-east-1,2025-03-10T01:05:12-04:00[America/New_York],User login
B,eu-central-1,2025-03-10T08:35:10+01:00[Europe/Berlin],JWT validation
C,ap-south-1,2025-03-10T13:20:55+05:30[Asia/Kolkata],Order created
A,us-east-1,2025-03-10T01:45:00-04:00[America/New_York],Token refresh
B,eu-central-1,2025-03-10T08:10:42+01:00[Europe/Berlin],Cart updated
C,ap-south-1,2025-03-10T13:10:33+05:30[Asia/Kolkata],Payment started
A,us-east-1,2025-03-09T23:58:59-05:00[America/New_York],Pre-DST log
B,eu-central-1,2025-03-10T09:01:01+01:00[Europe/Berlin],Inventory check
```

---

### **DATASET 2 — International Multi-Leg Flight (CSV)**

**Filename:** `flight_itinerary.csv`

```
segment,location,local_timestamp,zone,event
1,Tokyo,2025-11-02T10:00+09:00[Asia/Tokyo],Asia/Tokyo,Departure
1,LAX_Fallback_Log,2025-11-02T00:45-07:00[America/Los_Angeles],America/Los_Angeles,LA Pre-DST Log
1,LAX_Fallback_Log,2025-11-02T01:15-08:00[America/Los_Angeles],America/Los_Angeles,LA Post-DST Log
1,Tokyo-LA Duration,09:50,,Flight Duration
2,LA Layover,02:30,,Layover Duration
2,LA-Lima Duration,08:20,,Flight Duration
3,Lima,,America/Lima,Final Arrival (To Be Computed)
```

Notes:

* You compute the actual arrival timestamps using Java.
* Duration rows intentionally leave timestamp blank.

---

### **DATASET 3 — Financial Trading Windows (CSV)**

**Filename:** `trading_windows.csv`

```
exchange,zone,open_time,close_time
NYSE,America/New_York,09:30,16:00
LSE,Europe/London,08:00,16:30
TYO,Asia/Tokyo,09:00,15:00
```

#### Supplemental events

**Filename:** `trading_events.csv`

```
event,local_timestamp,zone
Fed_Announcement,2025-03-28T10:00-04:00[America/New_York],America/New_York
BOE_Press_Release,2025-03-28T11:30+00:00[Europe/London],Europe/London
Tokyo_Settlement,2025-03-28T14:50+09:00[Asia/Tokyo],Asia/Tokyo
```

#### Global timestamp for the problem

**Filename:** `query_timestamp.csv`

```
timestamp
2025-03-28T14:25:00Z
```

---

### **DATASET 4 — Subscription Billing (CSV)**

**Filename:** `subscription_billing.csv`

```
field,value
billing_anchor_date,2025-01-31
timezone,America/Sao_Paulo
upgrade_timestamp,2025-02-28T18:40-03:00[America/Sao_Paulo]
old_plan_price,40
new_plan_price,75
historical_dst_sample,2018-11-04T01:30-02:00[America/Sao_Paulo]
```

#### Month cycle breakdown

**Filename:** `billing_cycles.csv`

```
cycle_name,start_date,end_date
January_2025,2025-01-31,2025-02-01
February_2025,2025-02-01,2025-03-01
March_2025,2025-03-01,2025-04-01
```

---

### **DATASET 5 — Global Release Timestamps (CSV)**

**Filename:** `release_regions.csv`

```
region
Asia/Kolkata
Europe/Paris
Europe/London
America/New_York
America/Los_Angeles
America/Chicago
America/Toronto
America/Santiago
America/Sao_Paulo
Australia/Sydney
Australia/Perth
Africa/Cairo
Africa/Nairobi
Europe/Moscow
Asia/Dubai
Asia/Hong_Kong
Asia/Tokyo
Pacific/Honolulu
Pacific/Auckland
Europe/Istanbul
```

#### Release Metadata

**Filename:** `release_metadata.csv`

```
field,value
release_id,REL-2025-06-WORLD
release_sha,bdfe09ca2d944c66b33f2aa7e4d395aa
release_utc_timestamp,2025-06-01T00:00:00Z
monitoring_window_hours,2
```

---