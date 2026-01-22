# Relational Databases (SQL)

> **The Defaults**: PostgreSQL and MySQL.

## ⚙️ Storage Engine Logic

- **Structure**: Typically **B-Tree** (Balanced Tree).
- **Mechanism**: Optimized for Read-Heavy workloads. Data is stored in fixed-size pages (usually 8KB). B-Trees allow $O(log n)$ lookups.
- **Update Cost**: Updating a row often requires rewriting the page and updating indexes (Write Amplification).

## 🏗️ Deep Internals

### Indexing Strategy (B-Tree)

- **Structure**: Balanced Tree. Depth is usually 3-4 levels.
- **Time Complexity**: $O(log_b N)$ where $b$ is the fanout (hundreds).
- **Clustered Index**: (MySQL) Data lives _inside_ the leaf nodes of the Primary Key B-Tree.
- **Heap Table**: (Postgres) Data lives in a Heap; Index just points to it (CTID).

### Caching Strategy (Buffer Pool)

- Database maintains a separate memory area (Buffer Pool) to cache heavily read pages.
- **Algorithm**: LRU (Least Recently Used) or Clock-Sweep.
- **Checkpointing**: Dirty pages in the buffer pool are periodically flushed to the disk (WAL - Write Ahead Log protects against crashes).

## 🚀 Scaling Path

1.  **Vertical Scaling (Scale-Up)**: Buy a bigger server (RAM/CPU).
    - _Limit_: Reached when cost becomes exponential or hardware maxes out (e.g., DoorDash case study).
2.  **Read Replicas**: Master-Slave replication.
    - _Limit_: Asynchronous replication lag.
3.  **Sharding**: Horizontal partitioning (e.g., Vitess for MySQL, Citus for Postgres).

## ☕ Java Integration

- **Driver**: JDBC (Blocking) is the standard. R2DBC (Non-blocking) for reactive.
- **Connection Pooling**: **HikariCP** is mandatory for production.
  ```java
  HikariConfig config = new HikariConfig();
  config.setJdbcUrl("jdbc:postgresql://localhost:5432/mydb");
  config.setUsername("user");
  config.setPassword("password");
  config.addDataSourceProperty("cachePrepStmts", "true"); // Critical for perf
  config.addDataSourceProperty("prepStmtCacheSize", "250");
  HikariDataSource ds = new HikariDataSource(config);
  ```
- **Pattern**: **Repository Pattern** (Spring Data JPA) to abstract SQL.
