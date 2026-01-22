# Time-Series Databases (InfluxDB / TimescaleDB)

> **The Historians**: Optimized for "Write Once, Read Many" (WORM) metric data.

## ⚙️ How it Works (Compression & Layout)

Regular DBs are bad at time-series because B-Trees get fragmented by random IDs.
TSDBs exploit the fact that **time always moves forward**.

### 1. InfluxDB (TSM Engine)

- **Columnar Storage**: Stores timestamps and values separately.
- **Gorilla Compression**: Delta-of-delta compression.
  - _Example_: 12:00:00, 12:00:10, 12:00:20. It stores `t0`, `+10`, `+0`.
  - _Result_: Can compress 16 bytes of data into <2 bits.

### 2. TimescaleDB (Hypertables)

- **Hypertable**: An abstraction that automatically partitions data by time (e.g., 1 chunk per day).
- **Benefit**: You get SQL, JOINS, and ACID, but with TSDB performance.

## 🏗️ Deep Internals

### Indexing Strategy (TSM / B-Tree)

- **InfluxDB**: Uses **TSM Tree** (Time Structured Merge). Optimized for sorted time append.
- **TimescaleDB**: Uses B-Trees on the time column for partition pruning.

### Caching Strategy

- **WAL (Write Ahead Log)**: All writes hit the WAL first for durability.
- **Memory Index**: InfluxDB buffers recent writes in RAM (like an LSM MemTable) before flushing to TSM files.
- **Compaction**: Background jobs merge many small files into fewer, larger, highly compressed files.

## 🚀 Options

| Database        | Type  | Best For                                             |
| :-------------- | :---- | :--------------------------------------------------- |
| **InfluxDB**    | NoSQL | Pure metrics (DevOps monitoring).                    |
| **TimescaleDB** | SQL   | IoT, Financial Data (where Relational joins matter). |
| **ClickHouse**  | OLAP  | Massive Scale Analytics (Petabytes).                 |

## ☕ Java Integration

```java
// InfluxDB Client
InfluxDBClient client = InfluxDBClientFactory.create("http://localhost:8086", token);

// Write "Point" (Measurement)
Point point = Point
    .measurement("cpu")
    .addTag("host", "server-1")
    .addField("usage", 95.5)
    .time(Instant.now(), WritePrecision.NS);

writeApi.writePoint("bucket-1", "org-1", point);
```
