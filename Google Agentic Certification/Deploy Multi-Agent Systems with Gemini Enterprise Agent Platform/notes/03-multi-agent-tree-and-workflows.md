# 03 — Multi-Agent Tree & Workflow Agents

**Module map:** Covers ADK's hierarchical parent/sub-agent/peer transfer rules, LLM-based agents vs. deterministic workflow agents (`SequentialAgent`, `LoopAgent`, `ParallelAgent`, and custom workflow agents), and when to use each pattern.
**Exam objectives:** `3.3` (Orchestrating and coordinating agentic workflows), `3.1` (Building custom agents in ADK).
**Prerequisites:** [02 — ADK Setup & Execution Modes](02-adk-setup-and-execution-modes.md). **Next:** [04 — ADK Callbacks & Lifecycle Hooks](04-adk-callbacks-and-lifecycle-hooks.md)

---

## 1. The Multi-Agent Tree & Transfer Rules `[3.3]`

**What it is:** ADK structures multi-agent systems as a **tree** of parent agents and sub-agents rather than a flat, unrestricted mesh.

**Why it exists:** If any agent could transfer control to any other agent in a large system, two similarly named lookup agents in different branches could easily be confused. Restricting transfers to the tree makes routing predictable and reliable.

```
                   ┌──────────────┐
                   │  Root Agent  │  (Parent)
                   └──────┬───────┘
            ┌─────────────┴─────────────┐
            ▼                           ▼
   ┌─────────────────┐         ┌─────────────────┐
   │  Sub-Agent A    │◀───────▶│  Sub-Agent B    │  (Peers: share same parent)
   └────────┬────────┘  peer   └─────────────────┘
            │         transfer
            ▼        (can be disabled)
   ┌─────────────────┐
   │ Lookup Agent A1 │  (Only reachable from Sub-Agent A, not Sub-Agent B)
   └─────────────────┘
```

### Allowed Conversation Transfers
An agent in ADK can **only** transfer control to:
1. **Its sub-agents** (down the tree).
2. **Its parent** (back up the tree).
3. **Its peers** (other sub-agents sharing the **same parent**)—and you can **disable peer transfer on an agent-by-agent basis** for stricter control.

**Takeaway:** ADK restricts conversation transfers to **sub-agents, parent, or peers** (with optional per-agent peer-transfer disablement) so only relevant specialists in a branch can be invoked.

---

## 2. LLM-Based Agents vs. Workflow Agents `[3.1]`, `[3.3]`

**What it is:** ADK separates **LLM-based agents** (reasoning-driven, conversational) from **Workflow agents** (deterministic orchestration directors without their own LLM).

**Why it exists:** LLM-based agents typically alternate turns with a user and decide dynamically what to do next. When multiple agents must collaborate **without waiting on user turns in between**, or when business logic requires a **guaranteed execution sequence**, workflow agents provide deterministic control.

| Agent Type | Powered by LLM? | Execution Behavior | Best-Fit Use Case |
| :--- | :--- | :--- | :--- |
| **LLM-based Agent** | **Yes** | Dynamic reasoning, tool selection, and natural-language turns with users | Open-ended tasks, ambiguous intent, dynamic routing |
| **`SequentialAgent`** | **No** (director only) | Executes a predefined list of sub-agents **one after another** in fixed order | Structured multi-step pipelines (e.g., `Order Validation → Inventory Check → Payment → Confirmation`) |
| **`LoopAgent`** | **No** (director only) | **Repeatedly executes** a set of sub-agents until a condition is met | Iterative refinement, continuous monitoring, cyclical research, or simulated negotiation |
| **`ParallelAgent`** | **No** (director only) | Runs multiple sub-agents **simultaneously** | Independent sub-tasks like multi-source data retrieval or regional report generation |
| **Custom Workflow Agent** | **No** (arbitrary Python logic) | Developer-defined orchestration beyond Sequential/Loop/Parallel | Complex branching, stateful coordination, and custom business rules |

### Key Constraints & Design Rules
* **Deterministic repeatability:** Given the same input and configuration, workflow agents execute the exact same sequence of sub-agents.
* **`ParallelAgent` independence assumption:** `ParallelAgent` assumes **no inherent need for shared state or direct information exchange** between the concurrently executing sub-agents.
* **Worked Example (Order Processing vs. Regional Reports):**
  *Scenario (illustrative):*
  1. An e-commerce backend validates an order, checks inventory, charges payment, and sends confirmation. Because each step depends on the previous step succeeding in strict order, use **`SequentialAgent`**.
  2. A finance team generates Q3 summaries for North America, EMEA, and APAC. Because each region's report is independent and latency matters, use **`ParallelAgent`**.
  3. A research assistant gathers sources, critiques coverage, and fetches more data until the answer is sufficient. Use **`LoopAgent`**.
  *Cross-check:* None of the three workflow directors (`SequentialAgent`, `LoopAgent`, `ParallelAgent`) use an LLM to decide the next agent—they control context flow deterministically across their sub-agents (which themselves can be LLM-based agents).

**Takeaway:** Use **LLM-based agents** for adaptive reasoning and user turns; use **`SequentialAgent`**, **`LoopAgent`**, **`ParallelAgent`**, or **Custom Workflow Agents** for deterministic agent-to-agent orchestration without intermediate user turns.

---

## Common Confusions

| Term A | Term B | Key Distinction |
| :--- | :--- | :--- |
| **Workflow Agent** | **LLM-based Agent** | **Workflow agents** (`Sequential`, `Loop`, `Parallel`) have **no LLM** and deterministically direct sub-agents without user turns; **LLM agents** use an LLM to reason and alternate turns with users. |
| **`ParallelAgent`** | **`SequentialAgent`** | **`ParallelAgent`** runs independent sub-agents concurrently (assuming no shared-state exchange between them); **`SequentialAgent`** runs them one-by-one in strict order. |
| **Sub-agent transfer** | **Peer transfer** | **Sub-agent transfer** moves down from parent to child; **peer transfer** moves sideways between children of the same parent (and can be disabled per agent). |

*Sources: `Deploy Multi-Agent Systems with Gemini Enterprise Agent Platform/Sources/Raw.md` (Lesson 4).*
