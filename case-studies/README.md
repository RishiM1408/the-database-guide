# Architectural Case Studies: The "Why" Behind the Switch

> **"Real world systems are the ultimate benchmark."**

This section contains detailed "teardowns" of major database migrations in the tech industry. We analyze the specific technical triggers that forced a change and the architectural solutions that followed.

## 🏆 Case Study Index

| Company                                    | Migration                                | Primary Trigger                                   | Key Solution Pattern                                       |
| :----------------------------------------- | :--------------------------------------- | :------------------------------------------------ | :--------------------------------------------------------- |
| **[Uber](./uber-schemaless.md)**           | Postgres &rarr; Schemaless (MySQL)       | Index bloat, Replication lag, Write amplification | Application-level Sharding, Append-only Log                |
| **[Discord](./discord-scylladb.md)**       | MongoDB &rarr; Cassandra &rarr; ScyllaDB | GC pauses, Billions of messages, "Hot" partitions | Leaderless Replication, C++ (No GC), Seastar Framework     |
| **[DoorDash](./doordash-cockroachdb.md)**  | Aurora Postgres &rarr; CockroachDB       | Vertical scaling limits, Single writer bottleneck | Distributed SQL, Multi-Master, Geo-Partitioning            |
| **[Slack](./slack-vitess.md)**             | MySQL &rarr; Vitess                      | Tenant hotspots, Connection limit exhaustion      | Vitess Middleware, Horizontal Sharding, Connection Pooling |
| **[Notion](./notion-sharding.md)**         | Postgres &rarr; Sharded Postgres         | 0.5 Trillion blocks, Vacuum overload              | Application-Level Sharding, Soft Deletes                   |
| **[Stripe](./stripe-radar.md)**            | Relational &rarr; Graph (Neo4j)          | Fraud rings, Many-to-Many recursive joins         | Knowledge Graph, Index-Free Adjacency                      |
| **[Pinterest](./pinterest-vectors.md)**    | Keyword Search &rarr; Vector Search      | "Visual" similarity, Semantic Gap                 | Embeddings, HNSW Index, ANN Search                         |
| **[Robinhood](./robinhood-timeseries.md)** | Postgres &rarr; InfluxDB                 | Massive Tick data volume, Immutable logs          | Time-Structured Merge Tree, Retention Policies             |
| **[Wikipedia](./wikipedia-elastic.md)**    | MySQL FullText &rarr; Elasticsearch      | Typos, Stemming, Multilingual complexity          | Inverted Index, TF-IDF, Tokenizers                         |

## 🧠 Common Themes

### 1. The Limits of Vertical Scaling (The "Wall")

Almost every story begins with a single giant primary node (often Postgres or MySQL) hitting CPU/IO limits (DoorDash, Uber).

- **Lesson**: You can only buy a server so big. Eventually, you **must** shard or distribute.

### 2. The Move to "Specialized Engines"

In the early days, SQL was used for everything. Modern architectures offload specific problems to specialized engines:

- **Search**: Moved to Elasticsearch (Wikipedia).
- **Relationships**: Moved to Graph DBs (Stripe).
- **Ticks/Logs**: Moved to Time-Series DBs (Robinhood).
- **AI/Images**: Moved to Vector DBs (Pinterest).

### 3. Middleware vs. Native Distribution

There are two ways to solve scale:

1.  **Middleware**: Keep the old database (MySQL/Postgres) but put a smart robot in front of it (Vitess @ Slack, "Schemaless" @ Uber).
2.  **Native Distribution**: Adopt a new database that handles sharding for you (CockroachDB @ DoorDash, ScyllaDB @ Discord).

### 4. Write Amplification is the Enemy

Uber left Postgres partially because every update forced updates to all indexes (B-Tree). Discord left Mongo because GC pauses killed latency.

- **Lesson**: At scale, internal data structures (LSM-Trees, Write-Ahead Logs, Garbage Collection) matter more than features.
