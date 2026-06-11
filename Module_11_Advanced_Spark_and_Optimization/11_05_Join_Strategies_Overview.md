# Cell 1 (Markdown)

```markdown id="sp110501"
# 11.5 Join Strategies Overview

## Learning Objectives

By the end of this lesson you will be able to:

- Understand why joins are expensive in Spark
- Learn different Spark join strategies
- Understand Broadcast Join
- Understand Sort Merge Join
- Understand Shuffle Hash Join
- Learn when Spark chooses each strategy
- Read join operators in execution plans

Join optimization is one of the highest-impact Spark performance skills.
```

---

# Cell 2 (Markdown)

```markdown id="sp110502"
# Why Are Joins Important?

In real-world Data Engineering projects, joins are everywhere.

Examples:

- Orders + Customers
- Transactions + Accounts
- Products + Categories
- Clickstream + User Profiles

Most business analytics pipelines depend heavily on joins.

Unfortunately, joins are also among the most expensive Spark operations.
```

---

# Cell 3 (Markdown)

```markdown id="sp110503"
# Why Can Joins Become Slow?

To perform a join, Spark often needs to:

- Move data between executors
- Shuffle records
- Sort data
- Compare matching keys

These operations consume:

- CPU
- Memory
- Disk
- Network bandwidth

The larger the datasets, the greater the cost.
```

---

# Cell 4 (Markdown)

```markdown id="sp110504"
# Spark Doesn't Use One Join Method

Many beginners assume:

"There is only one way to perform a join."

In reality, Spark can choose different strategies.

Common join strategies include:

1. Broadcast Join
2. Sort Merge Join
3. Shuffle Hash Join

Each strategy has different performance characteristics.
```

---

# Cell 5 (Markdown)

```html id="sp110505"
<h3>Join Strategy Selection</h3>

<img src="../assets/Module_11/11_05_01.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark join strategy decision diagram showing Broadcast Join, Sort Merge Join and Shuffle Hash Join. Spark optimizer selecting the best strategy based on dataset size. Educational infographic.</p>
```

---

# Cell 6 (Markdown)

```markdown id="sp110506"
# Example Scenario

Suppose we have:

Customers Table
- 10,000 rows

Orders Table
- 100 Million rows

Should Spark move both datasets?

Or only move the smaller dataset?

The answer depends on the selected join strategy.
```

---

# Cell 7 (Markdown)

```markdown id="sp110507"
# Broadcast Join Introduction

Broadcast Join is usually the fastest join strategy.

Spark sends the smaller dataset to every executor.

Each executor performs the join locally.

This avoids large-scale data movement.
```

---

# Cell 8 (Markdown)

```markdown id="sp110508"
# How Broadcast Join Works

Step 1:
Identify the small table.

Step 2:
Copy the small table to all executors.

Step 3:
Join locally with the large table.

Since the large dataset never moves,

Shuffle is minimized.
```

---

# Cell 9 (Markdown)

```html id="sp110509"
<h3>Broadcast Join Architecture</h3>

<img src="../assets/Module_11/11_05_02.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark Broadcast Join visualization. Small lookup table copied to all executors while large dataset remains distributed. Local joins occurring without shuffle. Educational architecture diagram.</p>
```

---

# Cell 10 (Markdown)

```markdown id="sp110510"
# Example

Large Dataset:
100 Million Orders

Small Dataset:
10,000 Customers

Broadcasting 10,000 rows is much cheaper than shuffling 100 million rows.

Therefore Broadcast Join is often the preferred strategy.
```

---

# Cell 11 (Markdown)

```markdown id="sp110511"
# Advantages of Broadcast Join

Benefits include:

- Minimal Shuffle
- Reduced Network Traffic
- Faster Execution
- Lower Resource Usage

For small lookup tables, Broadcast Join is often the best choice.
```

---

# Cell 12 (Markdown)

```markdown id="sp110512"
# Limitations of Broadcast Join

Broadcast Join is not always possible.

Problems occur when:

- Small table becomes large
- Executor memory is insufficient
- Broadcast size exceeds thresholds

Large datasets should not be broadcast.
```

---

# Cell 13 (Markdown)

```html id="sp110513"
<h3>When Broadcast Join Fails</h3>

<img src="../assets/Module_11/11_05_03.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark Broadcast Join limitation diagram showing a large table attempting to be broadcast, causing memory pressure on executors. Educational infographic.</p>
```

---

# Cell 14 (Markdown)

```markdown id="sp110514"
# Sort Merge Join Introduction

When datasets become large,

Spark commonly chooses Sort Merge Join.

This is one of the most frequently used join strategies in production systems.
```

---

# Cell 15 (Markdown)

```markdown id="sp110515"
# How Sort Merge Join Works

Step 1:
Shuffle data by join key.

Step 2:
Sort records inside partitions.

Step 3:
Merge matching records.

Because sorting and shuffling are required,

this strategy can be expensive.
```

---

# Cell 16 (Markdown)

```html id="sp110516"
<h3>Sort Merge Join Workflow</h3>

<img src="../assets/Module_11/11_05_04.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark Sort Merge Join process. Data shuffled by join key, sorted within partitions and then merged. Clear educational architecture diagram with arrows.</p>
```

---

# Cell 17 (Markdown)

```markdown id="sp110517"
# Why Sort Merge Join Is Common

Large datasets usually cannot be broadcast.

Spark therefore relies on:

- Partitioning
- Sorting
- Merging

Sort Merge Join scales well for large distributed datasets.
```

---

# Cell 18 (Markdown)

```markdown id="sp110518"
# Cost of Sort Merge Join

The main costs are:

- Shuffle
- Sorting
- Network Transfer
- Disk I/O

This makes Sort Merge Join slower than Broadcast Join in many situations.
```

---

# Cell 19 (Markdown)

```html id="sp110519"
<h3>Sort Merge Join Cost</h3>

<img src="../assets/Module_11/11_05_05.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark Sort Merge Join showing shuffle, sort and merge phases with associated network, CPU and disk costs. Performance-focused educational infographic.</p>
```

---

# Cell 20 (Markdown)

```markdown id="sp110520"
# Shuffle Hash Join Introduction

Another join strategy is Shuffle Hash Join.

This strategy combines:

- Shuffle
- Hash-based matching

instead of sorting records.
```

---

# Cell 21 (Markdown)

```markdown id="sp110521"
# How Shuffle Hash Join Works

Step 1:
Shuffle both datasets.

Step 2:
Build hash tables.

Step 3:
Match records using hash lookups.

Hash lookups can be faster than sorting in some situations.
```

---

# Cell 22 (Markdown)

```html id="sp110522"
<h3>Shuffle Hash Join Workflow</h3>

<img src="../assets/Module_11/11_05_06.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark Shuffle Hash Join architecture. Datasets shuffled into partitions followed by hash table creation and hash-based matching. Educational diagram.</p>
```

---

# Cell 23 (Markdown)

```markdown id="sp110523"
# When Spark Uses Shuffle Hash Join

Spark may choose Shuffle Hash Join when:

- Data is partitioned appropriately
- Join keys are suitable
- Hash strategy is estimated to be cheaper

The exact choice depends on Spark's optimizer.
```

---

# Cell 24 (Markdown)

```markdown id="sp110524"
# Limitations of Shuffle Hash Join

Potential issues include:

- Memory pressure
- Large hash tables
- Executor memory constraints

If hash tables become too large,

performance may degrade.
```

---

# Cell 25 (Markdown)

```html id="sp110525"
<h3>Shuffle Hash Join Memory Usage</h3>

<img src="../assets/Module_11/11_05_07.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark Shuffle Hash Join showing hash tables consuming executor memory. Illustration of memory-intensive join processing. Beginner-friendly educational infographic.</p>
```

---

# Cell 26 (Markdown)

```markdown id="sp110526"
# Comparing Join Strategies

| Strategy | Shuffle | Sorting | Broadcast | Typical Speed |
|-----------|----------|----------|-----------|---------------|
| Broadcast Join | Minimal | No | Yes | Fastest |
| Sort Merge Join | Yes | Yes | No | Moderate |
| Shuffle Hash Join | Yes | No | No | Moderate |
```

---

# Cell 27 (Markdown)

```html id="sp110527"
<h3>Join Strategy Comparison</h3>

<img src="../assets/Module_11/11_05_08.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Side-by-side comparison of Broadcast Join, Sort Merge Join and Shuffle Hash Join. Comparing shuffle, sorting, memory usage and performance. Professional educational infographic.</p>
```

---

# Cell 28 (Markdown)

```markdown id="sp110528"
# Join Operators in Execution Plans

Execution plans reveal selected join strategies.

Examples:

BroadcastHashJoin

SortMergeJoin

ShuffledHashJoin

These operator names appear in Physical Plans.
```

---

# Cell 29 (Code)

```python id="sp110529"
result.explain()
```

---

# Cell 30 (Markdown)

```markdown id="sp110530"
# Example Physical Plan

Suppose the plan contains:

BroadcastHashJoin

This indicates Spark selected a Broadcast Join.

Similarly:

SortMergeJoin

indicates a Sort Merge Join.

Execution plans provide valuable optimization insights.
```

---

# Cell 31 (Markdown)

```html id="sp110531"
<h3>Join Operators in Physical Plan</h3>

<img src="../assets/Module_11/11_05_09.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark Physical Plan highlighting BroadcastHashJoin, SortMergeJoin and ShuffledHashJoin operators. Educational execution plan visualization.</p>
```

---

# Cell 32 (Markdown)

```markdown id="sp110532"
# Real-World Example

Suppose:

Customers Table:
50,000 rows

Orders Table:
200 Million rows

Broadcast Join:

Execution Time = 3 Minutes

Sort Merge Join:

Execution Time = 12 Minutes

Same result.

Different strategy.

Huge performance difference.
```

---

# Cell 33 (Markdown)

```markdown id="sp110533"
# Key Takeaways

- Spark supports multiple join strategies.
- Broadcast Join is usually fastest for small lookup tables.
- Sort Merge Join is common for large datasets.
- Shuffle Hash Join uses hash-based matching.
- Join strategy selection directly affects performance.
- Execution plans reveal which join strategy Spark selected.
```

---

# Cell 34 (Markdown)

```markdown id="sp110534"
# Next Lesson

## 11.6 Broadcast Joins Deep Dive

In the next lesson we will explore:

- Auto Broadcast Threshold
- broadcast() Hint
- BroadcastHashJoin Operator
- Performance Benchmark
- Best Practices

Broadcast Join is one of the most practical Spark optimization techniques used in production.
```

---

## Lesson Statistics

* **Topic:** Join Strategies Overview
* **Cells:** 34
* **Images:** 9
* **Estimated Duration:** 10–12 Minutes
* **Difficulty:** Beginner-Friendly
* **Foundation For:** Broadcast Join Deep Dive, Sort Merge Join Optimization, AQE, Physical Plan Analysis, Spark Performance Tuning
