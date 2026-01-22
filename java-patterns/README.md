# Java Design Patterns for Data

This section details the best-suited Design Patterns for specific database types.

## 🧩 Patterns

### 1. [Repository Pattern](./repository-pattern.md) (Relational)

- **Goal**: Abstraction. Decouple domain logic from the persistence layer (Hibernate/JPA).

### 2. [Proxy Pattern](./proxy-pattern.md) (Key-Value)

- **Goal**: Performance. Implement "Cache-Aside" logic transparently.

### 3. [Data Mapper](./data-mapper.md) (Document)

- **Goal**: Complexity Management. Transform JSON documents (MongoDB) into rich Java Domain Objects.

### 4. [CQRS](./cqrs-pattern.md) (Wide-Column)

- **Goal**: Optimization. Separate read models from write models to handle the specific query needs of Cassandra.

### 5. [Resilience](./resilience.md) (Production)

- **Goal**: Stability. Circuit Breakers and Retries to survive outages.
