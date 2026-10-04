# Chapter 6 – Chains and LCEL

## 1. Learning Objectives

After this chapter you should be able to:

- Explain why chains exist.
- Understand LangChain's Runnable abstraction.
- Read and write the `|` composition syntax.
- Understand `invoke`, `batch` and `stream`.
- Compose prompt, model and parser into a pipeline.
- Understand why LCEL is useful for production applications.
- Recognize when a chain is enough and when an agent is more appropriate.

---

## 2. Why Chains Exist

A useful LLM application rarely consists of one isolated model call.

A typical workflow is:

```text
Input
 ↓
Prompt Template
 ↓
Model
 ↓
Parser
 ↓
Application Result
```

Without an abstraction, developers manually connect every stage.

A chain provides a composable pipeline.

---

## 3. The Basic Chain

A common LangChain expression is:

```python
chain = prompt | model | parser
```

Read it left to right:

```text
Input
 ↓
prompt
 ↓
model
 ↓
parser
 ↓
output
```

Example:

```python
from langchain_core.output_parsers import StrOutputParser

chain = prompt | model | StrOutputParser()

result = chain.invoke({"topic": "Kafka"})
```

The important idea is **composition**.

---

## 4. What Is LCEL?

LCEL stands for **LangChain Expression Language**.

It provides a declarative way to compose LangChain Runnables.

Instead of writing orchestration code manually:

```python
formatted = prompt.invoke(data)
response = model.invoke(formatted)
result = parser.invoke(response)
```

you can express the same pipeline as:

```python
chain = prompt | model | parser
```

This is not merely shorter syntax. The components participate in a common execution model.

---

## 5. Runnable Mental Model

A Runnable is a component that can be executed with an input.

Conceptually:

```text
Runnable<Input, Output>
```

For example:

```text
Prompt
Input: dict
Output: messages
```

```text
Model
Input: messages
Output: AI message
```

```text
Parser
Input: AI message
Output: string
```

Therefore:

```text
Runnable A
     ↓
Runnable B
     ↓
Runnable C
```

can form a chain.

---

## 6. Type Compatibility

Think of composition as connecting compatible inputs and outputs.

```text
Prompt
  output → messages
             ↓
Model
  output → AIMessage
             ↓
Parser
  output → string
```

If two components are incompatible, the chain cannot work correctly.

This is similar to connecting typed methods in Java.

---

## 7. `invoke`

`invoke()` runs a chain once.

```python
result = chain.invoke({"topic": "RAG"})
```

Conceptually:

```text
Input
 ↓
Execute pipeline
 ↓
Output
```

Use it when you need one result.

---

## 8. `batch`

If you need to process multiple independent inputs, batching can be useful.

```python
results = chain.batch([
    {"topic": "Kafka"},
    {"topic": "Redis"},
    {"topic": "Kubernetes"}
])
```

Conceptually:

```text
Input 1 ─┐
Input 2 ─┼─→ Chain → Results
Input 3 ─┘
```

This can be more efficient than invoking the chain repeatedly, depending on the underlying model/provider and workload.

---

## 9. `stream`

Streaming allows output to be consumed incrementally.

Conceptually:

```text
Model generates
 ↓
chunk 1
chunk 2
chunk 3
chunk 4
...
```

Instead of waiting for the complete answer, the application can begin displaying or processing output earlier.

Typical use cases:

- Chat UI.
- Long responses.
- Interactive assistants.

---

## 10. Chain Execution Flow

```text
                    Input
                      │
                      ▼
              ┌──────────────┐
              │ Prompt       │
              │ Template     │
              └──────┬───────┘
                     │
                     ▼
              ┌──────────────┐
              │ Chat Model   │
              └──────┬───────┘
                     │
                     ▼
              ┌──────────────┐
              │ Output       │
              │ Parser       │
              └──────┬───────┘
                     │
                     ▼
                   Result
```

The pipe operator expresses this flow.

---

## 11. Why This Is Better Than One Giant Function

Bad architecture:

```python
def generate_answer(data):
    # format prompt
    # call model
    # parse response
    # retry
    # transform output
    # log everything
    # return result
```

Better:

```text
Prompt
 ↓
Model
 ↓
Parser
 ↓
Application Logic
```

Each component has a clearer responsibility.

Benefits:

- Reuse.
- Testing.
- Composition.
- Easier debugging.
- Easier replacement of components.

---

## 12. Backend Analogy

LCEL is similar to composing middleware or functional transformations.

Imagine:

```java
Request
  .map(validate)
  .map(transform)
  .map(callService)
  .map(toResponse);
```

Or a Spring pipeline:

```text
Controller
 ↓
Service
 ↓
Repository
 ↓
Mapper
```

The key idea is not the exact syntax. It is that independent components are composed into a predictable pipeline.

---

## 13. A Practical Example

Suppose we want to explain a technical concept for a junior developer.

Prompt:

```python
prompt = ChatPromptTemplate.from_template(
    "Explain {topic} for a junior backend developer."
)
```

Model:

```python
model = ChatOpenAI(model="...")
```

Parser:

```python
parser = StrOutputParser()
```

Chain:

```python
chain = prompt | model | parser
```

Invocation:

```python
answer = chain.invoke({"topic": "Kafka consumer groups"})
```

The entire application flow is represented by one composable object.

---

## 14. Adding Retrieval

Chains become more useful when several application stages are connected.

For example:

```text
Question
   ↓
Retriever
   ↓
Relevant Documents
   ↓
Prompt
   ↓
Model
   ↓
Parser
   ↓
Answer
```

This is the foundation of a RAG pipeline.

Conceptually:

```text
question → retrieve → format context → prompt → model → parse
```

---

## 15. Chain vs Agent

This distinction is important.

### Chain

The application defines the sequence.

```text
A → B → C → D
```

The workflow is predictable.

### Agent

The model can decide which action to take next.

```text
        ┌→ Tool A
LLM ────┼→ Tool B
        └→ Tool C
```

Use a chain when the workflow is known.

Use an agent when dynamic decision-making is genuinely required.

Do not turn every deterministic workflow into an agent.

---

## 16. Composition Beyond Simple Pipelines

LCEL supports more than simple sequential composition.

Conceptually, applications can build:

```text
Sequential
    A → B → C

Parallel
    ┌→ A ─┐
Input ───┼→ B ─┼→ Combined Output
    └→ C ─┘

Branching
    Input
      ↓
   condition
    ↙      ↘
  Path A   Path B
```

This makes LCEL useful for more than prompt → model → parser.

---

## 17. Configuration and Observability

Because components share a common Runnable model, execution can be configured and observed consistently.

Typical production concerns include:

- Tags.
- Metadata.
- Tracing.
- Runtime configuration.
- Retry handling.
- Timeouts.

The exact mechanism depends on the LangChain version and integrations being used, but the architectural principle is stable:

> Keep execution concerns separate from the business logic of each component.

---

## 18. Common Mistakes

### Mistake 1 – Using chains for everything

If the workflow is simply one model call, a chain may add unnecessary abstraction.

### Mistake 2 – Confusing a chain with an agent

A chain follows an application-defined workflow.

An agent dynamically selects actions.

### Mistake 3 – Ignoring input/output contracts

Every stage should have a clear expectation about what it receives and produces.

### Mistake 4 – Building huge chains

A chain should remain understandable. If one pipeline contains dozens of unrelated responsibilities, split it into meaningful components.

### Mistake 5 – Forgetting failure handling

Model calls can fail, parse operations can fail, and external tools can fail. Production chains need appropriate retries, timeouts and fallbacks.

---

## 19. Best Practices

- Keep each Runnable focused on one responsibility.
- Use explicit input/output contracts.
- Prefer composition over giant orchestration functions.
- Use `invoke` for one execution, `batch` for multiple independent inputs and `stream` when incremental output improves the experience.
- Keep deterministic business logic outside the model.
- Add observability to important production chains.
- Use agents only when dynamic planning is actually required.

---

## 20. Interview Questions

### Beginner

1. What is a chain?
2. What does the `|` operator mean in LangChain?
3. What is LCEL?
4. What does `invoke()` do?

### Intermediate

5. What is a Runnable?
6. Difference between `invoke`, `batch` and `stream`?
7. Why are chains composable?
8. How would you add a parser to a chain?

### Advanced

9. Difference between a chain and an agent?
10. How would you design a branching workflow?
11. How would you handle failures in a production chain?
12. How would you make a chain observable?
13. Why is a common Runnable abstraction useful for framework design?

---

## 21. Checkpoint Summary

- A chain composes multiple processing steps.
- LCEL provides a declarative way to compose Runnables.
- A Runnable accepts input and produces output.
- `prompt | model | parser` is the canonical mental model.
- `invoke` runs once.
- `batch` processes multiple inputs.
- `stream` exposes incremental output.
- Chains are predictable workflows.
- Agents introduce dynamic decision-making.

---

## 22. Mind Map

```text
LCEL / Chains
│
├── Runnable
│   ├── Prompt
│   ├── Model
│   ├── Parser
│   └── Retriever
│
├── Composition
│   ├── Sequential
│   ├── Parallel
│   └── Branching
│
├── Execution
│   ├── invoke
│   ├── batch
│   └── stream
│
└── Architecture
    ├── Predictable workflow → Chain
    └── Dynamic decision-making → Agent
```

---

## 23. 2-Minute Revision

Remember:

```text
Runnable = executable component

LCEL = composition language

A | B | C = output of A → input of B → input of C

invoke = one execution
batch = many inputs
stream = incremental output

Chain = predefined workflow
Agent = dynamically selected workflow
```

The core mental model:

> Build AI applications by composing small, compatible Runnables instead of putting the entire workflow into one function.
