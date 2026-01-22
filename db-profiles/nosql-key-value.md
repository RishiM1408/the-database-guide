# Key-Value Stores (Redis)

> **The Speed Demons**: In-memory data structures.

## ⚙️ Storage Engine Logic

- **Structure**: Hash Map (Primary), Skip List (Sorted Sets).
- **Mechanism**: All data resides in **RAM**. Persistence is handled via Snapshots (RDB) or Append-Only Files (AOF) on disk asynchronously.
- **Performance**: Sub-millisecond reads/writes ($O(1)$).

## 🏗️ Deep Internals

### Indexing Strategy (Hash Table)

- **Primary Structure**: Global Hash Table.
- **SipHash**: Redis uses SipHash to map keys to slots.
- **Rehashing**: When the table gets full, Redis expands it. This happens incrementally (Progressive Rehash) to avoid freezing the server.

### Caching Strategy

- **It IS the Cache**: Redis typically _is_ the caching layer for other DBs.
- **Eviction Policies**:
  - `volatile-lru`: Evict least recently used keys (if they have an expiry set).
  - `allkeys-lru`: Evict any key.
  - `noeviction`: Return error when full.

## 🚀 Scaling Path

1.  **Replication**: Primary-Replica for High Availability.
2.  **Redis Cluster**: Automatic sharding across multiple nodes.
    - _Trade-off_: Multi-key operations (transactions) are limited to keys on the same shard (hash slot).

## ☕ Java Integration

- **Driver**: **Jedis** or **Lettuce** (Spring Boot default).
- **Pattern**: **Proxy Pattern** (Cache-Aside).
  - _Logic_: Check Cache -> If Miss, Check DB -> Write to Cache.
  ```java
  // Simple Cache-Aside logic
  public User getUser(String userId) {
      String cacheKey = "user:" + userId;
      User user = redisTemplate.opsForValue().get(cacheKey);
      if (user == null) {
           user = userRepo.findById(userId); // DB Call
           redisTemplate.opsForValue().set(cacheKey, user, Duration.ofMinutes(10));
      }
      return user;
  }
  ```
