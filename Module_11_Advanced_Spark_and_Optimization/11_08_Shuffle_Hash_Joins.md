# Cell 1 (Markdown)

```markdown id="sp110801"
# 11.8 Shuffle Hash Joins

## Learning Objectives

By the end of this lesson you will be able to:

- Understand what a Shuffle Hash Join is
- Learn how Shuffle Hash Join works internally
- Understand the execution flow
- Learn how hash tables are used
- Compare Shuffle Hash Join with Sort Merge Join
- Understand performance trade-offs

Shuffle Hash Join is another join strategy Spark may use when joining large datasets.
```

---

# Cell 2 (Markdown)

```markdown id="sp110802"
# Recap: Previous Join Strategies

So far we have studied:

1. Broadcast Join
2. Sort Merge Join

Broadcast Join:

- Best for small tables

Sort Merge Join:

- Best for large distributed datasets

Now we will learn a third strategy:

Shuffle Hash Join
```

---

# Cell 3 (Markdown)

```markdown id="sp110803"
# Why Does Spark Need Another Join Strategy?

You might wonder:

"If Sort Merge Join already works for large datasets, why do we need Shuffle Hash Join?"

The answer is:

Sorting can be expensive.

If Spark can avoid sorting and use hashing instead,

execution may become faster in some situations.
```

---

# Cell 4 (Markdown)

```markdown id="sp110804"
# What is a Shuffle Hash Join?

Shuffle Hash Join is a join strategy that:

1. Shuffles data by join key
2. Builds hash tables
3. Uses hash lookups to find matches

Unlike Sort Merge Join,

it uses hashing instead of sorting.
```

---

# Cell 5 (Markdown)

```html id="sp110805"
<h3>Shuffle Hash Join Overview</h3>

<img src="../assets/Module_11/11_08_01.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark Shuffle Hash Join overview showing two datasets entering shuffle phase, hash table creation phase and matching phase. Educational architecture diagram with arrows and labeled stages.</p>
```

---

# Cell 6 (Markdown)

```markdown id="sp110806"
# Basic Idea Behind Hashing

A hash function converts a value into a hash key.

Example:

Customer ID = 101

Hash Function
    ↓

Hash Bucket = 5

Records with the same join key generate the same hash location.

This makes matching very efficient.
```

---

# Cell 7 (Markdown)

```markdown id="sp110807"
# Real-World Analogy

Imagine a library.

Instead of searching every shelf,

books are organized by category.

You directly go to the required section.

Hash tables work similarly.

They provide fast access to matching records.
```

---

# Cell 8 (Markdown)

```html id="sp110808"
<h3>Hash Table Concept</h3>

<img src="../assets/Module_11/11_08_02.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Educational hash table visualization showing customer IDs mapped into hash buckets. Beginner-friendly infographic explaining hashing concepts.</p>
```

---

# Cell 9 (Markdown)

```markdown id="sp110809"
# High-Level Execution Flow

Shuffle Hash Join consists of three phases:

Phase 1:
Shuffle

Phase 2:
Build Hash Table

Phase 3:
Hash-Based Matching

Let's examine each phase individually.
```

---

# Cell 10 (Markdown)

```markdown id="sp110810"
# Phase 1: Shuffle

Like Sort Merge Join,

Shuffle Hash Join begins with data redistribution.

Records with the same join key are moved into the same partition.

This ensures matching records eventually reach the same location.
```

---

# Cell 11 (Markdown)

```html id="sp110811"
<h3>Shuffle Phase</h3>

<img src="../assets/Module_11/11_08_03.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark shuffle process for Shuffle Hash Join. Matching keys moving across executors into common partitions. Educational cluster visualization.</p>
```

---

# Cell 12 (Markdown)

```markdown id="sp110812"
# Phase 2: Build Hash Table

After shuffling,

Spark selects one side of the join.

A hash table is created using the join key.

Example:

CustomerID
    ↓
Hash Bucket

Spark stores records in memory for fast lookup.
```

---

# Cell 13 (Markdown)

```markdown id="sp110813"
# Why Build a Hash Table?

Without a hash table,

Spark may repeatedly search records.

With a hash table,

Spark can directly jump to matching entries.

This reduces lookup time significantly.
```

---

# Cell 14 (Markdown)

```html id="sp110814"
<h3>Hash Table Creation</h3>

<img src="../assets/Module_11/11_08_04.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark executor building an in-memory hash table from shuffled records. Hash buckets storing records by join key. Educational architecture diagram.</p>
```

---

# Cell 15 (Markdown)

```markdown id="sp110815"
# Phase 3: Hash-Based Matching

The second dataset is scanned.

For every join key:

Spark performs a hash lookup.

Matching records are retrieved immediately.

The join result is generated.
```

---

# Cell 16 (Markdown)

```markdown id="sp110816"
# Example

Hash Table:

101 → Customer A

102 → Customer B

103 → Customer C

Incoming Order:

CustomerID = 102

Spark immediately finds:

Customer B

using hash lookup.
```

---

# Cell 17 (Markdown)

```html id="sp110817"
<h3>Hash-Based Matching</h3>

<img src="../assets/Module_11/11_08_05.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Hash lookup process showing incoming records matching entries in an existing hash table. Educational infographic demonstrating efficient join matching.</p>
```

---

# Cell 18 (Markdown)

```markdown id="sp110818"
# Complete Execution Flow

Dataset A
    ↓
Shuffle
    ↓
Build Hash Table
    ↓

Dataset B
    ↓
Shuffle
    ↓
Hash Lookup
    ↓
Join Result

This replaces the sorting phase used in Sort Merge Join.
```

---

# Cell 19 (Markdown)

```html id="sp110819"
<h3>Complete Shuffle Hash Join Flow</h3>

<img src="../assets/Module_11/11_08_06.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> End-to-end Shuffle Hash Join execution flow showing shuffle, hash table creation and hash lookup stages producing final joined output.</p>
```

---

# Cell 20 (Markdown)

```markdown id="sp110820"
# Comparing with Sort Merge Join

Sort Merge Join:

Shuffle
→ Sort
→ Merge

Shuffle Hash Join:

Shuffle
→ Build Hash Table
→ Match

The major difference is:

Sorting versus Hashing.
```

---

# Cell 21 (Markdown)

```markdown id="sp110821"
# Potential Advantage

Sorting can be expensive.

Hash lookups are often faster.

In some workloads,

Shuffle Hash Join may outperform Sort Merge Join.
```

---

# Cell 22 (Markdown)

```markdown id="sp110822"
# Memory Requirements

Hash tables must be stored in memory.

This creates an important requirement:

Enough executor memory must be available.

Large hash tables can create memory pressure.
```

---

# Cell 23 (Markdown)

```html id="sp110823"
<h3>Memory Usage in Shuffle Hash Join</h3>

<img src="../assets/Module_11/11_08_07.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark executors storing large in-memory hash tables. Memory consumption highlighted as a critical resource. Educational performance diagram.</p>
```

---

# Cell 24 (Markdown)

```markdown id="sp110824"
# Trade-Off 1: Less Sorting

Advantage:

No large sorting phase.

Benefits:

- Reduced CPU work
- Reduced sort overhead
- Potentially faster execution

This is the primary strength of Shuffle Hash Join.
```

---

# Cell 25 (Markdown)

```markdown id="sp110825"
# Trade-Off 2: Higher Memory Usage

Disadvantage:

Hash tables consume memory.

Problems may include:

- Memory pressure
- Executor instability
- Out-of-memory errors

Memory becomes a critical resource.
```

---

# Cell 26 (Markdown)

```markdown id="sp110826"
# Trade-Off 3: Still Requires Shuffle

A common misconception:

"Shuffle Hash Join eliminates Shuffle."

This is false.

Shuffle Hash Join still requires:

- Data redistribution
- Network transfer
- Partition alignment

Shuffle cost still exists.
```

---

# Cell 27 (Markdown)

```html id="sp110827"
<h3>Shuffle Still Exists</h3>

<img src="../assets/Module_11/11_08_08.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Comparison showing Shuffle Hash Join still requiring network shuffle before hash table creation. Educational infographic correcting common misconceptions.</p>
```

---

# Cell 28 (Markdown)

```markdown id="sp110828"
# When Spark Uses Shuffle Hash Join

Spark may choose Shuffle Hash Join when:

- Data sizes are suitable
- Memory is available
- Hashing appears cheaper than sorting

The optimizer evaluates multiple factors before selecting a strategy.
```

---

# Cell 29 (Markdown)

```markdown id="sp110829"
# Shuffle Hash Join in Execution Plans

Physical Plans reveal the join strategy.

Look for:

ShuffledHashJoin

This operator indicates Spark selected a Shuffle Hash Join.
```

---

# Cell 30 (Code)

```python id="sp110830"
result.explain()
```

---

# Cell 31 (Markdown)

```markdown id="sp110831"
# Example Physical Plan

A plan may contain:

ShuffledHashJoin
      ↓
Exchange
      ↓
Exchange

This indicates:

- Data redistribution occurred
- Hash-based matching is being used
```

---

# Cell 32 (Markdown)

```html id="sp110832"
<h3>ShuffledHashJoin Operator</h3>

<img src="../assets/Module_11/11_08_09.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark Physical Plan highlighting ShuffledHashJoin and Exchange operators. Educational execution plan visualization showing hash-based join processing.</p>
```

---

# Cell 33 (Markdown)

```markdown id="sp110833"
# Comparing Join Strategies

| Feature | Broadcast Join | Sort Merge Join | Shuffle Hash Join |
|----------|----------|----------|----------|
| Shuffle | Minimal | Yes | Yes |
| Sorting | No | Yes | No |
| Hash Table | No | No | Yes |
| Memory Usage | Moderate | Moderate | High |
| Large Dataset Support | Limited | Excellent | Good |

Each strategy has different strengths and weaknesses.
```

---

# Cell 34 (Markdown)

```markdown id="sp110834"
# Real-World Perspective

Most Spark workloads use:

- Broadcast Join
- Sort Merge Join

more frequently.

Shuffle Hash Join appears less often,

but understanding it helps explain Spark's optimizer decisions and execution plans.
```

---

# Cell 35 (Markdown)

```markdown id="sp110835"
# Key Takeaways

- Shuffle Hash Join uses hashing instead of sorting.
- Execution consists of:
  - Shuffle
  - Hash Table Creation
  - Hash-Based Matching
- It can outperform Sort Merge Join in some situations.
- It requires sufficient memory.
- Shuffle cost still exists.
- Physical Plans reveal ShuffledHashJoin operators.
```

---

# Cell 36 (Markdown)

```markdown id="sp110836"
# Next Lesson

## 11.9 Data Skewness

In the next lesson we will learn:

- What is Data Skew?
- Why Data Skew causes slow jobs
- How to detect skew
- Real-world skew examples
- Performance impact on joins and aggregations

Data Skew is one of the most common Spark performance problems.
```

---

## Lesson Statistics

* **Topic:** Shuffle Hash Joins
* **Cells:** 36
* **Images:** 9
* **Difficulty:** Beginner-Friendly
* **Estimated Duration:** 10–12 Minutes
* **Foundation For:** Data Skew, AQE, Join Optimization, Physical Plan Analysis, Spark Performance Tuning
