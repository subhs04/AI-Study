# Chapter 11 – Mini Projects

## Purpose

Use small projects to connect the concepts instead of memorizing APIs.

## Project 1 – Structured Support Classifier

Input a support message and return a validated structure containing category, priority and summary.

```text
Message → Prompt → Model → Structured Output → Validation
```

Concepts: prompt templates, models, structured output and Pydantic.

## Project 2 – Company Policy RAG Assistant

Load a small collection of policy documents and answer questions with source references.

```text
Documents → Load → Split → Embed → Vector Store
Question → Retrieve → Context → Prompt → Model → Answer
```

Experiment with different chunk sizes and top-K values and compare relevance and token usage.

## Project 3 – Tool-Using Customer Assistant

Expose controlled tools such as `get_customer`, `get_orders` and `get_balance`. Let an agent choose the required tools. Validate every argument.

## Project 4 – Research Assistant

Give an agent a document retriever, search capability and calculator. Observe the decide → act → observe loop.

## Project 5 – Production-Style AI API

Expose one of the previous workflows through an API. Add configuration, logging, request IDs, timeouts, error handling and basic metrics.

## Progression

```text
Structured Output → RAG → Tools/Agent → Multi-step Agent → Production API
```

## Design Questions

For every project ask:

1. Do I need an LLM?
2. Do I need retrieval?
3. Do I need tools?
4. Does the workflow really need an agent?
5. What must be validated?
6. What actions require authorization?

## Checkpoint Summary

The objective is to learn system design, not memorize LangChain syntax. Start with the simplest architecture and add capabilities only when the requirement demands them.

## Mind Map

```text
Projects
├─ Structured Output
├─ RAG
├─ Tools
├─ Agents
└─ Production API
```

## 2-Minute Revision

Build in this order:

```text
Prompt + Model
 → Structured Output
 → Retrieval
 → Tools
 → Agent
 → Production API
```
