# ACID, BASE, CAP, and PACELC

## 1. ACID (The Gold Standard for Money)

Transactions in RDBMS (Postgres, MySQL) typically guarantee ACID properties.

- **Atomicity**: All or nothing. If one part fails, the entire transaction rolls back.
- **Consistency**: Data moves from one valid state to another (constraints are enforced).
- **Isolation**: Concurrent transactions don't see each other's partial writes.
- **Durability**: Once committed, it stays committed even if the power plug is pulled.

> **Use Case**: Financial ledgers, Inventory management, Billing.

---

## 2. BASE (The Internet Scale standard)

NoSQL systems (Cassandra, DynamoDB) often trade strict consistency for availability and performance.

- **Basically Available**: The system works most of the time (partial failures are okay).
- **Soft state**: The state of the system may change over time, even without input.
- **Eventual consistency**: If input stops, the system will eventually become consistent.

> **Use Case**: Social media feeds, Analytics logging, Chat history.

---

## 3. The CAP Theorem

In a distributed system, you can only pick **two** of the three:

1.  **Consistency (C)**: Every read receives the most recent write or an error.
2.  **Availability (A)**: Every request receives a (non-error) response, without the guarantee that it contains the most recent write.
3.  **Partition Tolerance (P)**: The system continues to operate despite an arbitrary number of messages being dropped or delayed by the network between nodes.

> **The Reality**: In a distributed system, **P is mandatory**. You cannot choose to "not have network failures."
> Therefore, you must choose between **CP** (Consistency during partitions) or **AP** (Availability during partitions).

- **CP Systems**: Redis (single node), HBase, MongoDB (default). If the leader is down, you can't write.
- **AP Systems**: Cassandra, DynamoDB. If a node is down, write to another node.

---

## 4. The PACELC Theorem

CAP only explains what happens when there is a split (Partition). But what about when the system is running normally?
**PACELC** states:

> If there is a Partition (**P**), how does the system trade off Availability and Consistency (**A** and **C**)?
> **E**lse (**E**), when the system is running normally, how does the system trade off Latency (**L**) and Consistency (**C**)?

- **Example**: DynamoDB.
  - During Partition? It chooses **Availability** (reads/writes succeed).
  - Else (Normal)? It chooses **Latency** (sends data to replicas asynchronously) over **Consistency** (waiting for all replicas to ACK).
