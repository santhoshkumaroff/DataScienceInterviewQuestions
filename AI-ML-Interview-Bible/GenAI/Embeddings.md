# Embeddings

## What is it?
Embeddings are numerical vector representations of data (words, sentences, images) that capture meaning. Similar items have similar vectors. Word embeddings (Word2Vec, GloVe) represent individual words. Sentence embeddings (text-embedding-ada-002, SBERT) represent entire sentences. Cosine similarity measures how similar two embeddings are.

## Why do we use it?
Embeddings convert unstructured text into numbers that computers can process. They enable semantic search (finding meaning, not keywords), clustering (grouping similar documents), classification (spam detection), recommendation systems (finding similar products), and RAG (retrieving relevant context).

## Real-Time Example 1
**Job Candidate Matching** — LinkedIn uses embeddings. Step 1: All job descriptions are embedded into vectors. Step 2: All candidate profiles are embedded. Step 3: For a new job posting, find nearest neighbor candidates. Step 4: "Senior Python Developer" matches candidates with similar embeddings even if they wrote "Backend Engineer with 5 years Python". Step 5: Hiring manager sees matching candidates ranked by similarity.

## Real-Time Example 2
**Duplicate Question Detection** — Quora uses embeddings. Step 1: Users post "What is the capital of France?" Step 2: Check if similar question exists using embeddings. Step 3: Found "What's the capital city of France?" with 0.95 cosine similarity. Step 4: Route to existing answer instead of creating duplicate. Step 5: Users get instant answers, database stays clean.

## Concept Example 1
**Cosine Similarity with Embeddings**
```python
from openai import OpenAI
import numpy as np

client = OpenAI()

def get_embedding(text):
    response = client.embeddings.create(
        input=text, model="text-embedding-ada-002"
    )
    return response.data[0].embedding

def cosine_similarity(a, b):
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))

texts = [
    "I love programming in Python",
    "Python is my favorite coding language",
    "I enjoy eating pizza"
]

embeddings = [get_embedding(t) for t in texts]

print(f"Text 1 vs Text 2: {cosine_similarity(embeddings[0], embeddings[1]):.3f}")
print(f"Text 1 vs Text 3: {cosine_similarity(embeddings[0], embeddings[2]):.3f}")
```
**Input:** Three sentences about programming and pizza
**Output:** Text 1 vs Text 2: 0.92 (very similar), Text 1 vs Text 3: 0.72 (less similar)
**Explanation:** "I love programming in Python" and "Python is my favorite coding language" have high similarity (0.92) because they mean similar things. The pizza sentence is less related (0.72). Cosine similarity ranges from -1 to 1, where 1 = identical meaning.

## Concept Example 2
**Word Embeddings (Word2Vec)**
```python
from gensim.models import Word2Vec

# Training data
sentences = [
    ["king", "queen", "royal"],
    ["man", "woman", "human"],
    ["king", "man", "ruler"],
    ["queen", "woman", "ruler"]
]

# Train Word2Vec model
model = Word2Vec(sentences, vector_size=50, window=3, min_count=1)

# Find similar words
similar = model.wv.most_similar("king", topn=3)
print("Similar to king:", similar)
# Example: [("queen", 0.85), ("ruler", 0.78), ("royal", 0.72)]

# Analogy: king - man + woman = ?
result = model.wv.most_similar(
    positive=["king", "woman"],
    negative=["man"],
    topn=1
)
print("king - man + woman:", result)
# Example: [("queen", 0.78)]
```
**Input:** List of sentences for training Word2Vec
**Output:** "king - man + woman = queen"
**Explanation:** Word embeddings capture semantic relationships through vector arithmetic. The classic example: king (vector) - man (vector) + woman (vector) ≈ queen (vector). This shows embeddings encode gender relationships.

## Common Mistakes
- Using word embeddings (Word2Vec) for sentence-level tasks — use sentence transformers instead.
- Not normalizing vectors before cosine similarity — leads to incorrect results.
- Assuming higher dimensions are always better — ada-002 (1536) works better than 300-dim for many tasks.
- Using outdated embedding models (Word2Vec) when sentence transformers exist.
- Ignoring the embedding model's token limit — truncation loses meaning.

## How Interviewers Ask This
- "What are embeddings and how do they work?"
- "What is cosine similarity?"
- "Compare word embeddings vs sentence embeddings."
- "How is text-embedding-ada-002 different from Word2Vec?"
- "How do embeddings enable semantic search?"

## How To Impress The Interviewer
- Explain that embedding models are trained with contrastive learning — push similar pairs together, dissimilar apart.
- Discuss the embedding model zoo: ada-002 (OpenAI, 1536-dim), all-MiniLM-L6-v2 (SBERT, 384-dim, fast), BGE (BAAI, 1024-dim, best open-source), instructor-xl (uses instructions).
- Mention that ada-002 costs $0.13/1M tokens, all-MiniLM is free and runs locally.
- Talk about multi-modal embeddings: CLIP (text + images), Whisper (speech), Music2Vec (audio).
- Bring up the "curse of dimensionality" — very high dimensions can hurt similarity search quality.

## Most Asked Questions

**Q1: What are embeddings?**
A: Dense vector representations of data that capture semantic meaning. Similar items are close in vector space. Example: "dog" and "puppy" have similar embeddings; "dog" and "car" are far apart.

**Q2: What is the difference between word embeddings and sentence embeddings?**
A: Word embeddings (Word2Vec, GloVe) represent single words. Sentence embeddings (SBERT, ada-002) represent entire sentences. Sentence embeddings capture context. Example: "bank" as river bank vs financial bank — word embeddings give same vector; sentence embeddings differ based on context.

**Q3: What is cosine similarity?**
A: Measures the cosine of the angle between two vectors. Range -1 to 1. 1 = same direction (similar), 0 = perpendicular (unrelated), -1 = opposite (dissimilar). Formula: cos(θ) = A·B / (||A|| × ||B||).

**Q4: What is text-embedding-ada-002?**
A: OpenAI's embedding model. Creates 1536-dimensional vectors. Handles up to 8191 tokens. Cost: $0.13/1M tokens. Best for semantic search, classification, clustering. State-of-the-art for general use.

**Q5: How do you choose an embedding model?**
A: Considerations: cost (free vs paid), dimension (affects search speed), quality (MTEB benchmark), language support, max tokens, latency. Example: all-MiniLM-L6-v2 (384-dim, free, fast) for prototyping; ada-002 for production.

**Q6: What is the MTEB benchmark?**
A: Massive Text Embedding Benchmark. Tests embedding models on 8 tasks: classification, clustering, pair classification, re-ranking, retrieval, STS, summarization, and matching. Example: BGE-large-en scores 64.23, ada-002 scores 61.0.

**Q7: How do you train custom embeddings?**
A: 1) Gather pairs of similar/dissimilar texts, 2) Use contrastive loss (push similar together, pull dissimilar apart), 3) Fine-tune a pre-trained model (SBERT). Example: Fine-tune all-MiniLM on customer queries for domain-specific search.

**Q8: What is the length limit for embedding models?**
A: Most models have max input tokens. ada-002: 8191 tokens. all-MiniLM: 256 tokens. Beyond this, text is truncated. Solution: chunk long texts before embedding. Example: Split 10k token document into 250-token chunks with overlap.

**Q9: What is the curse of dimensionality in embeddings?**
A: In high dimensions, all vectors become equally far apart, making similarity search meaningless. Mitigation: use reasonable dimensions (384-1536), normalize vectors. Example: 4096-dim embeddings don't perform better than 1536-dim.

**Q10: Can embeddings capture negation?**
A: Poorly. "I like this movie" and "I don't like this movie" may have similar embeddings because they share most words. Solution: use models trained on NLI (natural language inference) data or use instruction-tuned embeddings like instructor-xl.

**Q11: What is the difference between sparse and dense embeddings?**
A: Sparse (TF-IDF, BM25) have mostly zeros, representing exact word matches. Dense (ada-002, SBERT) are mostly non-zero, capturing semantic meaning. Hybrid search combines both for best results.

**Q12: How do you cache embeddings?**
A: Store computed embeddings in a database to avoid recomputation. Example: Compute embedding for each product once, store in vector DB. New products compute on insertion. Queries use cached embeddings for fast search.
