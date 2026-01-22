# Case Study: Notion's Scalability (Sharded Postgres)

> **The Problem**: How to store 0.5 Trillion "Blocks" (paragraphs, images, todos) in Postgres without it exploding.

## 🧱 The Notion Data Model

Everything in Notion is a **Block**.

- A page is a block.
- A paragraph is a block.
- A database row is a block.

This means the `block` table grows infinitely faster than a normal "User" or "Order" table.

## 🚨 The Trigger (2020)

Notion was running on a massive monolithic PostgreSQL instance on AWS RDS.

1.  **Vacuum Spiral of Death**: Postgres needs to clean up dead tuples (VACUUM). With billions of rows, the Vacuum process consumed so much I/O that it slowed down production queries.
2.  **Migration Fear**: They considered DynamoDB/Cassandra but realized their data model (recursive tree structure) was heavily relational. Rewriting the entire backend to NoSQL was too risky.

## 🛠️ The Solution: Application-Level Sharding

Notion decided to **keep using Postgres** but split the data across 480 logical shards (running on 32 physical hosts).

### 1. The Sharding Key: `WorkspaceID`

- All data belonging to a Workspace (e.g., "The Acme Co") lives on the same shard.
- **Why?**: Transactions usually happen within a workspace. Cross-workspace transactions are rare.

### 2. The Routing Logic (Application Side)

Unlike Slack (which used Vitess middleware), Notion built the routing logic directly into their application code.

- `BlockRepository.save(block)` &rarr; `Hash(block.workspace_id)` &rarr; `Shard #42`.

### 3. The "Soft Delete" Strategy

To kill the "Vacuum Death Spiral", they changed how they deleted data.

- Instead of `DELETE FROM blocks`, they simply updated a pointer.
- They run a custom "Garbage Collector" script at night to batch-delete old data when traffic is low, preventing Vacuum storms during the day.

## 📉 Visualizing the Split

graph TD
App["Application Server"] -->|"Hash(WorkspaceID)"| R[Router]
R -->|Shard 1-15| DB1[Physical DB Host 1]
R -->|Shard 16-30| DB2[Physical DB Host 2]
R -->|...| DB3[...]
R -->|Shard 465-480| DB32[Physical DB Host 32]

```

## 💡 Key Takeaway for Architects

> **"You don't always need NewSQL."**
>
> Notion proved you can scale boring old PostgreSQL to massive heights if you are willing to build the sharding logic yourself. By sticking with Postgres, they kept their powerful rich-text query capabilities that NoSQL stores couldn't match.
```
