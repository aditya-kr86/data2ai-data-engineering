For a beginner-friendly academy, **11.1 should not jump into AQE or tuning immediately**. The goal is to make students understand:

> "Spark is fast, but Spark jobs can still become slow. Before fixing performance, we must understand where time is actually being spent."

Below is a **cell-by-cell Jupyter/Colab notebook structure**.

---

# Cell 1 (Markdown)

```markdown
# 11.1 Spark Performance Fundamentals

## Learning Objectives

By the end of this lesson you will understand:

- Why Spark jobs become slow
- Common Spark execution bottlenecks
- CPU bottlenecks
- Memory bottlenecks
- Disk bottlenecks
- Network bottlenecks
- Why optimization matters before scaling infrastructure

This lesson builds the foundation for all advanced Spark optimization topics.
```

---

# Cell 2 (Markdown)

```markdown
# Why Performance Matters

Apache Spark is designed to process large datasets across multiple machines.

However:

- Adding more machines does not automatically make jobs fast.
- Poorly written Spark code can waste resources.
- Slow jobs increase cloud costs.
- Slow jobs delay business reports and analytics.

A Data Engineer's job is not only to make pipelines work.

A Data Engineer must also make them efficient.
```

---

# Cell 3 (Markdown)

```markdown
# Real World Scenario

Imagine an e-commerce company.

Every day:

- Millions of orders arrive
- Customer activity is recorded
- Product views are tracked

Management expects daily analytics reports.

If a Spark job takes:

- 5 minutes → Excellent
- 30 minutes → Acceptable
- 5 hours → Problem

Understanding performance helps us identify why jobs take longer than expected.
```

---

# Cell 4 (Markdown)

```markdown
# What Makes a Spark Job Slow?

Many beginners assume:

"Big data is large, therefore jobs are slow."

This is only partially true.

Spark jobs usually become slow because of:

1. CPU Bottlenecks
2. Memory Bottlenecks
3. Disk Bottlenecks
4. Network Bottlenecks
5. Poor Spark Code
6. Inefficient Data Layout

Let's study each one.
```

---

# Cell 5 (Markdown)

```markdown
# Spark Execution Overview

Before understanding bottlenecks, let's review what Spark does.

A Spark job typically follows:

Data Source
    ↓
Read Data
    ↓
Transform Data
    ↓
Shuffle Data
    ↓
Aggregate / Join
    ↓
Write Results

Performance issues can occur at any stage.
```

---

# Cell 6 (Markdown)

```html
<h3>Execution Flow</h3>

<img src="../assets/Module_11/11_01_01.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Beginner-friendly Apache Spark execution pipeline diagram. Data Source → Read Data → Transformations → Shuffle → Aggregation/Join → Output Storage. Arrows showing data movement. Clean educational infographic, white background, modern training style.</p>
```

---

# Cell 7 (Markdown)

```markdown
# CPU Bottleneck

CPU performs actual computations.

Examples:

- Filtering records
- Calculating metrics
- Aggregations
- Sorting data

When CPUs become overloaded, tasks take longer to finish.

Symptoms:

- High CPU utilization
- Long task execution times
- Executors constantly busy
```

---

# Cell 8 (Markdown)

```markdown
# Example of CPU Heavy Processing

Suppose we process:

100 million customer records

And perform:

- Complex calculations
- Multiple aggregations
- Large sorting operations

Even if memory is sufficient,
CPU may become the limiting factor.

More computation means more processing time.
```

---

# Cell 9 (Markdown)

```html
<h3>CPU Bottleneck</h3>

<img src="../assets/Module_11/11_01_02.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark cluster with executors showing CPU usage reaching 100 percent. Tasks waiting in queue because processors are busy. Educational performance bottleneck diagram. White background.</p>
```

---

# Cell 10 (Markdown)

```markdown
# Memory Bottleneck

Spark prefers processing data in memory.

Memory access is much faster than disk access.

Problems occur when:

- Dataset is too large
- Executors have insufficient RAM
- Too much data is cached

Spark may start spilling data to disk.
```

---

# Cell 11 (Markdown)

```markdown
# What is Disk Spilling?

When memory fills up:

Spark temporarily writes data to disk.

This process is called:

Disk Spill

Disk operations are significantly slower than memory operations.

As a result:

Job execution slows down considerably.
```

---

# Cell 12 (Markdown)

```html
<h3>Memory Spill Example</h3>

<img src="../assets/Module_11/11_01_03.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark executor memory full. Data overflowing from RAM into disk storage labeled Spill to Disk. Performance slowdown illustration. Beginner-friendly infographic.</p>
```

---

# Cell 13 (Markdown)

```markdown
# Disk Bottleneck

Spark constantly interacts with storage.

Examples:

- Reading CSV files
- Reading Parquet files
- Writing reports
- Saving Delta tables

Slow storage systems can significantly increase runtime.
```

---

# Cell 14 (Markdown)

```markdown
# Example

Reading:

1 TB CSV file

from slow storage.

Even before processing begins,
Spark spends time waiting for data to arrive.

This is called an I/O bottleneck.

I/O means:

Input / Output Operations.
```

---

# Cell 15 (Markdown)

```html
<h3>Disk I/O Bottleneck</h3>

<img src="../assets/Module_11/11_01_04.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark executors waiting while data slowly arrives from storage system. Disk icon connected to executors with slow arrows. I/O bottleneck visualization. White background.</p>
```

---

# Cell 16 (Markdown)

```markdown
# Network Bottleneck

Spark is a distributed system.

Executors often exchange data.

This happens during:

- Joins
- GroupBy operations
- Aggregations
- Shuffles

Large data movement across machines can become expensive.
```

---

# Cell 17 (Markdown)

```markdown
# Understanding Data Shuffle

Shuffle occurs when Spark redistributes data between executors.

Example:

Group sales by customer.

Records belonging to the same customer may exist on different machines.

Spark must move data across the network.

Network transfer takes time.
```

---

# Cell 18 (Markdown)

```html
<h3>Network Shuffle</h3>

<img src="../assets/Module_11/11_01_05.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark shuffle operation. Multiple executors exchanging data across the network before aggregation. Arrows between executors showing heavy data transfer. Educational architecture diagram.</p>
```

---

# Cell 19 (Markdown)

```markdown
# The Four Major Bottlenecks

| Resource | Problem |
|-----------|----------|
| CPU | Too much computation |
| Memory | Insufficient RAM |
| Disk | Slow read/write operations |
| Network | Excessive data movement |

Most Spark performance issues can be traced back to one of these categories.
```

---

# Cell 20 (Markdown)

```markdown
# Why Throwing More Hardware Doesn't Always Help

Many beginners think:

"Let's add more machines."

But this doesn't always solve the problem.

Examples:

- Poor joins remain poor joins
- Excessive shuffles remain expensive
- Bad partitioning remains bad partitioning

Optimization usually provides better gains than blindly increasing cluster size.
```

---

# Cell 21 (Markdown)

```markdown
# Performance Optimization Mindset

When a Spark job is slow, ask:

1. Is CPU overloaded?
2. Is memory insufficient?
3. Is Spark spilling to disk?
4. Is too much data being shuffled?
5. Is storage slow?
6. Is the code inefficient?

This systematic approach helps identify the true bottleneck.
```

---

# Cell 22 (Markdown)

```markdown
# Key Takeaways

- Spark jobs become slow for specific reasons.
- CPU, Memory, Disk, and Network are the four primary bottlenecks.
- Distributed systems introduce data movement costs.
- Disk spills and shuffles are common causes of slow jobs.
- Understanding bottlenecks is the first step toward optimization.

Next Lesson:

## 11.2 Spark UI Deep Dive

We will learn how to use Spark UI to identify performance problems in real jobs.
```

This lesson is typically **8–12 minutes**, highly visual, beginner-friendly, and sets up all later topics such as **Shuffle, Joins, Data Skew, AQE, Caching, and Spark UI analysis**.
