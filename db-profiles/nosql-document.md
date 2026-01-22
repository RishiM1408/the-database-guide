# Document Stores (MongoDB)

> **The Flexible JSON Vaults**: No Schema? No Problem.

## ⚙️ How it Works (B-Tree & WiredTiger)

- **Storage**: Data is stored as BSON (Binary JSON).
- **Index**: Uses standard **B-Trees**, just like Relational DBs!
- **Sharding**: Automatic sharding via "Chunks". When a chunk grows too large (e.g., 64MB), it splits.

## 🏗️ Deep Internals

### Indexing Strategy (B-Tree + Geo)

- **Standard**: B-Tree for scalar fields.
- **Multikey**: For arrays (creates index entry for _each_ element).
- **2dsphere**: For geospatial queries.

### Caching Strategy (WiredTiger)

- **WiredTiger Cache**: The engine has its own internal cache (usually 50% of RAM - 1GB).
- **Compression**: Data in cache is uncompressed (usually), data on disk is compressed (Snappy/Zstd).
- **Journaling**: Write-ahead-log for durability.

## 🚀 Options

| Database      | Type  | Best For                                     |
| :------------ | :---- | :------------------------------------------- |
| **MongoDB**   | CP    | The standard. Great tooling.                 |
| **Couchbase** | CP/AP | Uses a KV-store engine + Query layer (N1QL). |
| **RavenDB**   | ACID  | .NET native, ACID guarantees.                |

## ☕ Java Integration

```java
// Spring Data MongoDB
@Document(collection = "users")
public class User {
    @Id private String id;
    private String name;
    private Address address; // Nested Object!
}

// Repository
public interface UserRepo extends MongoRepository<User, String> {

    // Finds inside nested objects automatically
    List<User> findByAddressCity(String city);
}
```
