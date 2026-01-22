# Architectural Case Studies: The "Why" Behind the Switch

This section contains detailed "teardowns" of major database migrations in the tech industry. We analyze the specific technical triggers that forced a change and the architectural solutions that followed.

## 🏆 Case Study Index

| Company                                   | Migration                                | Primary Trigger                                   | Key Solution Pattern                                       |
| :---------------------------------------- | :--------------------------------------- | :------------------------------------------------ | :--------------------------------------------------------- |
| **[Uber](./uber-schemaless.md)**          | Postgres &rarr; Schemaless (MySQL)       | Index bloat, Replication lag, Write amplification | Application-level Sharding, Append-only Append Log         |
| **[Discord](./discord-scylladb.md)**      | MongoDB &rarr; Cassandra &rarr; ScyllaDB | GC pauses, Billions of messages, "Hot" partitions | Leaderless Replication, C++ (No GC), Seastar Framework     |
| **[DoorDash](./doordash-cockroachdb.md)** | Aurora Postgres &rarr; CockroachDB       | Vertical scaling limits, Single writer bottleneck | Distributed SQL, Multi-Master, Geo-Partitioning            |
| **[Slack](./slack-vitess.md)**            | MySQL &rarr; Vitess                      | Tenant hotspots, Connection limit exhaustion      | Vitess Middleware, Horizontal Sharding, Connection Pooling |
| **[Notion](./notion-sharding.md)**        | Postgres &rarr; Sharded Postgres         | 0.5 Trillion blocks, Vacuum overload              | Application-Level Sharding, Soft Deletes                   |

## 🧠 Common Themes

1.  **The Limits of Vertical Scaling**: almost every story begins with a single giant primary node hitting CPU/IO limits.
2.  **Write Amplification**: B-Trees (used in Postgres/MySQL) struggle with massive write throughput compared to LSM Trees (Cassandra/ScyllaDB).
3.  **Operational Complexity**: The move often trades a "simple" monolithic DB for a "complex" distributed system to gain scale.
