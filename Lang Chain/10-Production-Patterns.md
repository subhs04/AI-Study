# Chapter 10 – Production Patterns

## Learning Objectives

Understand what changes when an LLM prototype becomes a production service.

## 1. Prototype vs Production

Prototype: `Prompt → LLM → Answer`. Production adds API boundaries, validation, observability, security, evaluation, cost and reliability controls.

## 2. Architecture

```text
Client → API → Application Service → AI Workflow
                                      ├─ Prompt
                                      ├─ Model
                                      ├─ Retriever
                                      └─ Tools
                                              ↓
                                      Validation → Response
```

Keep business rules outside prompts and agents.

## 3. Configuration

Centralize model names, credentials, temperature, timeouts, retry limits and token limits. Never commit API keys.

## 4. Reliability

LLM calls can fail because of network errors, provider outages, rate limits, timeouts and malformed output. Use bounded retries, timeouts and safe fallbacks. Do not retry permanent failures blindly.

## 5. Structured Output

Use schemas for important application decisions:

```text
LLM → Structured Output → Validation → Business Logic
```

## 6. Observability

Capture request IDs, model/version, latency, token usage, retrieval results, tool calls and failures. Redact sensitive information. Tracing is especially important for multi-step agents.

## 7. Cost and Latency

Cost depends on input tokens, output tokens, model pricing and number of calls. Agents can multiply calls. Reduce unnecessary context, use appropriate models, cache where safe, batch independent work and limit agent iterations.

## 8. Security

Treat user input, retrieved documents, tool results and model output as untrusted. Defend against prompt injection, data leakage and unauthorized tool use. Authorization belongs in the application, not in the prompt.

## 9. Evaluation

Evaluate retrieval, generation, tool selection and system metrics separately. A plausible answer is not proof that the system is correct.

## 10. Human-in-the-Loop

For consequential actions use:

```text
Agent → Proposed Action → Human Approval → Execution
```

## Best Practices

- Separate AI orchestration from domain logic.
- Set timeouts and iteration limits.
- Validate structured output and tool arguments.
- Trace important executions.
- Protect secrets and sensitive data.
- Version important prompts.
- Monitor cost, latency and failures.

## Common Mistakes

- Treating LangChain as the whole backend.
- Unlimited agent loops.
- Logging sensitive prompts.
- Trusting retrieved content as instructions.
- Putting authorization in prompts.
- Using the most expensive model everywhere.

## Interview Questions

1. What changes between an LLM prototype and production?
2. Which failures should be retried?
3. How would you observe an agent?
4. How would you control token cost?
5. How would you defend against prompt injection?
6. Where should authorization happen?

## Checkpoint Summary

Production AI is normal backend engineering plus probabilistic model behaviour. Reliability, security, observability, evaluation, cost and latency must be designed explicitly.

## Mind Map

```text
Production AI
├─ Architecture
├─ Reliability
│  ├─ Retry
│  └─ Timeout
├─ Observability
├─ Security
├─ Evaluation
└─ Cost / Latency
```

## 2-Minute Revision

```text
API → Service → AI Workflow → Model/RAG/Tools → Validation → Response
                 + Security + Observability + Evaluation
```
