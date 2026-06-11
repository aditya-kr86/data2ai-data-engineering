# Cell 1 (Markdown)

```markdown id="sp110601"
# 11.6 Broadcast Joins

## Learning Objectives

By the end of this lesson you will be able to:

- Understand what a Broadcast Join is
- Learn when to use Broadcast Joins
- Understand how Broadcast Joins improve performance
- Learn the benefits of Broadcast Joins
- Understand Broadcast Join limitations
- Identify Broadcast Joins in Execution Plans

Broadcast Join is one of the most practical and commonly used Spark optimization techniques.
```

---

# Cell 2 (Markdown)

```markdown id="sp110602"
# Why Learn Broadcast Joins?

In the previous lesson, we learned that joins are often expensive.

Many joins require:

- Data movement
- Shuffle operations
- Network communication

These operations increase execution time.

Broadcast Join helps reduce these costs by avoiding unnecessary data movement.
```

---

# Cell 3 (Markdown)

```markdown id="sp110603"
# Real-World Example

Suppose an e-commerce company has:

Orders Table
- 500 Million Rows

Customers Table
- 50,000 Rows

To join these datasets,

should Spark move 500 million rows?

Or should Spark move only 50,000 rows?

Broadcast Join solves this problem efficiently.
```

---

# Cell 4 (Markdown)

```markdown id="sp110604"
# What is a Broadcast Join?

A Broadcast Join is a join strategy where Spark:

1. Identifies the smaller dataset
2. Copies that dataset to all executors
3. Performs joins locally

Since the larger dataset remains in place,

large-scale shuffle operations are avoided.
```

---

# Cell 5 (Markdown)

```html id="sp110605"
<h3>Broadcast Join Architecture</h3>

<img src="../assets/Module_11/11_06_01.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark Broadcast Join architecture. Small dimension table copied to all executors while large fact table remains distributed. Local joins performed without shuffle. Clean educational architecture diagram, white background.</p>
```

---

# Cell 6 (Markdown)

```markdown id="sp110606"
# Traditional Join vs Broadcast Join

Traditional Join:

- Both datasets participate in shuffle
- Large network traffic
- More execution time

Broadcast Join:

- Small table copied once
- Minimal shuffle
- Faster execution

This difference often leads to significant performance improvements.
```

---

# Cell 7 (Markdown)

```html id="sp110607"
<h3>Traditional Join vs Broadcast Join</h3>

<img src="../assets/Module_11/11_06_02.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Side-by-side comparison. Traditional Spark join showing heavy shuffle of both datasets. Broadcast Join showing only small table copied to executors. Educational performance comparison infographic.</p>
```

---

# Cell 8 (Markdown)

```markdown id="sp110608"
# Example Scenario

Large Table:

Orders
- 100 Million Rows

Small Table:

Customers
- 10,000 Rows

Joining both tables using shuffle would be expensive.

Broadcasting 10,000 rows is much cheaper.
```

---

# Cell 9 (Markdown)

```markdown id="sp110609"
# How Broadcast Join Works

Step 1:
Spark identifies a small table.

Step 2:
The table is serialized.

Step 3:
The serialized copy is sent to every executor.

Step 4:
Each executor performs local matching.

No large-scale redistribution of the big table is required.
```

---

# Cell 10 (Markdown)

```html id="sp110610"
<h3>Broadcast Join Workflow</h3>

<img src="../assets/Module_11/11_06_03.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Step-by-step Spark Broadcast Join workflow showing small table broadcast from driver to executors followed by local joins. Beginner-friendly architecture diagram.</p>
```

---

# Cell 11 (Markdown)

```markdown id="sp110611"
# Spark's Automatic Broadcast Join

Spark can automatically choose Broadcast Join.

If a dataset is sufficiently small,

Spark may broadcast it without any user intervention.

This decision is made by the optimizer.
```

---

# Cell 12 (Code)

```python
# Example join

result = orders.join(
    customers,
    "customer_id"
)
```

---

# Cell 13 (Markdown)

```markdown id="sp110613"
# Auto Broadcast Threshold

Spark uses a configuration value called:

spark.sql.autoBroadcastJoinThreshold

This setting determines the maximum table size eligible for automatic broadcasting.

If the table is below the threshold,

Spark may choose Broadcast Join automatically.
```

---

# Cell 14 (Markdown)

```markdown id="sp110614"
# Checking the Threshold

The threshold can be viewed using:

spark.conf.get(
    "spark.sql.autoBroadcastJoinThreshold"
)

The exact value may vary depending on Spark version and configuration.
```

---

# Cell 15 (Code)

```python
spark.conf.get(
    "spark.sql.autoBroadcastJoinThreshold"
)
```

---

# Cell 16 (Markdown)

```markdown id="sp110616"
# Manually Forcing a Broadcast Join

Sometimes Spark does not automatically choose Broadcast Join.

In such situations, engineers can provide a hint.

Spark provides the broadcast() function.
```

---

# Cell 17 (Code)

```python
from pyspark.sql.functions import broadcast

result = orders.join(
    broadcast(customers),
    "customer_id"
)
```

---

# Cell 18 (Markdown)

```markdown id="sp110618"
# Why Use broadcast()?

The broadcast() hint tells Spark:

"This dataset is small enough to copy to all executors."

Spark will attempt to use a Broadcast Hash Join.

This can improve performance significantly.
```

---

# Cell 19 (Markdown)

```html id="sp110619"
<h3>Using broadcast() Hint</h3>

<img src="../assets/Module_11/11_06_04.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark broadcast() hint illustration. Developer applying broadcast() to a small table causing Spark optimizer to select Broadcast Hash Join. Educational coding infographic.</p>
```

---

# Cell 20 (Markdown)

```markdown id="sp110620"
# Benefits of Broadcast Join

Major benefits include:

- Reduced Shuffle
- Lower Network Traffic
- Faster Execution
- Lower Disk Usage
- Better Resource Utilization

These benefits make Broadcast Join one of the most valuable Spark optimizations.
```

---

# Cell 21 (Markdown)

```markdown id="sp110621"
# Benefit 1: Reduced Shuffle

Traditional joins often require data redistribution.

Broadcast Join minimizes this movement.

Less shuffle means:

- Faster stages
- Reduced network overhead
- Lower execution time
```

---

# Cell 22 (Markdown)

```html id="sp110622"
<h3>Reduced Shuffle</h3>

<img src="../assets/Module_11/11_06_05.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Comparison showing heavy shuffle in traditional join versus minimal shuffle in Broadcast Join. Spark optimization educational diagram.</p>
```

---

# Cell 23 (Markdown)

```markdown id="sp110623"
# Benefit 2: Faster Execution

Because less data moves across the network,

Spark spends less time:

- Shuffling
- Sorting
- Waiting for transfers

As a result, joins complete faster.
```

---

# Cell 24 (Markdown)

```markdown id="sp110624"
# Benefit 3: Better Scalability

Broadcast Join performs particularly well for:

- Fact tables
- Dimension tables
- Lookup datasets

These patterns are common in data warehouses and analytics systems.
```

---

# Cell 25 (Markdown)

```html id="sp110625"
<h3>Fact and Dimension Join</h3>

<img src="../assets/Module_11/11_06_06.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Star schema example showing large fact table joined with small dimension table using Broadcast Join. Data warehouse educational architecture diagram.</p>
```

---

# Cell 26 (Markdown)

```markdown id="sp110626"
# When Should You Use Broadcast Join?

Broadcast Join is usually appropriate when:

- One table is very small
- One table is significantly larger
- The small table fits comfortably in memory

This is one of the most common use cases in Spark.
```

---

# Cell 27 (Markdown)

```markdown id="sp110627"
# Typical Broadcast Join Candidates

Examples include:

- Customer Master Data
- Product Catalogs
- Country Codes
- Currency Tables
- Lookup Tables

These datasets are usually small and rarely change.
```

---

# Cell 28 (Markdown)

```html id="sp110628"
<h3>Good Broadcast Join Candidates</h3>

<img src="../assets/Module_11/11_06_07.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Examples of small lookup tables including customer dimension, product dimension, country codes and currency mappings suitable for Broadcast Join. Educational infographic.</p>
```

---

# Cell 29 (Markdown)

```markdown id="sp110629"
# Limitations of Broadcast Join

Broadcast Join is not always the correct choice.

Problems arise when:

- The table is too large
- Executor memory is limited
- Multiple broadcasts occur simultaneously

These situations can create memory pressure.
```

---

# Cell 30 (Markdown)

```markdown id="sp110630"
# Large Broadcast Example

Suppose a table contains:

50 Million Rows

Broadcasting such a dataset to every executor would be expensive.

Memory usage would increase significantly.

Performance could actually become worse.
```

---

# Cell 31 (Markdown)

```html id="sp110631"
<h3>Broadcast Join Memory Problem</h3>

<img src="../assets/Module_11/11_06_08.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark executors receiving an excessively large broadcast table causing memory pressure and potential out-of-memory situations. Educational warning infographic.</p>
```

---

# Cell 32 (Markdown)

```markdown id="sp110632"
# Identifying Broadcast Joins

Execution Plans reveal whether Broadcast Join was selected.

Look for:

BroadcastHashJoin

in the Physical Plan.

This indicates Spark is using a Broadcast Join strategy.
```

---

# Cell 33 (Code)

```python
result.explain()
```

---

# Cell 34 (Markdown)

```markdown id="sp110634"
# Example Physical Plan

A plan might contain:

BroadcastHashJoin
    ↓
Scan Orders
    ↓
BroadcastExchange

These operators confirm that Spark performed broadcasting.
```

---

# Cell 35 (Markdown)

```html id="sp110635"
<h3>BroadcastHashJoin Operator</h3>

<img src="../assets/Module_11/11_06_09.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark Physical Plan highlighting BroadcastHashJoin and BroadcastExchange operators. Educational execution plan visualization showing join optimization.</p>
```

---

# Cell 36 (Markdown)

```markdown id="sp110636"
# Real-World Example

Orders Table:
200 Million Rows

Products Table:
20,000 Rows

Without Broadcast Join:

Execution Time = 15 Minutes

With Broadcast Join:

Execution Time = 4 Minutes

Same hardware.

Same data.

Different join strategy.
```

---

# Cell 37 (Markdown)

```markdown id="sp110637"
# Key Takeaways

- Broadcast Join copies a small dataset to all executors.
- It minimizes shuffle operations.
- It reduces network traffic and execution time.
- It works best when one table is significantly smaller.
- Large tables should not be broadcast.
- BroadcastHashJoin in the Physical Plan indicates broadcast optimization.
```

---

# Cell 38 (Markdown)

```markdown id="sp110638"
# Next Lesson

## 11.7 Sort Merge Joins

In the next lesson we will explore:

- How Sort Merge Join works
- Why Spark often selects it
- Advantages and disadvantages
- Performance implications
- Reading SortMergeJoin operators in execution plans

Sort Merge Join is the most common join strategy used for large datasets.
```

---

## Lesson Statistics

* **Topic:** Broadcast Joins
* **Cells:** 38
* **Images:** 9
* **Difficulty:** Beginner-Friendly
* **Estimated Duration:** 10–12 Minutes
* **Foundation For:** Sort Merge Join, AQE, Join Optimization, Query Tuning, Production Spark Performance Analysis
