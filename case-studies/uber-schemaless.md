# Case Study: Uber's Migration from Postgres to Schemaless (MySQL)

> **The Shift**: Moving from a monolithic PostgreSQL architecture to "Schemaless," a custom-built sharding layer on top of MySQL.

## 🚨 The Trigger (The Problem)

In early 2014, Uber faced a crisis. Their trip data was growing exponentially, and their primary PostgreSQL database was effectively running out of runway.

### 1. Write Amplification & Index Bloat

PostgreSQL's design uses **Secondary Indexes** that point directly to the tuple (row) on disk. When a row is updated in Postgres, a _new_ version of the row is created (MVCC), and **ALL** indexes must be updated to point to this new physical location.

- **The Impact**: simple updates caused massive I/O storms.
- **The Cost**: Uber had many indexes on their trip table. Updating a user's status involved updating the primary record + updating every single index, leading to significant **Write Amplification**.

### 2. Replication Lag

Because of the high write volume and the nature of WAL (Write Ahead Log) propagation in Postgres (at the time), the read replicas often struggled to keep up with the primary.

- **The Impact**: Users would take a trip, but the app (reading from a replica) might not see it immediately.
- **The Consequence**: Inconsistent user states and billing issues.

### 3. Connection Overhead

PostgreSQL handles connections by forking a process per connection. This is memory-intensive compared to MySQL's thread-per-connection model.

- **The Impact**: Hard limits on the number of active connections meant they couldn't scale the application tier horizontally without hitting DB walls.

---

## 🛠️ The Solution (Schemaless)

Uber decided to build **Schemaless**, a sharded datastore built on top of MySQL.

> **Why MySQL?**
> MySQL uses **InnoDB**, which organizes data as a Clustered Index (B-Tree). Secondary indexes point to the _Primary Key_, not the physical location.
>
> - **Benefit**: When row data moves or changes (and PK stays same), secondary indexes **do not** need to be updated. This drastically reduced write amplification.

### Architecture Highlights

#### 1. Application-Level Sharding

Instead of relying on the DB to shard, logic was moved to a routing layer.

- **Trip Data**: Sharded by `TripUUID`.
- **User Data**: Sharded by `UserUUID`.

#### 3. The Data Structure (The "Cell")

Schemaless is basically a giant Key-Value store on top of MySQL.

- **Row Key**: `UUID` + `Timestamp`
- **Value**: JSON Blob

```json
{
  "cell_id": "uuid_v4",
  "shard_id": 42,
  "timestamp": 1410123456,
  "body": {
    "status": "COMPLETED",
    "driver_id": "driver_88",
    "fare": 25.5,
    "lat": 37.7749,
    "lng": -122.4194
  }
}
```

#### 4. Append-Only Data Model (The "Schemaless" part)

They treated data like an immutable ledger.

- **No Updates in Place**: Instead of `UPDATE trips SET status='COMPLETED'`, they inserted a _new_ cell with the new status and a higher timestamp.
- **Benefit**: Converting random writes (updates) into sequential writes (inserts) is significantly faster for spinning disks and SSDs.

#### 3. Buffer Pool Efficiency

MySQL's thread-per-connection model allowed for more efficient memory usage, enabling higher concurrency on the same hardware compared to their Postgres setup.

---

## 📉 The Trade-offs

| Feature         | PostgreSQL (Old)   | Schemaless/MySQL (New)            |
| :-------------- | :----------------- | :-------------------------------- |
| **Consistency** | Strong ACID        | Eventual (in some paths), Tunable |
| **Complexity**  | Low (New Monolith) | High (Custom Sharding Logic)      |
| **Updates**     | In-place (MVCC)    | Append-only (requires compaction) |
| **Joins**       | Full SQL Support   | **No Joins** (done in App)        |

## 💡 Key Takeaway for Architects

> **"Use the right tool for the read/write pattern."**
>
> Uber didn't leave Postgres because Postgres is "bad." They left because their specific workload (**high-frequency updates on indexed columns**) hit the specific architectural limits of Postgres's B-Tree implementation at that time. For a write-heavy append-only workload, the LSM-tree-like behavior they built on MySQL was the superior architectural fit.
