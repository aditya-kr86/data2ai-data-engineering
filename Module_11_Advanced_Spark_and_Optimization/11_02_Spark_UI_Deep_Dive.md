# Cell 1 (Markdown)

```markdown id="a1b2c3"
# 11.2 Spark UI Deep Dive

## Learning Objectives

By the end of this lesson you will be able to:

- Understand why Spark UI is important
- Navigate the Spark UI interface
- Analyze Jobs Tab
- Analyze Stages Tab
- Analyze Executors Tab
- Analyze SQL Tab
- Identify common performance bottlenecks
- Begin debugging slow Spark jobs

Spark UI is one of the most important tools for a Data Engineer working with Apache Spark.
```

---

# Cell 2 (Markdown)

```markdown id="d4e5f6"
# Why Do We Need Spark UI?

Imagine your Spark job takes:

- 30 seconds yesterday
- 20 minutes today

The question becomes:

Why?

Without visibility into execution, debugging is difficult.

Spark UI provides detailed information about:

- Job execution
- Stage execution
- Task execution
- Resource utilization
- Query plans

It helps engineers find bottlenecks and optimize jobs.
```

---

# Cell 3 (Markdown)

```markdown id="g7h8i9"
# What is Spark UI?

Spark UI is a web-based monitoring dashboard.

Whenever a Spark application runs, Spark generates an interface showing:

- Jobs
- Stages
- Executors
- Storage
- SQL Queries
- Environment Information

Think of Spark UI as the "control room" of your Spark application.
```

---

# Cell 4 (Markdown)

```markdown id="j1k2l3"
# Accessing Spark UI

When running Spark locally:

Spark UI is usually available at:

http://localhost:4040

For cloud platforms such as Databricks or EMR, Spark UI is integrated into the platform interface.

Every Spark application generates its own UI for monitoring execution.
```

---

# Cell 5 (Markdown)

```html id="m4n5o6"
<h3>Spark UI Overview</h3>

<img src="../assets/Module_10/11_02_01.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark UI dashboard overview showing top navigation tabs including Jobs, Stages, Executors, Storage, SQL and Environment. Clean educational screenshot-style illustration. White background.</p>
```

---

# Cell 6 (Markdown)

```markdown id="p7q8r9"
# Spark UI Navigation

The most important tabs for performance analysis are:

1. Jobs Tab
2. Stages Tab
3. Executors Tab
4. SQL Tab

These four tabs help answer:

- What is running?
- Where is time being spent?
- Which executor is overloaded?
- Which query is slow?
```

---

# Cell 7 (Markdown)

```markdown id="s1t2u3"
# Jobs Tab Introduction

The Jobs Tab provides a high-level overview of application execution.

A Job represents a complete Spark action.

Examples:

- count()
- collect()
- show()
- write()

Every action triggers a new Spark Job.
```

---

# Cell 8 (Markdown)

```markdown id="v4w5x6"
# Information Available in Jobs Tab

For every job, Spark UI shows:

- Job ID
- Description
- Submission Time
- Duration
- Number of Stages
- Completion Status

This allows engineers to quickly identify slow jobs.
```

---

# Cell 9 (Markdown)

```html id="y7z8a9"
<h3>Jobs Tab Example</h3>

<img src="../assets/Module_10/11_02_02.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark Jobs Tab showing multiple jobs with Job ID, Duration, Status, and Stage progress bars. One job highlighted as significantly slower than others. Educational monitoring dashboard.</p>
```

---

# Cell 10 (Markdown)

```markdown id="b1c2d3"
# How to Use Jobs Tab

When investigating a slow application:

Step 1:
Find the slowest job.

Step 2:
Check duration.

Step 3:
Open the job details.

Step 4:
Analyze its stages.

Jobs Tab tells us "which job is slow."

Stages Tab tells us "why it is slow."
```

---

# Cell 11 (Markdown)

```markdown id="e4f5g6"
# Stages Tab Introduction

Every Spark Job is divided into multiple stages.

A Stage contains a collection of tasks that can execute in parallel.

Stages are created whenever Spark encounters a shuffle boundary.

This makes the Stages Tab extremely useful for performance analysis.
```

---

# Cell 12 (Markdown)

```markdown id="h7i8j9"
# What Information Does Stages Tab Show?

The Stages Tab displays:

- Stage ID
- Duration
- Number of Tasks
- Input Data Size
- Shuffle Read
- Shuffle Write

This information helps locate expensive operations.
```

---

# Cell 13 (Markdown)

```html id="k1l2m3"
<h3>Stages Tab Overview</h3>

<img src="../assets/Module_10/11_02_03.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark Stages Tab displaying Stage ID, task counts, duration, shuffle read and shuffle write metrics. One stage highlighted with very high shuffle activity. Educational infographic.</p>
```

---

# Cell 14 (Markdown)

```markdown id="n4o5p6"
# Detecting Shuffle Problems

A common performance issue is excessive shuffle.

Indicators:

- High Shuffle Read
- High Shuffle Write
- Long Stage Duration

Large shuffle operations often occur during:

- groupBy()
- join()
- distinct()
- orderBy()

The Stages Tab helps identify these costly operations.
```

---

# Cell 15 (Markdown)

```markdown id="q7r8s9"
# Understanding Task Distribution

Within each stage, Spark creates tasks.

Ideally:

All tasks should finish around the same time.

If some tasks take significantly longer than others:

You may have Data Skew.

This is a common optimization challenge.
```

---

# Cell 16 (Markdown)

```html id="t1u2v3"
<h3>Data Skew Example</h3>

<img src="../assets/Module_10/11_02_04.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark Stage visualization showing many tasks finishing quickly while one task runs significantly longer. Illustration of data skew causing performance issues. White background.</p>
```

---

# Cell 17 (Markdown)

```markdown id="w4x5y6"
# Executors Tab Introduction

Executors are worker processes responsible for executing tasks.

The Executors Tab provides visibility into resource utilization.

It helps answer:

- Which executor is busy?
- Which executor is idle?
- Is memory usage too high?
- Are tasks evenly distributed?
```

---

# Cell 18 (Markdown)

```markdown id="z7a8b9"
# Metrics Available in Executors Tab

Important metrics include:

- Executor ID
- Active Tasks
- Completed Tasks
- Memory Usage
- Disk Usage
- Shuffle Read
- Shuffle Write

These metrics reveal resource bottlenecks.
```

---

# Cell 19 (Markdown)

```html id="c1d2e3"
<h3>Executors Tab Metrics</h3>

<img src="../assets/Module_10/11_02_05.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark Executors Tab showing executor IDs, active tasks, memory consumption, shuffle metrics and task counts. One executor highlighted with unusually high memory usage. Educational dashboard illustration.</p>
```

---

# Cell 20 (Markdown)

```markdown id="f4g5h6"
# Identifying Memory Problems

Suppose one executor shows:

- High memory usage
- Frequent disk spills
- Slow task completion

This indicates a memory bottleneck.

Potential solutions include:

- Increasing executor memory
- Repartitioning data
- Reducing cache usage
```

---

# Cell 21 (Markdown)

```markdown id="i7j8k9"
# SQL Tab Introduction

When using DataFrames and Spark SQL,
Spark generates SQL execution plans internally.

The SQL Tab provides visibility into:

- Query execution
- Query duration
- Query plans
- Operator performance

This tab is extremely useful for DataFrame optimization.
```

---

# Cell 22 (Markdown)

```markdown id="l1m2n3"
# Information Available in SQL Tab

The SQL Tab shows:

- Query Text
- Query Duration
- Physical Plan
- Execution Statistics

You can see exactly how Spark executes a query behind the scenes.
```

---

# Cell 23 (Markdown)

```html id="o4p5q6"
<h3>SQL Tab Overview</h3>

<img src="../assets/Module_10/11_02_06.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Apache Spark SQL Tab displaying SQL query execution details, physical execution plan tree, operator metrics and execution duration. Educational Spark monitoring interface.</p>
```

---

# Cell 24 (Markdown)

```markdown id="r7s8t9"
# Reading Query Plans

A query plan shows the operations Spark performs.

Examples:

- Scan
- Filter
- Join
- Aggregate
- Sort

Understanding query plans helps identify expensive operators.

This becomes very important when optimizing joins and aggregations.
```

---

# Cell 25 (Markdown)

```html id="u1v2w3"
<h3>Physical Query Plan</h3>

<img src="../assets/Module_10/11_02_07.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark SQL physical execution plan tree showing Scan, Filter, Join and Aggregate operators connected in execution flow. Clean educational architecture diagram.</p>
```

---

# Cell 26 (Markdown)

```markdown id="x4y5z6"
# Typical Debugging Workflow

When a Spark job is slow:

Step 1:
Open Jobs Tab

Step 2:
Identify the slow job

Step 3:
Open Stages Tab

Step 4:
Find expensive stages

Step 5:
Check Executors Tab

Step 6:
Inspect resource usage

Step 7:
Analyze SQL Tab

Step 8:
Review execution plan

This workflow is commonly used by Spark engineers.
```

---

# Cell 27 (Markdown)

```markdown id="a7b8c9"
# Example Investigation

Suppose a job takes 25 minutes.

Jobs Tab:
Identifies the slow job.

Stages Tab:
Shows large shuffle activity.

Executors Tab:
Reveals memory pressure.

SQL Tab:
Shows an expensive Sort-Merge Join.

Root cause identified.

Optimization can now begin.
```

---

# Cell 28 (Markdown)

```html id="d1e2f3"
<h3>Spark UI Investigation Flow</h3>

<img src="../assets/Module_10/11_02_08.png" alt="Image Prompt" width="75%">

<p><b>Image Prompt:</b> Spark performance troubleshooting workflow diagram. Jobs Tab → Stages Tab → Executors Tab → SQL Tab leading to Root Cause Analysis and Optimization. Modern educational infographic. White background.</p>
```

---

# Cell 29 (Markdown)

```markdown id="g4h5i6"
# Key Takeaways

- Spark UI is the primary monitoring tool for Spark applications.
- Jobs Tab identifies slow jobs.
- Stages Tab identifies expensive stages and shuffle operations.
- Executors Tab reveals resource bottlenecks.
- SQL Tab helps analyze query execution plans.
- Effective optimization begins with proper investigation.

Spark UI is one of the most valuable tools for production Data Engineering.
```

---

# Cell 30 (Markdown)

```markdown id="j7k8l9"
# Next Lesson

## 11.3 Understanding Shuffle

In the next lesson we will explore:

- What is Shuffle?
- Why Shuffle is expensive
- Shuffle Read vs Shuffle Write
- How Shuffle affects Spark performance
- Techniques to reduce shuffle overhead

Understanding Shuffle is essential before learning advanced Spark optimization.
```

**Estimated duration:** 10–12 minutes
**Images required:** 8
**Difficulty:** Beginner-friendly
**Foundation for:** Shuffle, Data Skew, Join Optimization, AQE, Caching, and Production Performance Tuning.
