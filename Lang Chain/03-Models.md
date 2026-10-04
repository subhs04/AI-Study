# Chapter 3 – Models

# Learning Objectives

After this chapter you should be able to:

- Understand what a Chat Model is.
- Know the difference between LLMs and Chat Models.
- Connect a model using LangChain.
- Configure common model parameters.
- Choose an appropriate model for a use case.

---

# 1. What is a Model?

A model is the component that generates text from a prompt.

LangChain **does not contain its own AI model**. It provides a common interface to communicate with many providers.

Examples:

- OpenAI
- Anthropic
- Google Gemini
- Ollama
- Azure OpenAI

Think of LangChain as a universal adapter.

---

# 2. LLM vs Chat Model

Traditional LLM

Input

```text
Explain Kubernetes
```

Output

```text
...
```

Chat Model

Input

- System Message
- Human Message
- Previous Conversation
- Tool Responses

Output

Assistant Response

Modern applications almost always use chat models.

---

# 3. Why LangChain Wraps Models

Without LangChain

```python
client.responses.create(...)
```

Changing providers usually requires learning another SDK.

With LangChain

```python
response = model.invoke(messages)
```

Only the model object changes while the rest of the application stays the same.

---

# 4. Creating a Chat Model

Example

```python
from langchain_openai import ChatOpenAI

model = ChatOpenAI(
    model="gpt-4.1",
    temperature=0
)
```

Invoking the model

```python
response = model.invoke("Explain vector databases.")
```

---

# 5. Model Parameters

## Temperature

Controls randomness.

Temperature = 0

- Predictable
- Deterministic
- Best for coding and factual tasks

Temperature = 1

- More creative
- More variation
- Better for brainstorming

---

## Max Tokens

Limits the response length.

Small value

- Lower cost
- Faster response

Large value

- Longer answers
- Higher cost

---

## Streaming

Instead of waiting for the complete response, tokens are returned as they are generated.

Benefits

- Better user experience
- Lower perceived latency

---

# 6. Execution Flow

User
    │
Prompt
    │
ChatPromptTemplate
    │
Chat Model
    │
LLM Provider
    │
Generated Response

---

# 7. Backend Analogy

Java

Interface

↓

Implementation

↓

Runtime Selection

LangChain

BaseChatModel

↓

ChatOpenAI / ChatAnthropic / ChatOllama

↓

Runtime Selection

Applications depend on the abstraction rather than a specific provider.

---

# 8. Choosing the Right Model

| Use Case | Typical Choice |
|-----------|----------------|
| Coding | Strong reasoning model |
| Customer Support | Fast, low-cost model |
| Summarization | Balanced model |
| Brainstorming | Higher temperature |
| Batch Processing | Cost-efficient model |

Always balance quality, latency and cost.

---

# 9. Best Practices

- Hide provider-specific code behind LangChain.
- Use lower temperatures for production APIs.
- Monitor token usage.
- Implement retries for transient failures.
- Avoid hard-coding model names throughout the codebase.

---

# 10. Common Mistakes

- Assuming larger models are always better.
- Ignoring response latency.
- Using high temperature for deterministic workflows.
- Forgetting token limits.
- Locking the application to one provider.

---

# Interview Questions

1. Does LangChain provide its own LLM?
2. Why use LangChain if providers already have SDKs?
3. Difference between an LLM and a chat model?
4. What does temperature control?
5. How would you switch from OpenAI to Anthropic with minimal code changes?

---

# Checkpoint Summary

- LangChain is model-agnostic.
- Chat models are the standard for modern AI applications.
- Temperature controls randomness.
- Streaming improves user experience.
- Build against abstractions, not provider SDKs.

---

# Mind Map

Models
├── Providers
│   ├── OpenAI
│   ├── Anthropic
│   ├── Gemini
│   └── Ollama
├── Temperature
├── Max Tokens
├── Streaming
└── invoke()

---

# 2-Minute Revision

Remember:

LangChain does not replace the model.

It standardizes communication with multiple providers.

The same application logic can work with different models by changing only the model implementation.
