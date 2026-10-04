# Chapter 2 – Prompt Templates

# Learning Objectives

After completing this chapter you should be able to:

- Explain why prompt templates are needed.
- Create reusable prompt templates.
- Pass variables safely into prompts.
- Distinguish static prompts from dynamic prompts.
- Know common mistakes when designing prompts.

---

# 1. Why Prompt Templates Exist

The simplest way to use an LLM is to create a string.

```python
prompt = f"Summarize the following article:\n\n{text}"
```

This works for small programs but quickly becomes difficult to maintain.

Problems include:

- Prompt text scattered across the codebase.
- Repeated instructions copied into multiple files.
- Difficult to update prompts consistently.
- Increased risk of formatting mistakes.
- Hard to test different prompt versions.

A Prompt Template separates the **prompt structure** from the **data**.

Think of it as a parameterized SQL query instead of string concatenation.

---

# 2. Static vs Dynamic Prompts

Static Prompt

```text
Explain Kubernetes.
```

Dynamic Prompt

```text
Explain {technology} for a {audience}.
```

Example

```python
technology = "Kubernetes"
audience = "Java Developers"
```

Final Prompt

```text
Explain Kubernetes for Java Developers.
```

---

# 3. PromptTemplate

```python
from langchain_core.prompts import PromptTemplate

prompt = PromptTemplate.from_template(
    "Explain {topic} in simple terms."
)

formatted = prompt.invoke({"topic":"Vector Database"})
```

Execution Flow

Application
    │
Variables
    │
PromptTemplate
    │
Formatted Prompt
    │
LLM

---

# 4. ChatPromptTemplate

Modern chat models expect messages instead of a single string.

```python
from langchain_core.prompts import ChatPromptTemplate

prompt = ChatPromptTemplate.from_messages([
    ("system","You are a helpful AI tutor."),
    ("human","Explain {topic}")
])
```

Benefits

- Clear separation of roles.
- Better conversational context.
- Easier maintenance.

---

# 5. Message Roles

System
- Defines behaviour.

Human
- User request.

AI
- Previous assistant response.

Tool
- Response returned from a tool.

---

# 6. Partial Variables

Instead of supplying every variable repeatedly, some values can remain fixed.

Example

```python
language = "English"
```

Only the topic changes.

---

# 7. Prompt Design Guidelines

- Give clear instructions.
- Specify the desired output.
- Define the audience.
- Ask for structured output when needed.
- Keep prompts focused.

Poor

"Tell me about Java."

Better

"Explain Java collections for a backend developer with 3 years of experience. Include examples."

---

# 8. Backend Analogy

Spring MVC

Controller
↓
DTO
↓
Template Engine
↓
HTML

LangChain

Variables
↓
PromptTemplate
↓
Formatted Prompt
↓
LLM

PromptTemplate plays a role similar to a template engine.

---

# 9. Common Mistakes

- Embedding user data directly into prompt strings.
- Mixing instructions with application logic.
- Extremely long prompts.
- Vague objectives.
- Forgetting variable names.

---

# 10. Best Practices

✓ Store prompts separately from business logic.

✓ Reuse templates.

✓ Version important prompts.

✓ Test prompts independently.

✓ Prefer ChatPromptTemplate for chat models.

---

# Interview Questions

1. Why use PromptTemplate instead of f-strings?
2. Difference between PromptTemplate and ChatPromptTemplate?
3. What are system messages?
4. Why separate prompt and variables?
5. How would you maintain prompts in a large application?

---

# Checkpoint Summary

- Prompt Templates improve maintainability.
- Variables make prompts reusable.
- ChatPromptTemplate is preferred for chat models.
- Good prompt design is as important as model selection.

---

# Mind Map

Prompt Templates
├── Variables
├── PromptTemplate
├── ChatPromptTemplate
├── System Messages
├── Human Messages
├── Partial Variables
└── Best Practices

---

# 2-Minute Revision

Remember:

Prompt = Instructions + Variables

PromptTemplate = Reusable prompt

ChatPromptTemplate = Prompt designed for chat models

Separate prompt text from business logic.
