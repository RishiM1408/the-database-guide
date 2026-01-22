# Database Mapper: The Ultimate Cheat Sheet

This matrix compares the internal architecture, consistency models, and best use cases for every major database.

| Database          | Type        | CAP Theorem | Storage Engine      | Primary Index   | Best For                               |
| :---------------- | :---------- | :---------- | :------------------ | :-------------- | :------------------------------------- |
| **PostgreSQL**    | Relational  | CA / CP     | Heap + B-Tree       | B-Tree          | Core Data, Financials, Complex Queries |
| **MySQL**         | Relational  | CA / CP     | Clustered Index     | B-Tree          | Read-Heavy Web Apps                    |
| **CockroachDB**   | Dist. SQL   | CP          | KV (Rafty)          | LSM-Tree        | Multi-Region, High-Resilience Systems  |
| **Cassandra**     | Wide-Column | AP          | LSM-Tree            | Hash Ring       | High-Velocity Writes (IoT, Logs)       |
| **ScyllaDB**      | Wide-Column | AP          | LSM-Tree            | Hash Ring       | Latency-Sensitive Massive Scale        |
| **Redis**         | Key-Value   | CP (ish)    | In-Memory           | Hash Map        | Caching, Rate Limiting, Pub/Sub        |
| **MongoDB**       | Document    | CP          | WiredTiger (B-Tree) | B-Tree          | CMS, Catalogs, Rapid Prototyping       |
| **Neo4j**         | Graph       | CA          | Native Graph        | Pointer Chasing | Social Networks, Fraud Detection       |
| **Qdrant**        | Vector      | CP          | Rust (HNSW)         | HNSW            | AI Similarity Search, RAG              |
| **Pinecone**      | Vector      | ? (Managed) | Proprietary         | HNSW/IVF        | Managed AI Search                      |
| **Elasticsearch** | Search      | AP          | Lucene              | Inverted Index  | Full-Text Search, Logs (ELK)           |
| **InfluxDB**      | Time-Series | AP          | TSM Tree            | TSI             | Metrics, Monitoring                    |
| **TimescaleDB**   | Time-Series | CP          | Hypertables (Heap)  | B-Tree          | Relational Time-Series Data            |

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
