# Case Study: Slack (MySQL &rarr; Vitess)

> **The Problem**: Making a massive monolithic application "Shard-Aware" without rewriting the application logic.

## 🏢 The Trigger: Hotspots & Enterprise Scale

Slack runs on a LAMP stack (Linux, Apache, MySQL, PHP).
In the beginning, they used a simple **Main + Shards** architecture.

### 1. The "Whale" Tenant Problem

Slack is multi-tenant.

- **Small Teams**: A few thousand messages.
- **IBM/Amazon/Uber**: Millions of messages per day.
- **The Issue**: If a massive customer ("Whale") lands on Shard #5, Shard #5 explodes with load while Shard #6 sits idle.
- **Hotspots**: You can't easily move a live active team to another shard without downtime.

### 2. Connection Exhaustion

- PHP uses persistent connections. As the number of web servers grew, the number of open connections to MySQL exhausted the `max_connections` limit.
- Attempting to add more replicas just to handle _connections_ (not load) is inefficient.

### 3. Routing Complexity

- The application code had to "know" about sharding.
- `getMessages(team_id)` -> App Logic -> `Connect to Shard_7` -> Query.
- This makes application logic brittle and complex.

---

## 🔮 The Solution: Vitess

Slack adopted **Vitess**, a database clustering system for horizontal scaling of MySQL, originally built by YouTube.

### Why Vitess?

#### 1. The "VTablet" Proxy (Connection Pooling)

- Vitess sits _between_ the App and MySQL.
- **Transformation**: It effectively multiplexes 100,000 incoming lightweight application connections onto a small pool of persistent MySQL connections.
- **Result**: Slack could spin up as many web servers as needed without killing the DB with connection overhead.

#### 2. Transparent Sharding (VSchema)

- **The Helper**: Vitess uses a schema definition (`VSchema`) that maps sharding keys.
- **The Magic**: The application sends a query like `SELECT * FROM messages WHERE team_id = 'XYZ'`.
- Vitess intercepts this, looks up where 'XYZ' lives, and routes the query to the correct MySQL shard.
- **Application Impact**: The PHP code thinks it's talking to a single giant MySQL database. Zero app rewrites needed for routing.

#### 3. Online Resharding

- Vitess allows splitting a shard while it is live.
- If Shard A gets too hot, Vitess can split it into Shard B and Shard C, migrate the data, and cut over traffic with microseconds of downtime.

## 📉 Visualizing the Middleware

```mermaid
graph TD
    App["Application Tier (PHP/Hack)"] -->|SQL Protocol| VT["VtGate (Vitess Proxy)"]

    VT -->|Routes Query| S1[Shard 1 (MySQL)]
    VT -->|Routes Query| S2[Shard 2 (MySQL)]
    VT -->|Routes Query| S3[Shard 3 (MySQL)]

    subgraph Vitess Cluster
    VT
    end

    subgraph Database Layer
    S1
    S2
    S3
    end
```

## 💡 Key Takeaway for Architects

> **"Abstract Complexity via Middleware."**
>
> Slack solved their scalability problem not by changing the database engine (it's still MySQL underneath!) but by introducing an intelligent **Middleware Layer** (Vitess). This allowed them to keep their existing operational expertise with MySQL while gaining the "magical" scaling properties of a NoSQL system.
