# Relational Databases (SQL)

> **The Defaults**: PostgreSQL and MySQL.

## ⚙️ Storage Engine Logic

- **Structure**: Typically **B-Tree** (Balanced Tree).
- **Mechanism**: Optimized for Read-Heavy workloads. Data is stored in fixed-size pages (usually 8KB). B-Trees allow $O(log n)$ lookups.
- **Update Cost**: Updating a row often requires rewriting the page and updating indexes (Write Amplification).

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
