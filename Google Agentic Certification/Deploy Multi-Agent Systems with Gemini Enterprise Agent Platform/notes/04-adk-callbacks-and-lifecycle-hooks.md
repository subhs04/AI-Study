# 04 — ADK Callbacks & Lifecycle Hooks

**Module map:** Covers ADK's six callback hooks across three execution scopes—**Agent lifecycle** (`before_agent`, `after_agent`), **LLM interaction** (`before_model`, `after_model`), and **Tool execution** (`before_tool`, `after_tool`)—including their short-circuiting and override semantics.
**Exam objectives:** `3.1` (Designing and building agentic workflows in code).
**Prerequisites:** [03 — Multi-Agent Tree & Workflow Agents](03-multi-agent-tree-and-workflows.md). **Next:** [05 — Agent Runtime Deployment & ADK Evaluation](05-agent-runtime-deployment-and-adk-eval.md)

---

## 1. What ADK Callbacks Are & How They Fire `[3.1]`

**What it is:** **Callbacks** are custom Python functions registered on an agent that ADK automatically invokes at predefined interception points during execution.

**Why it exists:** Production agents need cross-cutting logic—guardrails, caching, authorization checks, dynamic prompt injection, logging, and output redaction—without polluting core agent or tool business logic.

```
  [before_agent] ──(returns content?)──▶ Skip agent run (& skip after_agent)
        │
        ▼
  Agent Run Starts
        │
        ├──▶ [before_model] ──(returns LLM Response?)──▶ Skip LLM call
        │          │
        │          ▼
        │       LLM Call ──▶ [after_model] (modify/censor/parse to state)
        │
        └──▶ [before_tool] ──(returns dict?)──▶ Skip tool.run_async()
                   │
                   ▼
             Tool.run_async() ──▶ [after_tool] (returns new dict? replaces result)
        │
        ▼
  [after_agent]  (runs only if agent completed normally & wasn't skipped)
```

**Takeaway:** Callbacks act as deterministic lifecycle hooks that can observe, modify, cache-short-circuit, or block execution at the agent, model, or tool boundary.

---

## 2. The Six Callback Hooks & Short-Circuit Semantics `[3.1]`

### Scope Availability
* **Agent lifecycle callbacks** (`before_agent`, `after_agent`): Available on **any agent inheriting from `BaseAgent`** (including `LLMAgent`, `SequentialAgent`, `ParallelAgent`, and `LoopAgent`).
* **LLM & Tool callbacks** (`before_model`, `after_model`, `before_tool`, `after_tool`): Specific to **`LLMAgent`** (because workflow agents do not call LLMs or tools directly).

| Callback Hook | Scope | When It Fires | Common Use Cases | Return / Short-Circuit Behavior |
| :--- | :--- | :--- | :--- | :--- |
| **Before Agent** | `BaseAgent` (all agents) | Immediately before the agent's `run` method executes | Setup run-specific resources/state, validate session state, log entry | Returning default response / cached content **skips the agent's run** (and `after_agent` will **not** run) |
| **After Agent** | `BaseAgent` (all agents) | Immediately after the agent's `run` completes successfully | Cleanup, post-execution validation, log completion, modify final state or output | **Does not run** if skipped by Before Agent or if `end_invocation` was set during the run |
| **Before Model** | `LLMAgent` | Just before the `generate_content` request is sent to the LLM | Inject dynamic instructions or few-shot examples from state, modify config, input guardrails (e.g., profanity filter), request-level caching | Returning an **LLM Response** object **skips the LLM call** and uses the returned response directly |
| **After Model** | `LLMAgent` | Just after raw LLM response is received, before agent processing | Log outputs, reformat, censor sensitive info, parse structured data into `callback_context.state`, handle error codes | Modifies/replaces the raw LLM response before the agent processes it |
| **Before Tool** | `LLMAgent` (Function & Agent Tools) | After LLM generates a function call, just before the tool's `run` method | Inspect/modify tool arguments, **authorization checks**, log tool attempts, tool-level caching | Returning a **dictionary** **skips `tool.run_async()`** and uses that dictionary directly as the tool result |
| **After Tool** | `LLMAgent` (Function & Agent Tools) | Just after `tool.run_async()` completes successfully | Log tool results, post-process/format output, save parts of result to session state | Returning a **new dictionary** **replaces the original tool response** seen by the LLM |

### Worked Example: Implementing Guardrails & Caching with Callbacks
*Scenario (illustrative):* You want to enforce two controls on an `LLMAgent`: (1) block profanity before calling Gemini, and (2) check user authorization before calling a `delete_record` tool.
1. Register a **Before Model** callback to inspect the outgoing prompt. If profanity is detected, return a canned `LLM Response` refusal—ADK **skips the LLM call** completely, saving cost and preventing unsafe generation.
2. Register a **Before Tool** callback to check the caller's permissions before `delete_record` runs. If unauthorized, return `{"error": "Permission denied"}`—ADK **skips `run_async()`** and passes the dictionary back to the LLM as the tool result.
*Cross-check:* Both hooks enforce policy deterministically in Python rather than relying on the LLM to obey prompt instructions, and both use their documented short-circuit return types (`LLM Response` for `before_model`, `dict` for `before_tool`).

**Takeaway:** Returning a response object in **Before Agent** (`content`), **Before Model** (`LLM Response`), or **Before Tool** (`dict`) short-circuits execution; **After** callbacks inspect, redact, or transform outputs and update `callback_context.state`.

---

## Common Confusions

| Term A | Term B | Key Distinction |
| :--- | :--- | :--- |
| **`before_model` return** | **`before_tool` return** | Returning an **`LLM Response`** in `before_model` skips the LLM call; returning a **`dict`** in `before_tool` skips `tool.run_async()`. |
| **Agent callbacks** | **Model / Tool callbacks** | Agent callbacks (`before/after_agent`) work on **all `BaseAgent` subclasses** (including `Sequential`, `Parallel`, `Loop`); Model and Tool callbacks only work on **`LLMAgent`**. |
| **`after_agent` execution** | **`before_agent` skip** | If `before_agent` returns content to skip the agent run (or `end_invocation` is set), **`after_agent` does not run**. |

*Sources: `Deploy Multi-Agent Systems with Gemini Enterprise Agent Platform/Sources/Raw.md` (Lesson 5).*
