# Vector Databases

## What is it?
A vector database stores data as vector embeddings (numerical representations) and enables fast similarity search. Instead of exact matching (SQL's "WHERE name = 'John'"), vector databases find "nearest neighbors" using distance metrics like cosine similarity, Euclidean distance, or dot product. Examples: FAISS (Facebook), ChromaDB, Pinecone, Weaviate, Milvus.

## Why do we use it?
Traditional databases can't do semantic search — they match exact keywords. Vector databases find meaning: searching "canine" returns results about "dogs". They're essential for RAG, recommendation systems, image search, anomaly detection, and any AI application needing similarity matching at scale.

## Real-Time Example 1
**E-Commerce Product Search** — Step 1: Each product (shoes, laptops, books) is converted to an embedding vector using a model. Step 2: User searches "comfortable running shoes". Step 3: Query is embedded and vector DB finds nearest products. Step 4: Results include "Nike Air Max", "Adidas Ultraboost" — even if title doesn't contain "comfortable". Step 5: Shown to user in milliseconds. Semantic search captures intent, not just keywords.

## Real-Time Example 2
**Anomaly Detection in Manufacturing** — Step 1: Sensors collect machine vibration data. Step 2: Normal patterns are embedded and stored. Step 3: Each new reading is embedded and compared. Step 4: If distance from normal patterns exceeds threshold, flag as anomaly. Step 5: Maintenance team is alerted. Finding a "dissimilar" vector is as fast as finding a "similar" one.

## Concept Example 1
**FAISS: Similarity Search**
```python
import faiss
import numpy as np

# Create 1000 random vectors of dimension 128
d = 128
nb = 1000  # database size
nq = 1     # number of queries

np.random.seed(42)
xb = np.random.random((nb, d)).astype('float32')
xq = np.random.random((nq, d)).astype('float32')

# Build index
index = faiss.IndexFlatL2(d)  # L2 (Euclidean) distance
index.add(xb)                 # Add vectors

# Search
k = 5  # top-5 results
distances, indices = index.search(xq, k)
print(f"Query vector index: 0")
print(f"Top 5 nearest neighbors: {indices[0]}")
print(f"Distances: {distances[0]}")
```
**Input:** 1000 random 128-dim vectors (database) + 1 query vector
**Output:** Indices of 5 nearest neighbors and their distances
**Explanation:** FAISS (Facebook AI Similarity Search) is the most popular library for vector search. IndexFlatL2 does brute-force search (exact, but slow for millions). For large scale, use IVF (inverted file index) or HNSW (hierarchical navigable small world).

## Concept Example 2
**ChromaDB: Simple Vector DB**
```python
import chromadb

# Initialize client (in-memory or persistent)
client = chromadb.PersistentClient(path="./chroma_db")

# Create collection
collection = client.create_collection(
    name="products",
    metadata={"hnsw:space": "cosine"}  # distance metric
)

# Add documents with embeddings
collection.add(
    documents=[
        "Wireless Bluetooth headphones",
        "Noise cancelling headphones",
        "Gaming keyboard with RGB"
    ],
    metadatas=[
        {"category": "audio", "price": 50},
        {"category": "audio", "price": 200},
        {"category": "gaming", "price": 150}
    ],
    ids=["prod1", "prod2", "prod3"]
)

# Search
results = collection.query(
    query_texts=["Best headphones for music"],
    n_results=2
)
print(results["documents"])      # [["Wireless...", "Noise cancelling..."]]
print(results["distances"][0])   # similarity scores
print(results["metadatas"][0])   # metadata
```
**Input:** 3 products with text and metadata, query "Best headphones for music"
**Output:** Top 2 matching products with scores and metadata
**Explanation:** ChromaDB handles embedding internally (using all-MiniLM-L6-v2 by default) or accepts pre-computed embeddings. It supports metadata filtering (e.g., price < 100) combined with vector search.

## Common Mistakes
- Using cosine similarity when Euclidean distance is more appropriate (and vice versa).
- Not normalizing vectors before using cosine similarity.
- Using brute-force (IndexFlat) for millions of vectors — too slow.
- Ignoring metadata filtering — vector search alone can return irrelevant results.
- Not updating indexes when documents change — stale vectors.
- Using wrong embedding dimension for the database (e.g., ada-002 is 1536-dim).

## How Interviewers Ask This
- "What is a vector database and when would you use it?"
- "How does similarity search work?"
- "Compare FAISS, Pinecone, and Weaviate."
- "What distance metrics are used in vector search?"
- "How do you scale vector search to millions of vectors?"

## How To Impress The Interviewer
- Explain ANN (Approximate Nearest Neighbor) vs exact search: exact is O(n), ANN (IVF, HNSW) is O(log n) with slight accuracy loss.
- Discuss HNSW algorithm: builds a multi-layer graph where upper layers are "expressways" and lower layers are "local roads". Search is ~O(log n).
- Know that vector DBs support CRUD — adding, deleting, and updating vectors. FAISS doesn't easily support deletion (need to rebuild).
- Talk about hybrid search: combine vector similarity with keyword (BM25) using reciprocal rank fusion.
- Mention disk-based vs in-memory: FAISS is mostly in-memory, Milvus supports disk-based indexes for huge datasets.

## Most Asked Questions

**Q1: What is a vector database?**
A: Stores and searches vector embeddings by similarity. Unlike SQL databases (exact match), vector DBs find "nearest neighbors" using distance metrics. Example: Searching "red car" finds images of red vehicles, not just text containing "red car."

**Q2: What are vector embeddings?**
A: Numerical representations of data (text, images, audio) in vector space. Similar items have similar vectors. Example: "king" - "man" + "woman" ≈ "queen". Text-embedding-ada-002 creates 1536-dimensional vectors.

**Q3: What similarity metrics are used?**
A: Cosine similarity (angle between vectors), Euclidean/L2 distance (straight-line distance), Dot product (magnitude + angle). Cosine is most common for text. L2 for image search.

**Q4: What is ANN (Approximate Nearest Neighbor)?**
A: Finds approximate (not exact) nearest neighbors. Much faster than brute force for large datasets. Algorithms: HNSW, IVF, PQ. Example: HNSW searches 1M vectors in ~10ms, vs brute force ~100ms.

**Q5: What is FAISS?**
A: Open-source library by Facebook for efficient similarity search. Supports multiple index types (Flat, IVF, HNSW, PQ). Example: IndexIVFFlat with 100 centroids searches 1M vectors in 1ms with 90% recall.

**Q6: What is Pinecone?**
A: Managed vector database service (SaaS). Handles scaling, backups, updates automatically. Pay-per-use. Example: A startup uses Pinecone to avoid managing FAISS infrastructure. Starts free, scales to millions.

**Q7: What is ChromaDB?**
A: Open-source, lightweight vector DB. Great for prototyping and small-scale. Runs in-memory or persisted. Example: Data scientists use ChromaDB for local RAG experiments before moving to production.

**Q8: What is Weaviate?**
A: Open-source vector DB with built-in ML capabilities. Supports hybrid search (vector + keyword), CRUD, and multi-tenancy. Example: An e-commerce site uses Weaviate for product search with category filters.

**Q9: What is Milvus?**
A: Open-source vector DB designed for billion-scale similarity search. Distributed, cloud-native. Supports GPU acceleration. Example: A social media platform uses Milvus to search 10B images by similarity.

**Q10: How do you choose a vector database?**
A: Considerations: scale (millions/billions), latency (ms), managed vs self-hosted, cost, features (hybrid search, filtering), cloud/on-prem. Example: Prototyping → ChromaDB. Production < 1M vectors → Pinecone. 1B+ vectors → Milvus.

**Q11: What is HNSW?**
A: Hierarchical Navigable Small World — state-of-the-art ANN algorithm. Builds multi-layer graph. Search time grows logarithmically with data size. Example: HNSW with efConstruction=200, efSearch=50 gives 99% recall on 1M vectors.

**Q12: What is metadata filtering in vector search?**
A: Filtering search results by metadata before or after vector search. Post-filtering (search then filter) vs pre-filtering (filter then search). Example: Search "headphones" with metadata filter price < 100 only returns affordable results.
