# ScyllaDB (The C++ Evolution)

> **The Speed Demon**: Cassandra compatible, but 10x faster.

## ⚙️ Storage Engine Logic

- **Structure**: **LSM-Tree** (Log-Structured Merge Tree).
- **Mechanism**:
  1.  Writes go to an in-memory **MemTable**.
  2.  When full, it flushes to disk as an immutable **SSTable** (Sorted String Table).
  3.  Background **Compaction** merges SSTables to remove deleted/superseded data.
- **Performance**: Writes are $O(1)$ (append-only). Reads can be slower than B-Trees if data is fragmented across many SSTables (Bloom Filters help).

## 🏗️ Deep Internals

### Indexing Strategy (LSM-Tree)

- **MemTable**: The in-memory buffer where writes happen first (Sorted Map).
- **SSTable**: The immutable file on disk.
- **Bloom Filters**: Identifying if a key is _possibly_ in an SSTable or _definitely not_. Saves disk seeks.

### Caching Strategy

- **Key Cache**: Caches the location of keys in SSTables.
- **Row Cache**: Caches the entire hot row in memory (Expensive but fast).
- **OS Page Cache**: Cassandra relies heavily on Linux caching the SSTable files in RAM.

## 🚀 Scaling Path

1.  **Peer-to-Peer (Leaderless)**: Every node is equal.
2.  **Linear Scalability**: Doubling nodes = Doubling throughput.
3.  **Cross-Datacenter Replication**: Native support for partitioning data across `us-east`, `eu-west`, etc.

## ☕ Java Integration

- **Driver**: Datastax Java Driver (Cassandra) or Scylla Java Driver (shard-aware).
- **Pattern**: **CQRS** (Command Query Responsibility Segregation).
  - _Why?_ Cassandra tables are optimized for _specific queries_. You cannot join.
  - _Logic_: You often need one table for writing `messages_by_id` and another table for reading `messages_by_user`. The App must write to both (Command) or use a materialized view.

  ```java
  // CQRS: Write path handles multiple tables/views
  public void postMessage(Message cmd) {
      // 1. Write to the main table (partitioned by ChatID)
      session.execute("INSERT INTO messages_by_chat ...", cmd);

      // 2. Write to the user timeline (partitioned by UserID)
      session.execute("INSERT INTO messages_by_user ...", cmd);
  }
  ```
