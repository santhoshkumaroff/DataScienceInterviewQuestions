# LangChain

## What is it?
LangChain is a framework for building LLM-powered applications. It provides modular components: Chains (sequence of steps), Prompts (templates), Memory (conversation history), Tools (APIs, databases), Agents (autonomous decision-makers), Document Loaders (read PDFs, web pages), Text Splitters (chunk documents), and Vector Stores (embedding storage). It's the most popular LLM framework.

## Why do we use it?
Building LLM apps from scratch requires glue code for prompts, API calls, memory management, tool integration, and response parsing. LangChain provides pre-built components and abstractions, reducing development time by 10x. Companies use it for RAG systems, chatbots, document analysis, and agent applications.

## Real-Time Example 1
**Customer Support Chatbot with Memory** — Step 1: Load company FAQ documents (Document Loader). Step 2: Split into chunks (Text Splitter). Step 3: Create embeddings and store in vector DB. Step 4: Set up a RetrievalQA chain. Step 5: Add ConversationBufferMemory to remember past chats. Step 6: User asks "What's your return policy?" Step 7: Chain retrieves relevant FAQ, LLM generates answer. Step 8: User follows up "Is that for all products?" Step 9: Memory provides context, LLM knows it's still about return policy.

## Real-Time Example 2
**Document Summarization Pipeline** — A legal firm processes 1000 contracts daily. Step 1: Load PDFs (PyPDFLoader). Step 2: Split into 2000-character chunks (RecursiveCharacterTextSplitter). Step 3: Map step — LLM summarizes each chunk. Step 4: Reduce step — LLM combines chunk summaries into final summary. Step 5: Output structured data (parties, dates, obligations) using PydanticOutputParser. Step 6: Store in database. All using LangChain's MapReduce chain.

## Concept Example 1
**Simple RAG Chain with LangChain**
```python
from langchain_community.document_loaders import TextLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.vectorstores import Chroma
from langchain_community.embeddings import OpenAIEmbeddings
from langchain.chains import RetrievalQA
from langchain_openai import ChatOpenAI

# 1. Load documents
loader = TextLoader("faq.txt")
documents = loader.load()

# 2. Split into chunks
splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=50)
chunks = splitter.split_documents(documents)

# 3. Embed and store
vectorstore = Chroma.from_documents(chunks, OpenAIEmbeddings())

# 4. Create QA chain
qa = RetrievalQA.from_chain_type(
    llm=ChatOpenAI(model="gpt-4"),
    chain_type="stuff",    # put all chunks into one prompt
    retriever=vectorstore.as_retriever(search_kwargs={"k": 3})
)

# 5. Query
result = qa.invoke("What is the return policy?")
print(result["result"])
```
**Input:** FAQ document, user question
**Output:** Answer grounded in the FAQ document
**Explanation:** LangChain connects all the steps: loading → splitting → embedding → storing → retrieving → generating. The "stuff" chain type puts all retrieved chunks into a single prompt. LangChain handles the glue code so you focus on your application logic.

## Concept Example 2
**Conversation Chain with Memory**
```python
from langchain.memory import ConversationBufferMemory
from langchain.chains import ConversationChain
from langchain_openai import ChatOpenAI

# Create memory
memory = ConversationBufferMemory()

# Create conversation chain
conversation = ConversationChain(
    llm=ChatOpenAI(model="gpt-4"),
    memory=memory,
    verbose=True
)

# First interaction
response1 = conversation.predict(input="Hi, I'm Alice")
print(response1)  # "Hello Alice! How can I help?"

# Second interaction (remembers name)
response2 = conversation.predict(input="What's my name?")
print(response2)  # "Your name is Alice!"

# Memory stores the entire conversation
print(memory.buffer)  # Shows full conversation history
```
**Input:** Sequential conversation turns
**Output:** Context-aware responses that remember past exchanges
**Explanation:** ConversationBufferMemory stores the entire chat history. On each call, LangChain adds the history to the prompt, allowing the LLM to reference previous turns. Other memory types: ConversationSummaryMemory (summarizes long histories) and VectorStoreMemory (retrieves relevant past exchanges).

## Common Mistakes
- Using chains when agents are needed (and vice versa) — chains are fixed, agents are flexible.
- Not managing token limits — long conversations exceed context windows.
- Using naive memory (BufferMemory) for long conversations — consumes too many tokens.
- Ignoring error handling — API calls fail, documents can't be loaded.
- Over-abstracting — sometimes raw API calls are simpler than LangChain wrappers.
- Not upgrading LangChain versions — breaking changes are common.

## How Interviewers Ask This
- "What is LangChain and why use it?"
- "Explain the difference between a chain and an agent in LangChain."
- "How does LangChain handle memory?"
- "What are document loaders and text splitters?"
- "What is the RetrievalQA chain?"

## How To Impress The Interviewer
- Explain LangChain's modular design: each component is swappable. Swap Chroma for Pinecone by changing one import.
- Discuss the different chain types: stuff (simple, short docs), map_reduce (parallel chunk processing), refine (iterative refinement), map_rerank (select best answer).
- Mention LangServe: deploy LangChain chains as REST APIs with auto-generated UI.
- Talk about LangSmith: LangChain's observability platform for tracing, debugging, and evaluating LLM apps.
- Know about LangGraph: LangChain's library for building stateful, multi-step agent workflows (graphs vs chains).

## Most Asked Questions

**Q1: What is LangChain?**
A: An open-source framework for building LLM applications. Provides pre-built components for prompts, chains, memory, agents, tools, document loading, and vector storage. Example: 50 lines of code for a RAG chatbot that would take 200+ lines without LangChain.

**Q2: What is a chain in LangChain?**
A: A sequence of steps where output of one step feeds into the next. Example: LLMChain (prompt → LLM → output parser). SequentialChain (chain1 → chain2). RetrievalQA (retrieve → prompt → LLM).

**Q3: What is the difference between chains and agents?**
A: Chains follow fixed sequences (deterministic). Agents decide dynamically which tools to call (autonomous). Example: A chain always does "retrieve → generate". An agent might "search → calculate → search again → generate" based on needs.

**Q4: What types of memory does LangChain support?**
A: BufferMemory (full history), SummaryMemory (summarized history), VectorStoreMemory (semantic retrieval of past), CombinedMemory (multiple types). Example: Use BufferMemory for short chats, SummaryMemory + VectorStoreMemory for long conversations.

**Q5: What are document loaders?**
A: Components that load documents from different sources. TextLoader (plain text), PyPDFLoader (PDFs), CSVLoader (CSV), WebBaseLoader (web pages), NotionLoader (Notion docs). Example: PyPDFLoader("contract.pdf") loads and extracts text from PDF.

**Q6: What are text splitters?**
A: Components that split documents into chunks for embedding. RecursiveCharacterTextSplitter (tries separators in order), CharacterTextSplitter (by character count), MarkdownHeaderSplitter (by headers). Example: Split 10-page PDF into 100 chunks of 500 chars with 50 overlap.

**Q7: What is a retriever in LangChain?**
A: Component that finds relevant documents for a query. VectorStoreRetriever (semantic search), BM25Retriever (keyword), MultiQueryRetriever (generates multiple query variations). Example: retriever.get_relevant_documents("return policy") returns 3 related chunks.

**Q8: What is the stuff chain type?**
A: Simplest chain — puts all retrieved documents into one prompt. Works for short documents. Example: 3 chunks of 500 chars each → single 1500-char context. Not suitable for long documents that exceed context window.

**Q9: What is the map_reduce chain type?**
A: Two steps: Map (summarize each chunk independently) → Reduce (combine summaries). Works for long documents. Example: 50 chunks → 50 summaries → 1 final summary. Parallelized for speed.

**Q10: What is LangSmith?**
A: LangChain's observability platform. Traces, debugs, and evaluates LLM application runs. Example: See exactly which documents were retrieved, which prompts were used, and how the LLM responded for any user query.

**Q11: What is LangGraph?**
A: LangChain's library for building stateful, multi-step agent workflows. Instead of simple chains, LangGraph uses directed graphs with branching and cycles. Example: An agent that can "think → act → observe → retry → decide" with state management.

**Q12: What are the criticisms of LangChain?**
A: 1) Over-abstraction (hides details), 2) Breaking changes between versions, 3) Debugging complexity, 4) Vendor lock-in feeling, 5) Sometimes simpler with raw API calls. Example: A simple API call with error handling might be 10 lines; LangChain equivalent might be 30 lines with hidden complexity.
