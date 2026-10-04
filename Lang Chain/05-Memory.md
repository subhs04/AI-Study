# Chapter 5 – Memory

## 1. Learning Objectives

After this chapter you should be able to:

- Explain why LLM applications need memory.
- Distinguish model context from application memory.
- Understand conversation history and message history.
- Distinguish short-term conversation state from long-term memory.
- Understand how memory fits into a LangChain application.
- Design memory without blindly sending an entire conversation on every request.

---

## 2. Why Memory Exists

An LLM call is fundamentally stateless unless the application supplies previous information again.

For example:

```text
User: My name is Subhs.
Assistant: Nice to meet you.

User: What is my name?
```

If the second request is sent to the model by itself, the model has no guaranteed access to the first exchange.

The application therefore needs to provide the relevant history:

```text
Current request
      +
Previous relevant messages
      ↓
    Model
```

Memory is therefore primarily an **application concern**, not magical permanent knowledge inside the LLM.

---

## 3. The Most Important Mental Model

Think of the model as a function:

```text
response = model(context)
```

If previous conversation is required, the application constructs the context:

```text
context = system instructions
         + conversation history
         + current request
```

Then:

```text
context → model → response
```

This is the foundation of LLM memory.

---

## 4. Conversation History

A simple chat application can maintain a list of messages:

```python
messages = [
    ("system", "You are a helpful assistant."),
    ("human", "My name is Subhs."),
    ("ai", "Nice to meet you, Subhs."),
    ("human", "What is my name?")
]
```

The model receives the relevant messages together.

The important point is:

> The model does not inherently remember the conversation. The application provides conversation state as context.

---

## 5. Message History

LangChain applications commonly represent conversation history as messages rather than manually concatenated strings.

Conceptually:

```text
HumanMessage
AIMessage
HumanMessage
AIMessage
...
```

This is better than creating one huge string because message roles remain explicit.

```text
System
  ↓
Human
  ↓
AI
  ↓
Human
  ↓
AI
```

---

## 6. A Simple Stateful Flow

```text
                ┌──────────────────┐
User ──────────►│ Application       │
                └────────┬─────────┘
                         │
                  Load conversation
                         │
                         ▼
                ┌──────────────────┐
                │ Prompt + History │
                └────────┬─────────┘
                         │
                         ▼
                    Chat Model
                         │
                         ▼
                     Response
                         │
                         ▼
                  Store new messages
```

This is the basic architecture behind conversational memory.

---

## 7. Short-Term Memory

Short-term memory means state needed during an ongoing interaction or conversation.

Examples:

- Previous messages in a chat.
- Current agent state.
- Intermediate results needed during one workflow.
- Information needed for the next few turns.

Typical flow:

```text
Conversation
    ↓
Message History / State
    ↓
Current Invocation
```

In modern LangChain/LangGraph applications, short-term conversation state is commonly associated with the execution/thread state of the application rather than an old-style global `ConversationBufferMemory` object.

---

## 8. Long-Term Memory

Long-term memory is information intended to survive beyond one conversation or session.

Examples:

```text
User preference: prefers concise explanations
Customer preference: monthly billing
Profile: software engineer
```

This information usually belongs in an external store such as:

- SQL database
- Document store
- Key-value store
- Vector store, where semantic retrieval is appropriate

The model does not become permanently trained on the information.

Instead:

```text
Long-Term Store
      ↓
Retrieve relevant facts
      ↓
Add to current context
      ↓
LLM
```

---

## 9. Short-Term vs Long-Term

| Aspect | Short-Term | Long-Term |
|---|---|---|
| Scope | Current conversation/workflow | Across sessions |
| Example | Previous chat messages | User preferences |
| Lifetime | Limited | Persistent |
| Typical storage | Thread/session state | Database/store |
| Main purpose | Maintain current context | Remember durable information |

Do not confuse conversation history with permanent user memory.

---

## 10. Why Sending the Entire Conversation Is Not a Good Strategy

A naive implementation can send every historical message on every request.

```text
Turn 1 → 2 messages
Turn 10 → 20 messages
Turn 100 → 200 messages
Turn 1000 → huge context
```

Problems:

- Token cost increases.
- Latency increases.
- Context window can eventually be exceeded.
- Irrelevant information can distract the model.

Therefore memory design is also a **context-management problem**.

---

## 11. Summarization

Instead of keeping every historical message, the application can summarize older conversation.

```text
Recent messages
      +
Summary of older conversation
      ↓
Current context
```

Example:

```text
Original history:
100 messages

Compressed state:
"User is building a Spring Boot backend, prefers Java examples,
and is currently designing a RAG service."
```

This reduces context size while preserving important information.

But summarization can lose details, so it should not be treated as a perfect replacement for original history.

---

## 12. Retrieval-Based Memory

For long-term information, retrieve only what is relevant.

```text
User asks question
       ↓
Search memory store
       ↓
Relevant memories
       ↓
Prompt
       ↓
LLM
```

This is conceptually similar to RAG.

The important distinction is the source of the retrieved information:

```text
RAG → external knowledge/documents
Memory retrieval → information about prior user/application state
```

The implementation techniques can overlap.

---

## 13. Backend Analogy

Think about a normal backend request.

```text
HTTP Request
   ↓
Session / Authentication
   ↓
Load State
   ↓
Service
   ↓
Update State
```

An LLM application is similar:

```text
User Message
   ↓
Identify Conversation / Thread
   ↓
Load State
   ↓
Build Context
   ↓
LLM
   ↓
Persist Updated State
```

Memory is therefore closer to **state management** than to a special LLM capability.

---

## 14. Memory and Agents

Agents make memory more important because an agent may execute multiple steps.

```text
User
 ↓
Agent
 ↓
Reason / Tool Call
 ↓
Observation
 ↓
Next Step
 ↓
Final Response
```

The agent needs state so it knows what has already happened.

For example:

```text
Task: Find delayed orders

Step 1 → query database
Step 2 → inspect results
Step 3 → call shipping API
Step 4 → produce response
```

The intermediate state must survive across those steps.

---

## 15. What Memory Should NOT Become

Do not blindly store everything.

A memory system that stores every user message forever creates problems:

- Privacy risk.
- Storage growth.
- Retrieval noise.
- Outdated information.
- Conflicting facts.

A good memory system asks:

```text
Is this information useful later?
Is it safe to retain?
How long should it live?
How will it be retrieved?
How can it be corrected or deleted?
```

---

## 16. Common Mistakes

### Mistake 1 – Thinking the model remembers

The model only sees what is included in the request/context available to it.

### Mistake 2 – Sending unlimited history

This eventually creates cost, latency and context-window problems.

### Mistake 3 – Treating every message as durable memory

Most conversation content does not need to survive forever.

### Mistake 4 – Using vector search for everything

Not every memory is a semantic-search problem. Structured user preferences may belong in a normal database.

### Mistake 5 – Mixing memory with business state

Order status, payment state and authorization should remain authoritative in business systems, not in an LLM memory store.

---

## 17. Best Practices

- Define what information should be remembered.
- Separate short-term conversation state from long-term memory.
- Keep authoritative business state in the source-of-truth database.
- Retrieve only relevant long-term information.
- Summarize or prune large conversations.
- Consider privacy, retention and deletion requirements.
- Monitor token usage caused by history.
- Treat retrieved memories as context, not unquestionable truth.

---

## 18. Interview Questions

### Beginner

1. Why does an LLM application need memory?
2. Does an LLM automatically remember previous requests?
3. What is conversation history?

### Intermediate

4. Difference between short-term and long-term memory?
5. Why can sending the entire chat history become expensive?
6. How can conversation history be compressed?
7. When would you use a database instead of a vector store for memory?

### Advanced

8. How would you design memory for a multi-user chatbot?
9. How would you prevent stale memories from overriding current facts?
10. How would you handle memory deletion and privacy requirements?
11. How does agent state differ from user memory?
12. Why should business state not be stored only in LLM memory?

---

## 19. Checkpoint Summary

- LLM calls are fundamentally stateless.
- Memory is application-managed context/state.
- Conversation history provides short-term context.
- Long-term memory persists selected information across sessions.
- Sending all history forever is expensive and eventually fails.
- Summarization and retrieval help control context size.
- Memory is not a replacement for authoritative business databases.
- Agent workflows also need state to track intermediate steps.

---

## 20. Mind Map

```text
Memory
│
├── Short-Term
│   ├── Conversation history
│   ├── Thread state
│   └── Agent state
│
├── Long-Term
│   ├── User preferences
│   ├── Profile facts
│   └── Persistent application memory
│
├── Context Management
│   ├── Pruning
│   ├── Summarization
│   └── Retrieval
│
└── Production
    ├── Privacy
    ├── Retention
    ├── Cost
    ├── Relevance
    └── Source of truth
```

---

## 21. 2-Minute Revision

Remember:

```text
LLM = stateless computation

Memory = application-managed state/context

Short-term = current conversation/workflow

Long-term = selected information that survives sessions

Large history → summarize/prune/retrieve

Business truth → authoritative backend/database
```

The core idea is simple:

> Memory does not make the model permanently remember. Memory makes the application selectively provide the right past information at the right time.
