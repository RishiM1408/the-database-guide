# Key-Value Stores (Redis)

> **The Speed Demons**: In-memory data structures.

## ⚙️ Storage Engine Logic

- **Structure**: Hash Map (Primary), Skip List (Sorted Sets).
- **Mechanism**: All data resides in **RAM**. Persistence is handled via Snapshots (RDB) or Append-Only Files (AOF) on disk asynchronously.
- **Performance**: Sub-millisecond reads/writes ($O(1)$).

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
