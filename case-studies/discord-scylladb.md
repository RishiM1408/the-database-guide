# Case Study: Discord's Evolution (MongoDB &rarr; Cassandra &rarr; ScyllaDB)

> **The Journey**: From storing messages in MongoDB to a massive Cassandra cluster, and finally to ScyllaDB to conquer Garbage Collection (GC) pauses.

## 🚨 Phase 1: The MongoDB Bottleneck

In the early days (2015), Discord stored message history in a single replica set of MongoDB.

### The Trigger

- **The Scale**: Reached 100 million messages.
- **The Problem**: The data no longer fit in RAM.
- **The Latency**: MongoDB performance degrades significantly when the working set exceeds available memory (page faults).
- **The Architecture Mismatch**: Messages are write-heavy and read-heavy (tail reads). Document stores like Mongo are flexible but not optimized for the specific "bucket based" time-series access pattern Discord needed.

---

## 🛠️ Phase 2: The Move to Apache Cassandra

Discord migrated to **Apache Cassandra** (an LSM-Tree based wide-column store) to handle the write velocity.

### Why Cassandra?

- **Log-Structured Merge Tree (LSM)**: Writes are appended to a MemTable and flushed to SSTables on disk. This is $O(1)$ for writes, making it perfect for chat logs.
- **Linear Scalability**: Need more capacity? Just add more nodes.

### The New Problem: GC Pauses 🛑

By 2017, Discord was storing billions of messages. Cassandra is written in **Java**.

- **The Issue**: Java uses a Garbage Collector (GC) to manage memory.
- **The Impact**: During heavy read/write operations, the JVM would "stop the world" to clean up objects. This caused unpredictable **latency spikes** (hiccups) in the chat. A 100ms pause is noticeable in voice/chat apps.

---

## 🚀 Phase 3: The Final Shift to ScyllaDB

> **"It's like Cassandra, but without the JVM."**

ScyllaDB is a specific reimplementation of Cassandra in **C++**, built on the Seastar framework.

### The Trigger (2019)

- Cassandra cluster grew to 177 nodes.
- On-call engineers were constantly tuning the JVM to fight GC pauses.
- "Hot Partitions" (popular channels) caused specific nodes to overload.

### The Solution: ScyllaDB Architecture

1.  **Shard-per-Core Architecture**:
    - In traditional DBs, threads fight for locks on shared memory.
    - In Scylla, each CPU core is assigned a specific shard of data (RAM + CPU). There are **no locks** between cores.
2.  **No Garbage Collection**:
    - Written in C++. Manual memory management means consistent low latency.
3.  **Intelligent Scheduler**:
    - Scylla uses a custom IO scheduler to prioritize interactive traffic (user reading a message) over background compaction.

## 📊 The Results

| Metric               | Cassandra (177 Nodes)         | ScyllaDB (72 Nodes)         |
| :------------------- | :---------------------------- | :-------------------------- |
| **P99 Read Latency** | Unpredictable (spikes >100ms) | < 15ms (Consistent)         |
| **Node Count**       | 177                           | 72 (Massive Cost Reduction) |
| **Maintenance**      | High (JVM Tuning)             | Low (Autotuning)            |

## 💡 Key Takeaway for Architects

> **"Language Implementation Matters at Scale."**
>
> The architectural _design_ of Cassandra (Ring topology, Dynamo-style replication, LSM Trees) was correct for the problem. The _implementation_ (Java/JVM) became the bottleneck for Discord's specific strict-latency requirements. Moving to a native C++ implementation (ScyllaDB) allowed them to keep the Architecture while fixing the Performance.
