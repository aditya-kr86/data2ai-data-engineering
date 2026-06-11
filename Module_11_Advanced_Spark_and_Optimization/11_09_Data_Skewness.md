# Cell 1 (Markdown)

```markdown id="sp110901"
# 11.9 Data Skewness

## Learning Objectives

By the end of this lesson you will be able to:

- Understand what Data Skewness is
- Learn why Data Skew causes slow Spark jobs
- Identify symptoms of skewed data
- Detect Data Skew using Spark UI
- Analyze real-world skew examples
- Understand the impact of skew on joins and aggregations

Data Skew is one of the most common causes of poor Spark performance.
```

---

# Cell 2 (Markdown)

```markdown id="sp110902"
# Why Learn Data Skew?

Many Spark jobs appear to have:

- Enough executors
- Enough memory
- Proper cluster configuration

Yet they still run slowly.

A common reason is:

Data Skew.

Data Skew causes some tasks to do significantly more work than others.

As a result, the entire job becomes slow.
```

---

# Cell 3 (Markdown)

```markdown id="sp110903"
# Real-World Scenario

Imagine a classroom with 100 students.

The teacher assigns homework grading.

Scenario 1:

10 teachers
Each teacher grades 10 papers.

Workload is balanced.

Scenario 2:

One teacher grades 90 papers.

Nine teachers grade 1 paper each.

Workload is highly unbalanced.

This imbalance is similar to Data Skew in Spark.
```

---

# Cell 4 (Markdown)

```markdown id="sp110904"
# What is Data Skew?

Data Skew occurs when data is not distributed evenly across partitions.

Some partitions contain:

- Very large amounts of data

while others contain:

- Very little data

This creates uneven workloads across executors.
```

---

# Cell 5 (Markdown)

```html id="sp110905"
<h3>Balanced vs Skewed Data Distribution</h3>

<img src="../assets/Module_11/11_09_01.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Side-by-side comparison of balanced Spark partitions versus skewed partitions. Balanced partitions contain similar amounts of data, while one skewed partition contains most records. Educational infographic.</p>
```

---

# Cell 6 (Markdown)

```markdown id="sp110906"
# Balanced Partition Example

Partition 1:
250 MB

Partition 2:
240 MB

Partition 3:
260 MB

Partition 4:
250 MB

All executors receive similar workloads.

Processing remains efficient.
```

---

# Cell 7 (Markdown)

```markdown id="sp110907"
# Skewed Partition Example

Partition 1:
50 MB

Partition 2:
40 MB

Partition 3:
60 MB

Partition 4:
5 GB

One partition contains most of the data.

One executor now performs significantly more work.
```

---

# Cell 8 (Markdown)

```html id="sp110908"
<h3>Skewed Partition Example</h3>

<img src="../assets/Module_11/11_09_02.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark cluster showing one partition containing several gigabytes of data while other partitions contain very little. Visualization of severe data skew.</p>
```

---

# Cell 9 (Markdown)

```markdown id="sp110909"
# Why Is Data Skew a Problem?

Spark processes partitions in parallel.

Ideally:

All tasks finish at approximately the same time.

With skew:

Most tasks finish quickly.

One task continues running for a long time.

The entire stage must wait for that task to complete.
```

---

# Cell 10 (Markdown)

```markdown id="sp110910"
# The Slowest Task Determines Completion Time

Suppose:

Task 1:
10 Seconds

Task 2:
12 Seconds

Task 3:
11 Seconds

Task 4:
5 Minutes

The stage cannot finish until Task 4 finishes.

This is called the "straggler problem."
```

---

# Cell 11 (Markdown)

```html id="sp110911"
<h3>Straggler Task Problem</h3>

<img src="../assets/Module_11/11_09_03.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark stage visualization showing multiple tasks finishing quickly while one task continues running much longer. Educational illustration of straggler tasks caused by skew.</p>
```

---

# Cell 12 (Markdown)

```markdown id="sp110912"
# Common Causes of Data Skew

Data Skew often occurs because:

- Certain keys appear very frequently
- Customer activity is uneven
- Product popularity is uneven
- Some categories dominate the dataset

Real-world data is rarely perfectly balanced.
```

---

# Cell 13 (Markdown)

```markdown id="sp110913"
# Example: E-Commerce Orders

Suppose we group orders by product.

Most products sell:

100–500 units

But one product sells:

5 Million units

This popular product creates a highly skewed partition.
```

---

# Cell 14 (Markdown)

```html id="sp110914"
<h3>Popular Product Skew</h3>

<img src="../assets/Module_11/11_09_04.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Product sales distribution chart showing one extremely popular product generating millions of records while most products have relatively few records. Data skew visualization.</p>
```

---

# Cell 15 (Markdown)

```markdown id="sp110915"
# Example: Banking Transactions

Suppose a bank processes transactions.

Most accounts:

10–50 transactions

One corporate account:

10 Million transactions

Grouping by account ID can produce severe skew.
```

---

# Cell 16 (Markdown)

```markdown id="sp110916"
# Example: Website Traffic

Website visits:

Most users:
1–20 visits

One bot:
50 Million visits

Aggregating by user ID may create an extremely large partition.
```

---

# Cell 17 (Markdown)

```html id="sp110917"
<h3>Real-World Skew Examples</h3>

<img src="../assets/Module_11/11_09_05.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Multiple examples of skewed datasets including e-commerce products, banking accounts and website traffic. Educational infographic demonstrating uneven data distribution.</p>
```

---

# Cell 18 (Markdown)

```markdown id="sp110918"
# Data Skew During Joins

Data Skew becomes especially dangerous during joins.

Suppose:

Customer ID 100 appears 1 million times.

Other customer IDs appear only 100 times.

During shuffle,

Customer ID 100 creates a huge partition.

Join performance suffers significantly.
```

---

# Cell 19 (Markdown)

```markdown id="sp110919"
# Data Skew During Aggregations

Data Skew also affects:

- groupBy()
- count()
- sum()
- avg()

Any operation that groups records by key can be impacted.
```

---

# Cell 20 (Markdown)

```html id="sp110920"
<h3>Skew During Join and Aggregation</h3>

<img src="../assets/Module_11/11_09_06.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark shuffle operation showing a heavily skewed key causing one partition to receive significantly more records than others during join and aggregation.</p>
```

---

# Cell 21 (Markdown)

```markdown id="sp110921"
# Detecting Data Skew

Data Skew can often be identified through:

- Spark UI
- Stage Metrics
- Task Duration
- Shuffle Statistics

Performance analysis usually starts here.
```

---

# Cell 22 (Markdown)

```markdown id="sp110922"
# Detecting Skew in Spark UI

Open:

Stages Tab

Look for:

- One task taking much longer
- Uneven task durations
- High shuffle read values
- High shuffle write values

These are common skew indicators.
```

---

# Cell 23 (Markdown)

```html id="sp110923"
<h3>Detecting Skew in Spark UI</h3>

<img src="../assets/Module_11/11_09_07.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark UI Stages Tab showing one task significantly longer than all others. Highlighted metrics indicating possible data skew. Educational dashboard visualization.</p>
```

---

# Cell 24 (Markdown)

```markdown id="sp110924"
# Another Warning Sign

Suppose:

99 tasks finish in 20 seconds.

1 task takes 15 minutes.

This usually indicates:

- Data Skew
- Resource imbalance
- Uneven partition distribution

Further investigation is required.
```

---

# Cell 25 (Markdown)

```markdown id="sp110925"
# Examining Key Distribution

A useful technique is checking key frequency.

Example:

customer_id

Count occurrences of each key.

Extremely frequent keys often indicate skew.
```

---

# Cell 26 (Code)

```python id="sp110926"
df.groupBy("customer_id") \
  .count() \
  .orderBy("count", ascending=False) \
  .show()
```

---

# Cell 27 (Markdown)

```markdown id="sp110927"
# Interpreting Results

If most customers appear:

100 times

but one customer appears:

10 million times

there is a strong possibility of Data Skew.
```

---

# Cell 28 (Markdown)

```html id="sp110928"
<h3>Key Frequency Analysis</h3>

<img src="../assets/Module_11/11_09_08.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Frequency distribution chart showing one dominant key with extremely high occurrence count compared to all other keys. Data skew detection infographic.</p>
```

---

# Cell 29 (Markdown)

```markdown id="sp110929"
# Business Impact

Data Skew can cause:

- Slow pipelines
- Delayed reporting
- Increased cloud costs
- Poor resource utilization

Identifying skew early is important for production systems.
```

---

# Cell 30 (Markdown)

```markdown id="sp110930"
# Real Production Example

Daily Sales Job:

Expected Runtime:
10 Minutes

Actual Runtime:
45 Minutes

Investigation:

- Cluster healthy
- Memory sufficient
- CPU utilization normal

Root Cause:

One highly skewed customer key.

This single key slowed the entire pipeline.
```

---

# Cell 31 (Markdown)

```html id="sp110931"
<h3>Production Impact of Data Skew</h3>

<img src="../assets/Module_11/11_09_09.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Production Spark pipeline slowed by a skewed partition. Timeline comparison showing expected runtime versus actual runtime due to data skew.</p>
```

---

# Cell 32 (Markdown)

```markdown id="sp110932"
# Key Takeaways

- Data Skew occurs when partitions are unevenly distributed.
- Some tasks receive significantly more work than others.
- Data Skew commonly affects joins and aggregations.
- Spark UI helps detect skew.
- Long-running tasks are often a warning sign.
- Key frequency analysis can reveal skewed values.

Data Skew is one of the most common Spark performance challenges.
```

---

# Cell 33 (Markdown)

```markdown id="sp110933"
# Next Lesson

## 11.10 Fixing Data Skew

In the next lesson we will learn:

- Salting
- Repartitioning
- Broadcast-based solutions
- Adaptive Query Execution (AQE) skew handling

Understanding skew is the first step.

Fixing skew is the next.
```

---

## Lesson Statistics

* **Topic:** Data Skewness
* **Cells:** 33
* **Images:** 9
* **Difficulty:** Beginner-Friendly
* **Estimated Duration:** 10–12 Minutes
* **Foundation For:** Salting, AQE, Repartitioning, Join Optimization, Production Spark Performance Tuning
