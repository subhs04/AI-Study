# Chapter 7 – Retrieval

## Learning Objectives

After this chapter you should be able to:

- Explain why retrieval is needed in LLM applications.
- Understand the basic RAG architecture.
- Distinguish loading, splitting, embedding, indexing and retrieval.
- Understand retrievers and vector stores.
- Explain why retrieval quality matters.
- Understand common retrieval failure modes.

## 1. Why Retrieval Exists

An LLM often needs information that is private, recent, domain-specific, or unavailable in its training data.

Example:

> What is our company's reimbursement policy for international travel?

The model cannot reliably answer a private company policy question from its pretrained knowledge. The application must retrieve the relevant information and provide it as context.

This pattern is commonly called **Retrieval-Augmented Generation (RAG)**.

## 2. Basic RAG Architecture

```text
User Question
     ↓
Retriever
     ↓
Relevant Documents
     ↓
Prompt + Context
     ↓
LLM
     ↓
Answer
```

The core idea is:

```text
Retrieve first
Generate second
```

## 3. RAG Has Two Phases

### Ingestion

```text
Documents
   ↓
Load
   ↓
Split
   ↓
Embed
   ↓
Store
```

### Query

```text
Question
   ↓
Embed
   ↓
Search
   ↓
Top relevant chunks
   ↓
Prompt
   ↓
LLM
```

Keeping these phases separate is important. Ingestion is normally performed before users ask questions; query-time retrieval should be fast.

## 4. Document Loading

Documents may come from PDFs, websites, Markdown, CSV, databases, APIs or cloud storage.

A loader converts external content into documents that can be processed by the retrieval pipeline.

A document commonly contains:

```text
page_content
metadata
```

Example metadata:

```json
{
  "source": "employee-policy.pdf",
  "page": 14
}
```

Metadata is useful for filtering, debugging and citations.

## 5. Why Split Documents?

A 200-page document is usually too large and inefficient to send to an LLM for every question.

Instead, divide it into chunks:

```text
Large Document
       ↓
 ┌─────┬─────┬─────┬─────┐
 │ C1  │ C2  │ C3  │ C4  │
 └─────┴─────┴─────┴─────┘
```

The retriever can then return only the chunks likely to contain the answer.

## 6. Chunk Size and Overlap

There is no universal perfect chunk size.

Small chunks provide precision but may lose surrounding context. Large chunks preserve context but can reduce retrieval precision and consume more tokens.

Chunks can overlap:

```text
Chunk 1
----------------
A B C D E F

Chunk 2
        ----------------
        E F G H I J
```

Overlap reduces the chance that useful information is lost at a chunk boundary, but excessive overlap increases storage and processing cost.

## 7. Embeddings

An embedding converts text into a numerical vector representing semantic characteristics.

```text
"How do I claim travel expenses?"
              ↓
       Embedding Model
              ↓
[0.12, -0.44, 0.87, ...]
```

A query can then be compared with stored document vectors.

## 8. Vector Store

Embeddings and associated document chunks can be stored in a vector store/database.

Examples include FAISS, Chroma, Pinecone, Weaviate, Milvus and PostgreSQL with pgvector.

Conceptually:

```text
Chunk
 ↓
Embedding
 ↓
Vector Store
```

At query time:

```text
Question
 ↓
Embedding
 ↓
Vector Search
 ↓
Top K Chunks
```

## 9. Retriever

A retriever is an abstraction for obtaining relevant documents.

Conceptually:

```python
docs = retriever.invoke(question)
```

The application can work with the retriever abstraction without coupling every part of the application to a particular vector database.

A useful backend analogy is:

```java
List<Document> documents = documentRepository.findRelevant(query);
```

The retrieval semantics are different from an ordinary database query, but the abstraction boundary is similar.

## 10. Similarity Search

A basic vector retriever compares the query vector against stored vectors and ranks candidates.

Common similarity/distance approaches include cosine similarity, dot product and Euclidean distance.

The conceptual flow is:

```text
Query vector
      ↓
Compare with stored vectors
      ↓
Rank
      ↓
Return top K
```

## 11. Top K

If K = 4, the retriever returns four candidate chunks.

More is not automatically better.

Too few:

```text
Missing context
```

Too many:

```text
Noise + more tokens + potentially conflicting context
```

K should be evaluated against the actual application.

## 12. Retrieval Chain

A simple RAG pipeline is:

```text
Question
   ↓
Retriever
   ↓
Context
   ↓
Prompt
   ↓
Model
   ↓
Answer
```

Conceptually:

```python
docs = retriever.invoke(question)
context = "\n\n".join(doc.page_content for doc in docs)
response = model.invoke(
    prompt.format(question=question, context=context)
)
```

The exact LangChain APIs can vary, but the architecture remains the same.

## 13. Why Retrieval Quality Matters

A strong LLM cannot reliably answer from evidence that was never retrieved.

A useful engineering intuition is:

```text
Final answer quality
        ↓
depends on
        ↓
retrieval quality + generation quality
```

This is not a formal equation. It is a reminder that RAG problems are often retrieval problems rather than model problems.

## 14. Metadata Filtering

Suppose documents contain policies for India, USA and UK.

A query for an India policy can use metadata such as:

```text
country = India
```

Filtering can reduce irrelevant candidates before or alongside semantic retrieval.

## 15. Retrieval Problems

### Bad chunking

The answer may be split across chunks in an unhelpful way.

### Poor embedding model

The embedding model may not represent the domain or language well.

### Wrong K

Too few chunks can omit evidence; too many can introduce noise.

### Vocabulary mismatch

The user may ask:

```text
How much can I claim for a hotel?
```

while the document says:

```text
Accommodation reimbursement limits
```

Semantic retrieval can bridge this better than exact keyword matching.

## 16. Reranking

Initial retrieval can return a candidate set. A reranker can then reorder those candidates using a more expensive relevance model or strategy.

```text
Question
 ↓
Initial retrieval
 ↓
Candidate set
 ↓
Reranker
 ↓
Best context
 ↓
LLM
```

Reranking is useful when initial vector similarity is not sufficiently precise.

## 17. RAG Is More Than a Vector Database

A production RAG system may look like:

```text
Document ingestion
      ↓
Cleaning
      ↓
Chunking
      ↓
Embedding
      ↓
Indexing
      ↓
Retrieval
      ↓
Filtering / reranking
      ↓
Context construction
      ↓
LLM
      ↓
Citation / validation
```

A vector database is only one component.

## 18. Backend Analogy

Traditional backend:

```text
Request
 ↓
Repository
 ↓
Database
 ↓
Rows
 ↓
Service
```

RAG:

```text
Question
 ↓
Retriever
 ↓
Vector Store
 ↓
Relevant Documents
 ↓
Prompt
 ↓
LLM
```

## 19. Best Practices

- Keep ingestion and query pipelines separate.
- Store useful metadata.
- Evaluate chunking instead of choosing arbitrary values.
- Evaluate retrieval independently from generation.
- Do not blindly increase K.
- Consider filtering and reranking for difficult domains.
- Preserve source information for citations.
- Monitor retrieval failures.

## 20. Common Mistakes

- Treating RAG as simply putting documents into a vector database.
- Choosing chunk sizes without testing.
- Assuming the top result is always correct.
- Sending huge amounts of context to the LLM.
- Ignoring metadata.
- Evaluating only the final answer and never retrieval itself.

## 21. Interview Questions

### Beginner

1. What is RAG?
2. Why are documents chunked?
3. What is an embedding?
4. What is a vector database?
5. What is a retriever?

### Intermediate

6. Why use chunk overlap?
7. What does top K mean?
8. Difference between keyword and semantic search?
9. Why is metadata useful?
10. Why can increasing K make results worse?

### Advanced

11. How would you debug a poor RAG answer?
12. How would you evaluate retrieval separately from generation?
13. What is reranking?
14. How would you handle very long documents?
15. How would you design tenant-isolated document retrieval?

## 22. Checkpoint Summary

- RAG gives an LLM access to external knowledge.
- Ingestion and query are separate phases.
- Documents are loaded, split, embedded and indexed.
- A retriever finds relevant chunks.
- Retrieved context is passed to the model.
- Retrieval quality strongly influences final answer quality.
- A vector database is only one component of RAG.

## 23. Mind Map

```text
RAG
│
├── Ingestion
│   ├── Load
│   ├── Split
│   ├── Embed
│   └── Store
│
├── Query
│   ├── Embed
│   ├── Retrieve
│   ├── Filter
│   └── Rank
│
├── Generation
│   ├── Prompt
│   └── LLM
│
└── Quality
    ├── Chunking
    ├── Embeddings
    ├── K
    ├── Filtering
    └── Reranking
```

## 24. 2-Minute Revision

```text
Documents
 ↓
Chunks
 ↓
Embeddings
 ↓
Vector Store

Question
 ↓
Retriever
 ↓
Relevant Chunks
 ↓
Prompt
 ↓
LLM
 ↓
Answer
```

RAG means:

> Give the model the right external context before asking it to generate an answer.
