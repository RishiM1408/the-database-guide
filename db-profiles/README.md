# Database Profiles: The Engines Under the Hood

This section breaks down the internal architecture, scaling capability, and Java integration patterns for major database categories.

## 🗂️ Categories

### 1. [Relational (SQL)](./relational.md)

- **Systems**: PostgreSQL, MySQL, CockroachDB.
- **Best For**: Financials, Identity, Complex relationships.
- **[Deep Dive: Postgres (The Everything Database)](./postgres-everything.md)**
- **[Deep Dive: Distributed SQL](./distributed-sql.md)** (CockroachDB, Vitess)

### 2. [Key-Value Stores](./nosql-key-value.md)

- **Systems**: Redis, Memcached.
- **Best For**: Caching, Session Management, Real-time counters.

### 3. [Wide-Column Stores](./nosql-wide-column.md)

- **Systems**: Cassandra, ScyllaDB.
- **Best For**: High-velocity writes, Time-series data, Append-only logs.

### 4. [Document Stores](./nosql-document.md)

- **Systems**: MongoDB, Couchbase.
- **Best For**: Content management, Catalogs.

### 5. [Vector Databases](./vector.md)

- **Systems**: Qdrant, Pinecone, Milvus.
- **Best For**: AI, Semantic Search, RAG.

### 6. [Graph Databases](./graph.md)

- **Systems**: Neo4j, Amazon Neptune.
- **Best For**: Social Networks, Fraud Detection.

### 7. [Search Engines](./search.md)

- **Systems**: Elasticsearch, Solr.
- **Best For**: Full-Text Search, Logs.

### 8. [Time-Series](./time-series.md)

- **Systems**: InfluxDB, TimescaleDB.
- **Best For**: IoT, Metrics, Real-time Analysis.
