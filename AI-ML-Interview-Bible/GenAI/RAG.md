# RAG (Retrieval-Augmented Generation)

## What is it?
RAG is a technique that combines information retrieval with text generation. When a user asks a question, the system first searches a knowledge base (database, documents) for relevant information, then gives that information to the LLM as context to generate a grounded answer. Steps: Indexing (chunk + embed documents), Retrieval (search for relevant chunks), Generation (LLM answers with context).

## Why do we use it?
LLMs have knowledge cutoffs and can't access private data. RAG solves this without retraining. It gives LLMs access to updated information, internal documents, and reduces hallucinations. Companies use RAG for customer support (using product manuals), legal research (using case law databases), and medical Q&A (using clinical guidelines).

## Real-Time Example 1
**Customer Support for a Bank** — Step 1: Bank indexes all product documents (loan policies, fee schedules) into a vector database. Step 2: Customer asks "What are the fees for wire transfers?" Step 3: RAG retrieves 3 relevant chunks from the database (wire transfer fee document). Step 4: LLM generates "Domestic wire: $25, International wire: $45" using the retrieved context. Step 5: Response is accurate, current, and grounded in bank policy.

## Real-Time Example 2
**Medical Research Assistant** — A hospital uses RAG for doctors. Step 1: They index 10,000 medical research papers into a vector DB. Step 2: Doctor asks "What are recent treatments for drug-resistant TB?" Step 3: System retrieves 5 relevant papers from 2024. Step 4: LLM summarizes treatment options with citations. Step 5: Doctor gets a research-backed answer in seconds instead of hours.

## Concept Example 1
**Basic RAG Implementation**
```python
from openai import OpenAI
import chromadb

client = OpenAI()
chroma_client = chromadb.Client()
collection = chroma_client.create_collection("docs")

# Step 1: Index documents
documents = [
    "The Eiffel Tower is in Paris, France.",
    "The Great Wall of China is in China.",
    "The Statue of Liberty is in New York, USA."
]
for i, doc in enumerate(documents):
    embedding = client.embeddings.create(
        input=doc, model="text-embedding-ada-002"
    ).data[0].embedding
    collection.add(ids=[str(i)], embeddings=[embedding], documents=[doc])

# Step 2: Retrieve
query = "Where is the Eiffel Tower?"
query_embedding = client.embeddings.create(
    input=query, model="text-embedding-ada-002"
).data[0].embedding
results = collection.query(query_embeddings=[query_embedding], n_results=1)
context = results["documents"][0][0]

# Step 3: Generate
response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "Answer using only the provided context."},
        {"role": "user", "content": f"Context: {context}\n\nQuestion: {query}"}
    ]
)
print(response.choices[0].message.content)  # "The Eiffel Tower is in Paris, France."
```
**Input:** User query "Where is the Eiffel Tower?"
**Output:** "The Eiffel Tower is in Paris, France."
**Explanation:** The query is embedded, the most similar document is retrieved from the vector DB, and GPT-4 generates an answer using only that context. Without RAG, GPT-4 would answer from its training memory. With RAG, we control exactly what information is used.

## Concept Example 2
**Chunking Strategies**
```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

text = "..." * 1000  # Long document

# Strategy 1: Recursive character splitting
splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,     # Characters per chunk
    chunk_overlap=50,   # Overlap between chunks
    separators=["\n\n", "\n", ". ", " ", ""]  # Priority order
)
chunks = splitter.split_text(text)
print(f"Number of chunks: {len(chunks)}")
print(f"First chunk: {chunks[0][:100]}...")

# Strategy 2: Semantic chunking (concept-based)
# Use LLM to identify topic boundaries
# More accurate but slower and more expensive
```
**Input:** Long document (e.g., 10-page PDF)
**Output:** List of text chunks with overlap
**Explanation:** Chunking breaks documents into pieces for embedding. Too large → lose specificity. Too small → lose context. Overlap ensures no information is lost at boundaries. Recursive splitting tries separators in order (double newline first, then single newline, then period).

## Common Mistakes
- Chunking without overlap — losing context at boundaries.
- Using a single chunk for retrieval — need top-k for comprehensive answers.
- Not filtering retrieved chunks for relevance before sending to LLM.
- Ignoring document metadata (source, date) in retrieval.
- Over-relying on RAG without considering that retrieval can fail.
- Chunking purely by character count without respecting semantic boundaries.

## How Interviewers Ask This
- "What is RAG and why is it useful?"
- "How does the retrieval step work in RAG?"
- "What chunking strategies have you used?"
- "How does RAG reduce hallucinations?"
- "Compare RAG versus fine-tuning."

## How To Impress The Interviewer
- Explain the RAG triad: Indexing (chunk + embed), Retrieval (search + rank), Generation (LLM + context).
- Discuss advanced retrieval: hybrid search (dense + sparse), re-ranking (cross-encoder), query expansion.
- Mention RAGAS framework for evaluating RAG systems: faithfulness, answer relevance, context precision.
- Talk about the "lost in the middle" problem — LLMs focus on first and last chunks. Use re-ranking to put most relevant chunks first.
- Know about advanced RAG: self-RAG, CRAG, RAPTOR (tree-structured retrieval).

## Most Asked Questions

**Q1: What is RAG?**
A: Retrieval-Augmented Generation. Combines search + LLM. Retrieve relevant documents, then generate answer with context. Example: Customer asks "What's my remaining vacation?" RAG retrieves their HR record, LLM answers with exact balance.

**Q2: Why use RAG instead of fine-tuning?**
A: RAG provides up-to-date information without retraining. It's cheaper and more flexible. Fine-tuning is for learning new skills/styles. Example: A company's FAQ changes weekly — RAG updates instantly; fine-tuning would need daily retraining.

**Q3: What are the steps in a RAG pipeline?**
A: 1) Load documents, 2) Chunk documents, 3) Generate embeddings, 4) Store in vector DB, 5) Query → embed query, 6) Retrieve top-k chunks, 7) LLM generates answer with chunks as context.

**Q4: What are chunking strategies?**
A: Fixed size (simple, fast), Recursive (langchain default, semantic-aware), Document-based (by paragraphs/sections), Semantic (LLM detects topic changes). Example: 500 chars with 50 overlap works for general text.

**Q5: What is the optimal chunk size?**
A: Depends on use case. 200-1000 characters for Q&A, 1000-2000 for summarization. Embedding models have max input (e.g., ada-002: 8191 tokens). Test different sizes for your data.

**Q6: What is a vector database in RAG?**
A: Stores embeddings and enables similarity search. Examples: ChromaDB (local), Pinecone (managed), FAISS (Facebook, local), Weaviate (self-hosted). See Vector_Databases.md for details.

**Q7: What is hybrid search?**
A: Combines dense (semantic) and sparse (keyword) search. Dense captures meaning, sparse captures exact terms. Example: Searching "car" with hybrid finds "automobile" (dense) AND "car accident" (sparse).

**Q8: How many chunks should you retrieve?**
A: Typically 3-10. More chunks = more context = slower + more tokens. Test: 5 chunks works for most Q&A. Use re-ranker to select top 3 from 10 initial candidates.

**Q9: What is re-ranking in RAG?**
A: After initial retrieval (e.g., 20 chunks), a cross-encoder model re-scores them for relevance to the query. Then select top 3-5. Significantly improves accuracy. Example: Cohere Rerank, BGE-reranker.

**Q10: How does RAG reduce hallucinations?**
A: By providing factual context from trusted sources. The LLM generates answers based on retrieved text rather than relying on memory. Example: Medical RAG uses clinical guidelines; LLM cites source paragraph.

**Q11: What is query expansion in RAG?**
A: Generating multiple variations of the query to improve recall. Example: Original "hypertension treatment" → expanded to ["high blood pressure medication", "antihypertensive drugs", "managing hypertension"].

**Q12: What are the limitations of RAG?**
A: 1) Retrieval can fail (irrelevant chunks), 2) Latency (retrieval + generation), 3) LLM can ignore context, 4) Document quality issues. Mitigation: validate retrieval quality, use strong LLM, monitor with RAGAS.
