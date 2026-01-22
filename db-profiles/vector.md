# Vector Databases (AI & RAG)

> **The New Kids**: Databases designed to store "embeddings" (arrays of floats) and find similar items.

## ⚙️ How it Works (Internals)

Standard databases index _values_ (e.g., `WHERE age = 25`).
Vector databases index _meaning_ (e.g., "Find documents semantically similar to this query").

### The Data Structure: HNSW

**Hierarchical Navigable Small World** graphs are the gold standard.

- **Concept**: Think of it like a "Six Degrees of Kevin Bacon" graph for data points.
- **Layers**: Top layers have long links (highways) to jump across the vector space. Bottom layers have short links (local roads) to find precise neighbors.
- **Search**: You start at the top, jump close to your target, drop down a layer, and refine. $O(log n)$ scaling.

## 🚀 Options

| Database     | Type        | Engine      | Best For                                          |
| :----------- | :---------- | :---------- | :------------------------------------------------ |
| **Qdrant**   | Open Source | Rust        | **High Performance**, Filtering + Search together |
| **Pinecone** | Managed     | Proprietary | **Ease of Use**, Pure SaaS                        |
| **Milvus**   | Open Source | Go          | **Massive Scale** (Billions of vectors)           |
| **Weaviate** | Open Source | Go          | **Hybrid Search** (Vector + Keyword)              |

## 🏗️ Deep Internals

### Indexing Strategy (HNSW / IVF)

- **HNSW**: Graph navigation. High RAM usage, fastest search.
- **IVF (Inverted File)**: Clusters vectors into lists (Voronoi cells). Scans only relevant lists. Low RAM usage, slower search.

### Caching Strategy

- **Memory Bound**: Vector indexes (HNSW) are heavy. They want to live in RAM.
- **mmap**: Many vector DBs map the index file directly to memory, letting the OS manage paging.
- **Quantization**: To save RAM, databases compress 32-bit floats into 8-bit integers (Scalar Quantization) or smaller. This trades a tiny bit of accuracy for 4x storage capacity.

## ☕ Java Integration

```java
// Logic: Vector DBs usually don't have complex Java ORMs.
// You use a client to push vectors.
QdrantClient client = new QdrantClient(QdrantGrpcClient.newBuilder("localhost", 6334).build());

// 1. Upsert a Vector
PointStruct point = PointStruct.newBuilder()
    .setId(id(1))
    .setVectors(vectors(0.9f, 0.1f, -0.5f))
    .putPayload("category", "sports")
    .build();

client.upsertAsync("my_collection", List.of(point));

// 2. Search (ANN - Approximate Nearest Neighbor)
List<ScoredPoint> results = client.searchAsync(
    SearchPoints.newBuilder()
        .setCollectionName("my_collection")
        .setVector(targetEmbedding)
        .setFilter(filter("category", "sports")) // Pre-filtering!
        .setLimit(5)
        .build()
).get();
```
