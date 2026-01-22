# Database Mapper: The Ultimate Cheat Sheet

This matrix compares the internal architecture, consistency models, and best use cases for every major database.

| Database          | Type        | CAP Theorem | Storage Engine      | Primary Index   | Caching Strategy      | Best For                               |
| :---------------- | :---------- | :---------- | :------------------ | :-------------- | :-------------------- | :------------------------------------- |
| **PostgreSQL**    | Relational  | CA / CP     | Heap + B-Tree       | B-Tree          | OS Page Cache+Buffer  | Core Data, Financials, Complex Queries |
| **MySQL**         | Relational  | CA / CP     | Clustered Index     | B-Tree          | InnoDB Buffer Pool    | Read-Heavy Web Apps                    |
| **CockroachDB**   | Dist. SQL   | CP          | KV (Rafty)          | LSM-Tree        | Block Cache           | Multi-Region, High-Resilience Systems  |
| **Vitess**        | Dist. SQL   | AP/CP       | MySQL (InnoDB)      | B-Tree          | Row Cache (VTTablet)  | Infinite Scale Web (YouTube/Slack)     |
| **Cassandra**     | Wide-Column | AP          | LSM-Tree            | Hash Ring       | Key Cache + Row Cache | High-Velocity Writes (IoT, Logs)       |
| **ScyllaDB**      | Wide-Column | AP          | LSM-Tree            | Hash Ring       | Row Cache             | Latency-Sensitive Massive Scale        |
| **Redis**         | Key-Value   | CP (ish)    | In-Memory           | Hash Map        | It IS the Cache       | Caching, Rate Limiting, Pub/Sub        |
| **MongoDB**       | Document    | CP          | WiredTiger (B-Tree) | B-Tree          | WiredTiger Cache      | CMS, Catalogs, Rapid Prototyping       |
| **Neo4j**         | Graph       | CA          | Native Graph        | Pointer Chasing | Page Cache            | Social Networks, Fraud Detection       |
| **Qdrant**        | Vector      | CP          | Rust (HNSW)         | HNSW            | Memory Mapped         | AI Similarity Search, RAG              |
| **Pinecone**      | Vector      | ? (Managed) | Proprietary         | HNSW/IVF        | Managed               | Managed AI Search                      |
| **Elasticsearch** | Search      | AP          | Lucene              | Inverted Index  | OS Page Cache         | Full-Text Search, Logs (ELK)           |
| **InfluxDB**      | Time-Series | AP          | TSM Tree            | TSI             | WAL + MemIndex        | Metrics, Monitoring                    |
| **TimescaleDB**   | Time-Series | CP          | Hypertables (Heap)  | B-Tree          | OS Page Cache         | Relational Time-Series Data            |

## 🧠 Dictionary of Internals

### Storage Engines

- **B-Tree**: Optimized for **Read-Heavy**. Data is stored in sorted pages. exact lookups are $O(log n)$.
- **LSM-Tree (Log-Structured Merge)**: Optimized for **Write-Heavy**. Data is appended to memory, flushed to disk as SSTables, and compacted later. Writes are $O(1)$.
- **WiredTiger**: MongoDB's engine. Uses B-Trees but supports compaction and document-level locking.
- **TSM (Time Structured Merge)**: InfluxDB's custom engine optimized for high-volume time-stamped data.

### Indexes

- **Inverted Index**: Maps words to document IDs. The core of search engines.
- **HNSW (Hierarchical Navigable Small World)**: A graph-based index for high-dimensional vectors. Finds "approximate" nearest neighbors extremely fast.
- **Pointer Chasing**: Graph DBs store direct memory pointers to related nodes. Accessing a friend-of-a-friend is an $O(1)$ memory dereference, not an $O(log n)$ index lookup.
