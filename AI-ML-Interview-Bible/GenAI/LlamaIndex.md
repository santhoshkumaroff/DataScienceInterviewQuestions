# LlamaIndex

## What is it?
LlamaIndex (formerly GPT Index) is a data framework for LLM applications focused on data ingestion, indexing, and retrieval. It specializes in connecting LLMs to your data with advanced indexing strategies. Key features: Data connectors (ingest from 100+ sources), Indexing (vector, summary, keyword, tree), Query Engines (ask questions), Chat Engines (multi-turn conversations), and Document Management (update/delete).

## Why do we use it?
While LangChain is general-purpose, LlamaIndex is specialized for data-to-LLM pipelines. It provides more sophisticated indexing strategies (beyond simple chunking), better data connectors, and optimized query engines. Companies use it for enterprise RAG, document Q&A, data analysis, and knowledge base applications.

## Real-Time Example 1
**Enterprise Knowledge Base** — A company with 10,000 internal documents. Step 1: Use LlamaIndex readers to load PDFs, Confluence pages, Notion docs. Step 2: Build a vector index (embeddings + metadata). Step 3: Add a summary index (document-level summaries). Step 4: Query engine retrieves relevant documents AND their summaries. Step 5: User asks "What's our data retention policy?" Step 6: Engine searches vector index for relevant chunks, retrieves summary of the policy document. Step 7: LLM generates answer with citations. Better accuracy than naive RAG.

## Real-Time Example 2
**Financial Report Analysis** — An investment firm has 5000 quarterly reports. Step 1: LlamaIndex parses PDFs (including tables). Step 2: Builds a tree index (hierarchical: sections → paragraphs → sentences). Step 3: Adds keyword table index for financial terms. Step 4: Analyst asks "What was Apple's Q3 revenue in 2024?" Step 5: Engine uses keyword index to find relevant reports, tree index to navigate to the exact section. Step 6: Returns "$85.8B" with source citation. Step 7: Follow-up "How does that compare to Q2?" — chat engine uses conversation memory.

## Concept Example 1
**LlamaIndex RAG with Different Index Types**
```python
from llama_index.core import VectorStoreIndex, SummaryIndex, SimpleDirectoryReader
from llama_index.core import Settings
from llama_index.llms.openai import OpenAI

# Load documents
documents = SimpleDirectoryReader("./data").load_data()

# Option 1: Vector Index (semantic search)
vector_index = VectorStoreIndex.from_documents(documents)
vector_engine = vector_index.as_query_engine()
response = vector_engine.query("What is the return policy?")
print("Vector:", response)

# Option 2: Summary Index (document-level summaries)
summary_index = SummaryIndex.from_documents(documents)
summary_engine = summary_index.as_query_engine()
response = summary_engine.query("Summarize all documents")
print("Summary:", response)

# Option 3: Keyword Table Index (exact term matching)
from llama_index.core import KeywordTableIndex
keyword_index = KeywordTableIndex.from_documents(documents)
keyword_engine = keyword_index.as_query_engine()
response = keyword_engine.query("Return policy refund period")
print("Keyword:", response)
```
**Input:** Directory of documents, user queries
**Output:** Contextual answers using different index strategies
**Explanation:** LlamaIndex supports multiple index types for different retrieval needs. Vector index for semantic search. Summary index for document-level overviews. Keyword index for exact-term matching. You can combine them using a ComposableGraph for best results.

## Concept Example 2
**Query Engine with Custom Retrieval**
```python
from llama_index.core import VectorStoreIndex, SimpleDirectoryReader
from llama_index.core.retrievers import VectorIndexRetriever
from llama_index.core.query_engine import RetrieverQueryEngine
from llama_index.core.postprocessor import SimilarityPostprocessor

# Load and index
documents = SimpleDirectoryReader("./data").load_data()
index = VectorStoreIndex.from_documents(documents)

# Custom retriever (top 5 chunks)
retriever = VectorIndexRetriever(
    index=index,
    similarity_top_k=5
)

# Post-processor (filter low similarity)
postprocessor = SimilarityPostprocessor(similarity_cutoff=0.7)

# Custom query engine
engine = RetrieverQueryEngine(
    retriever=retriever,
    node_postprocessors=[postprocessor],
    response_mode="compact"  # fit as much context as possible
)

response = engine.query("What are the shipping options?")
print(response)
print("Sources:", response.source_nodes)  # which documents were used
```
**Input:** Query about shipping options
**Output:** Answer with source citations
**Explanation:** LlamaIndex gives fine-grained control over retrieval. Custom retriever selects top 5 chunks. Post-processor filters out chunks below 0.7 similarity. response_mode="compact" packs as many chunks as possible within the context window. The response includes source nodes for citation.

## Common Mistakes
- Using vector index for everything — other indices (tree, keyword, summary) are better for specific use cases.
- Not setting proper chunk sizes — too small = lost context, too large = poor retrieval.
- Ignoring metadata (dates, authors, categories) — useful for filtering.
- Not using response modes like "compact" or "tree_summarize" for long documents.
- Forgetting to update index when source documents change.

## How Interviewers Ask This
- "What is LlamaIndex and how is it different from LangChain?"
- "What types of indexes does LlamaIndex support?"
- "How does the query engine work in LlamaIndex?"
- "What are chat engines and how do they differ from query engines?"
- "How does LlamaIndex handle document updates?"

## How To Impress The Interviewer
- Explain LlamaIndex's data hub (LlamaHub): 100+ data connectors for Google Drive, Notion, Slack, SQL, PDF, etc. One line of code to load data from any source.
- Discuss advanced indexing: RecursiveRetriever (nested index), RouterQueryEngine (routes query to best index), SubQuestionQueryEngine (decomposes complex questions).
- Mention response modes: "tree_summarize" (build answer recursively), "compact" (pack chunks), "refine" (iteratively refine), "accumulate" (multi-part answers).
- Talk about the differences: LangChain is "Swiss Army knife" for LLM apps; LlamaIndex is "data specialist" for RAG.
- Know that LlamaIndex integrates with LangChain (can use LangChain's tools and agents within LlamaIndex).

## Most Asked Questions

**Q1: What is LlamaIndex?**
A: A data framework for LLM applications focused on data ingestion, indexing, and retrieval. Specializes in connecting LLMs to your data with advanced indexing. Example: Load PDFs, Confluence pages, and SQL databases → unified index → query with natural language.

**Q2: How is LlamaIndex different from LangChain?**
A: LlamaIndex is data-centric (indexing, retrieval, data connectors). LangChain is general-purpose (chains, agents, tools). They overlap but complement each other. Example: Use LlamaIndex for data ingestion + retrieval, LangChain for agent orchestration.

**Q3: What types of indexes does LlamaIndex support?**
A: VectorStoreIndex (semantic), SummaryIndex (document summaries), KeywordTableIndex (keyword search), TreeIndex (hierarchical), KnowledgeGraphIndex (entity relationships). Example: Use TreeIndex for long documents, VectorIndex for Q&A.

**Q4: What is a query engine in LlamaIndex?**
A: Component that accepts a query and returns a response. It combines retriever + post-processor + response synthesizer. Example: engine.query("What is the revenue?") → retrieves chunks → filters → generates answer.

**Q5: What is a chat engine in LlamaIndex?**
A: Query engine with conversation memory. Supports multi-turn conversations. Modes: "condense_question" (rephrase with history), "simple" (full history), "context" (retrieve + memory). Example: User asks "What's the revenue?" then "How about profit?" — second query knows context.

**Q6: What is LlamaHub?**
A: A repository of 100+ data connectors (readers) for LlamaIndex. Load data from Google Drive, Notion, Slack, SQL, PDF, S3, Wikipedia, YouTube, etc. Example: SimpleDirectoryReader("data") automatically detects file types.

**Q7: How does LlamaIndex handle document updates?**
A: Supports insert (add new documents), delete (remove), update (replace). The index is updated incrementally (no need to rebuild). Example: Company adds new policy → insert into index → queries immediately return updated results.

**Q8: What are response modes in LlamaIndex?**
A: How the query engine constructs the final answer: "compact" (pack chunks into prompt), "refine" (iterate through chunks), "tree_summarize" (recursive summarization), "accumulate" (separate answers per chunk). Example: "tree_summarize" for long document Q&A.

**Q9: What is a node in LlamaIndex?**
A: A chunk of text with metadata (source, position, relationships). Nodes are the atomic unit of indexing. Example: A 500-char chunk from page 3 of a PDF is a Node with metadata {"file": "policy.pdf", "page": 3}.

**Q10: How does LlamaIndex handle structured data?**
A: Supports SQL databases. Creates a "table index" that retrieves relevant table schemas and generates SQL queries. Example: "What was total revenue in 2024?" → index finds sales table → LLM generates SQL query → returns result.

**Q11: What is the RouterQueryEngine?**
A: Routes queries to the best index based on query content. Example: "Summarize Q3 report" → routes to summary index. "What's in section 4.2?" → routes to tree index. "Revenue" → routes to vector index.

**Q12: Can LlamaIndex integrate with LangChain?**
A: Yes. LlamaIndex provides a LangChain-compatible wrapper. You can use LlamaIndex retrievers as LangChain tools, or use LangChain agents with LlamaIndex query engines. Example: LangChain agent uses LlamaIndex retriever to answer data questions, then calls other tools.
