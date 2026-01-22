# Apache Cassandra

> **The Heavyweight Champion**: The original "Masterless" database.

## 🐘 The JVM Giant

Cassandra is written in Java used effectively by Apple, Netflix, and Uber for massive scale.

## ⚙️ Usage vs ScyllaDB

| Feature                | Cassandra (Java)                          | ScyllaDB (C++)                  |
| :--------------------- | :---------------------------------------- | :------------------------------ |
| **Architecture**       | JVM-based. Uses Heap.                     | Shard-per-Core (Seastar).       |
| **Garbage Collection** | Can cause "Stop-the-World" pauses.        | None. Manual memory management. |
| **Tuning**             | Requires deep JVM tuning (Heap size, GC). | Auto-tuning.                    |
| **Ecosystem**          | Massive. Every tool works with it.        | Compatible, but newer.          |

## 🏗️ Deep Internals

### 1. The Ring (Consistent Hashing)

- **Token Ring**: Nodes are assigned "Tokens" (ranges of numbers).
- **VNodes (Virtual Nodes)**: One physical server owns ~256 small random ranges. This spreads the load evenly when a node is added/removed.

### 2. Storage Engine (LSM-Tree)

- **Commit Log**: Append-only log for durability (WAL).
- **MemTable**: On-heap structure for incoming writes.
- **SSTable on Disk**: Immutable data files.
- **Off-Heap Memory**: Modern Cassandra uses off-heap memory for Bloom Filters and Compression Metadata to reduce GC pressure.

### 3. The "GC Pause" Problem

Since Cassandra runs on Java, massive heaps (16GB+) can trigger long Garbage Collection pauses.

- **Mitigation**: Use ZGC or Shenandoah GC (Low latency GCs) in newer Java versions.

## ☕ Java Integration

Cassandra _is_ Java, so the integration is first-class.

```java
// Datastax Java Driver
CqlSession session = CqlSession.builder().build();

// Prepared Statements (Critical for Performance)
// Prevents parsing query string every time
PreparedStatement ps = session.prepare("INSERT INTO users (id, name) VALUES (?, ?)");

session.execute(ps.bind(UUID.randomUUID(), "Alice"));
```
