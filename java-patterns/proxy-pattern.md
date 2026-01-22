# Proxy Pattern (Redis Caching)

> **Goal**: Adding caching behavior without modifying the core service logic.

## The Pattern

Use a Proxy that wraps the real service. It checks the cache first (Cache-Aside).

### The Interface

```java
public interface ProductService {
    Product getProduct(String id);
}
```

### The "Real" Implementation

```java
public class RealProductService implements ProductService {
    public Product getProduct(String id) {
        // Expensive DB Call (e.g., 200ms)
        return db.find(id);
    }
}
```

### The Cached Proxy

```java
public class CachedProductService implements ProductService {
    private final ProductService realService;
    private final RedisClient redis;

    public Product getProduct(String id) {
        // 1. Check Cache
        String cacheKey = "product:" + id;
        Product cached = redis.get(cacheKey);
        if (cached != null) {
            return cached; // Fast! (1ms)
        }

        // 2. Fallback to Real Service
        Product p = realService.getProduct(id);

        // 3. Write to Cache (Cache-Aside)
        redis.set(cacheKey, p, Duration.ofMinutes(10));

        return p;
    }
}
```
