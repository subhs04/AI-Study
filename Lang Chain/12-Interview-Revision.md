# Chapter 12 – Interview Revision

## 1. Core Mental Model

```text
Prompt → Model → Structured Output → optional Retrieval → optional Tools → optional Agent → Application
```

Not every system needs every component.

## 2. LangChain

LangChain is an orchestration framework around language models. It is not itself an LLM.

## 3. Prompt Templates

Prompt templates separate reusable instructions from runtime variables. `ChatPromptTemplate` represents structured chat messages.

## 4. Models

LangChain provides abstractions for model providers. Temperature affects output variability; model selection should consider quality, cost and latency.

## 5. Structured Output

Applications need predictable data. Use schemas and validation rather than substring matching or blindly trusting JSON.

```text
LLM → Schema → Validation → Business Logic
```

## 6. Memory

Conversation history is application-managed context. Short-term state supports a conversation/workflow; long-term memory stores selected information across sessions.

## 7. LCEL

LCEL composes Runnables:

```python
chain = prompt | model | parser
```

`invoke` runs once, `batch` processes multiple inputs and `stream` exposes incremental output.

## 8. Retrieval

RAG retrieves external context before generation.

```text
Documents → Split → Embed → Store
Question → Retrieve → Context → Model
```

An embedding is a numerical representation useful for semantic comparison. A vector store is only one component of RAG.

## 9. Agents

A chain follows a developer-defined workflow. An agent dynamically chooses actions.

```text
Decide → Act → Observe → Decide → ... → Finish
```

Use agents when dynamic decision-making is genuinely required.

## 10. Tools

A tool is a controlled capability exposed to a model/agent. It has a name, description, input schema and implementation. The model requests the tool; the application validates, authorizes and executes it.

## 11. Security

Never let model output directly authorize a consequential action. Treat user input, retrieved content, tool results and model output as untrusted.

## 12. Production

Major concerns:

```text
Reliability | Security | Observability | Evaluation | Cost | Latency
```

## 13. Rapid-Fire Questions

1. What is LangChain?
2. What is LCEL?
3. What is a Runnable?
4. PromptTemplate vs ChatPromptTemplate?
5. What is structured output?
6. Why validate LLM output?
7. What is memory?
8. What is RAG?
9. What is an embedding?
10. What is a vector store?
11. What is a retriever?
12. What is a tool?
13. What is tool calling?
14. What is an agent?
15. Chain vs agent?
16. Can an agent use RAG?
17. Where should authorization happen?
18. How do you prevent agent loops?
19. How do you control LLM cost?
20. How do you evaluate a RAG system?

## 14. Architecture Mind Map

```text
AI Application
├─ Prompt
├─ Model
├─ Structured Output
├─ State
├─ Retrieval
│  ├─ Embeddings
│  ├─ Vector Store
│  └─ Retriever
├─ Tools
│  ├─ Schema
│  ├─ Validation
│  └─ Execution
├─ Agent
│  ├─ Decision
│  ├─ State
│  └─ Loop
└─ Production
   ├─ Security
   ├─ Observability
   └─ Evaluation
```

## 15. Decision Tree

```text
Need an LLM?
├─ No → conventional software
└─ Yes
   ├─ Fixed workflow → chain / deterministic workflow
   └─ Dynamic workflow → consider agent

Need external knowledge? → Retrieval/RAG
Need external actions? → Tools
```

## 16. Final Checkpoint

The progression is:

```text
LLM → Prompt → Model → Structured Output → Chain → Retrieval → Tools → Agent → Production
```

Each layer solves a different problem. Do not add a layer unless the problem requires it.

## 17. 2-Minute Revision

```text
Chain = predefined workflow
Agent = dynamically selected workflow
RAG = external knowledge
Tool = external capability
Memory = application-managed state
Structured output = predictable application data
LCEL = composable Runnable pipelines
```

Core principle:

> Use the simplest architecture that reliably solves the problem.
