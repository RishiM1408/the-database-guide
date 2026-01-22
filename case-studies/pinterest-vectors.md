# Case Study: Pinterest Visual Search (Vector Database)

> **The Problem**: How to build "Shazam for Images" at the scale of billions of pins.

## 📌 The Challenge: Search beyond Keywords

Pinterest users want to find "a lamp that looks like this one."

- **Old Way**: Tagging images with text. "Modern", "Lamp", "Gold".
- **Failure Mode**: Manual tagging is slow, inaccurate, and misses the nuance of "shape" and "texture."

## 🧠 The Solution: Visual Embeddings & Vector Search

Pinterest uses Deep Learning (Convolutional Neural Networks) to convert every image into a **Vector** (a list of numbers, e.g., `[0.1, 0.5, ... 0.9]`).

### 1. Generating Embeddings

- **The Model**: They trained a model (PinSage) that "looks" at an image and outputs a 256-dimensional vector.
- **The Magic**: Images that _look similar_ (visually) will have vectors that are numerically _close_ to each other in mathematical space (Cosine Similarity).

### 2. Indexing with HNSW

You cannot scan 50 billion vectors every time a user searches. It would take years.

- **HNSW (Hierarchical Navigable Small World)**: They effectively build a map of the vector space.
- **Query**: "Find the 50 closest neighbors to `Vector_A`."
- **Performance**: Sub-millisecond response time.

## 🏗️ The Architecture (Manifold & Scorpion)

### 1. Manifold (The Partitioning Service)

Managing 18 Billion pins across thousands of nodes is hard.

- **Role**: Manifold is a generic resource mapping layer. It maps `PinID` -> `Shard_705` -> `Host_IP`.
- **Benefit**: It decouples the application from physical hardware. If a node dies, Manifold re-maps `Shard_705` to a backup node instantly.

### 2. Scorpion (The Serving Layer)

When you search, you don't just query the DB directly.

- **Step 1 (Candidate Generation)**: Get he top 1k nearest neighbors from HNSW.
- **Step 2 (Ranking)**: Pass those 1k candidates through a lightweight ML model (Scorpion) to filter by stock availability, spam score, and user personalization.
- **Step 3 (Fanout)**: The query is scattered to multiple leaf nodes in parallel.

### 3. HNSW Configuration

Pinterest runs HNSW with specific tuning:

- **`M` (Connection Count)**: Higher = More RAM, but better recall.
- **`efConstruction`**: They tune this high during index build time to ensure the graph is high quality, even if building takes longer.

## 📉 Visualizing High-Dimensional Space

- **Input**: User crops a photo of a **Red Chair**.
- **Vector Engine**: Finds a cluster of vectors in the "Red furniture" region of the hyperspace.
- **Output**: 50 other Red Chairs.

## 💡 Key Takeaway

> **"Search by Meaning, Not Key."**
>
> Vector Databases bridge the gap between unstructured data (images, audio, long text) and structured queries. They allow you to query based on **semantic similarity** rather than exact string matches.
