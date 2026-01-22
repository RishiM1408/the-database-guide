# Distributed SQL (NewSQL)

> **The Best of Both Worlds**: The Scale of NoSQL + The ACID of SQL.

## ⚙️ How it Works (Sharding & Consensus)

Traditional RDBMS (Postgres/MySQL) are monolithic. They scale vertically.
Distributed SQL databases split data into "Ranges" or "Tablets" and spread them across nodes.

### 1. CockroachDB (Native Distributed)

- **Storage**: **Key-Value Store** (Pebble, based on LSM-Tree) at the bottom.
- **Sharding**: **Ranges**. Data is sorted by Key.
  - `[A - Before]` -> Node 1
  - `[Before - D]` -> Node 2
- **Consensus**: **Raft**. Every Range has 3 replicas. Writes must be acknowledged by a quorum (2/3).
- **Indexing**: Global Indexes. The DB handles the routing.

### 2. Vitess (Middleware)

- **Architecture**: It's a proxy that sits in front of standard **MySQL** instances.
- **Sharding**: Defines a `VSchema` to route queries based on a Sharding Key (e.g., `user_id`).
- **Caching**: `VTTablet` manages a buffer pool better than standard MySQL connections.

## 🚀 Options

| Database        | Architecture   | Protocol | Best For                                       |
| :-------------- | :------------- | :------- | :--------------------------------------------- |
| **CockroachDB** | Cloud-Native   | Postgres | Multi-Region, "Survival Mode" reliability.     |
| **YugabyteDB**  | Cloud-Native   | Postgres | High performance, uses RocksDB.                |
| **Vitess**      | Sharding Layer | MySQL    | Hyper-scale web apps (Slack, Square, YouTube). |
| **TiDB**        | Cloud-Native   | MySQL    | HTAP (Hybrid Transactional/Analytical).        |

## 🏗️ Deep Internals: Indexing & Caching

- **Indexing**:
  - **LSM-Tree** (Cockroach/Yugabyte/TiDB): Optimized for heavy write throughput.
  - **B-Tree** (Vitess/MySQL): Optimized for read performance.
- **Caching**:
  - **Block Cache**: Integrated into the LSM engine to cache uncompressed data blocks.
  - **Row Cache**: Caches parsed SQL results for hot rows.

## ☕ Java Integration

Since they speak standard SQL wire protocols, you just use the standard drivers!

```java
// CockroachDB uses the Postgres driver
HikariConfig config = new HikariConfig();
config.setJdbcUrl("jdbc:postgresql://localhost:26257/mydb"); // Port 26257
```
