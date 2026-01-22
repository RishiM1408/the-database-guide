# Search Engines (Elasticsearch)

> **The Librarians**: Optimized for finding a needle in a haystack.

## ⚙️ How it Works (Inverted Index)

Standard databases index _rows_. Search engines index _words_.

- **Tokenization**: "The quick brown fox" &rarr; `["quick", "brown", "fox"]`.
- **Inverted Index**:
  - `"quick"` &rarr; `[Doc1, Doc5, Doc9]`
  - `"brown"` &rarr; `[Doc1, Doc2]`
- **Search**: To find "quick AND brown", intersection of the lists is `[Doc1]`. This is lightning fast.

## 🚀 Options

| Database          | Ecosystem | Best For                                            |
| :---------------- | :-------- | :-------------------------------------------------- |
| **Elasticsearch** | ELK Stack | **The Giant**. Logs, Metrics, Enterprise Search.    |
| **Solr**          | Apache    | Older, robust, great for standard search.           |
| **Meilisearch**   | Rust      | **Speed**. Typo-tolerance, e-commerce, simpler API. |
| **OpenSearch**    | AWS Fork  | AWS-native version of Elastic.                      |

## 🏗️ Deep Internals

### Indexing Strategy (Inverted Index)

- **Segments**: Data is written to immutable segments (Lucene index files).
- **Merge**: Background process merges small segments into larger ones (Compaction).
- **FST (Finite State Transducers)**: Used to compress the term dictionary in memory.

### Caching Strategy

- **OS Page Cache**: Lucene is designed to rely on the Linux Page Cache for segment files.
- **Request Cache**: Caches the results of search queries (e.g., aggregations).
- **Query Cache**: Caches the bitsets of filters (e.g., `status:published`).

## ☕ Java Integration

```java
// Spring Data Elasticsearch
@Document(indexName = "products")
public class Product {
    @Id private String id;
    private String name;
    private String description;
}

// Repository
public interface ProductRepo extends ElasticsearchRepository<Product, String> {

    // Auto-generated implementation
    List<Product> findByNameAndDescription(String name, String desc);
}

// Native Query (Complex)
// MATCH "name" fuzzy with slop 2...
```
