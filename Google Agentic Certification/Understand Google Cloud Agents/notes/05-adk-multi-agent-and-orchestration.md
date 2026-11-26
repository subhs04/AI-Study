# 05 — ADK Multi-Agent Architecture & Orchestration

**Module map:** Covers the **Agent Development Kit (ADK)** four pillars, **Agent-to-Agent (A2A)** protocol (`/.well-known/agent.json`), stateful orchestrator vs. stateless specialist pattern, deterministic vs. LLM-driven orchestration, event streaming, and separating Gen AI from deterministic code.
**Exam objectives:** `3.3` (Orchestrating & coordinating agentic workflows), `3.1` (partial — ADK in code), `3.2` (partial — MCP/tool wrapping), `4.1` (partial — ADK trajectory evaluation).
**Prerequisites:** [04 — Gemini Platform & Enterprise Connectivity](04-gemini-platform-and-enterprise-connectivity.md). **Next:** [06 — Agent Security, Identity & Governance](06-agent-security-and-governance.md)

---

## 1. Agent Development Kit (ADK) Core Pillars `[3.1]`, `[4.1]`

**ADK** is an open-source, code-first framework (**Python**, **Go**, **Java**) for building, evaluating, and deploying multi-agent systems:

1. **Precision and Control:** Fine-grained control over multi-step workflows, multi-agent hierarchies, and rich tool ecosystems.
2. **Integrated Developer Experience:**
   * Code-first modularity, testability, and version control.
   * Local **CLI** and **web-based UI** for step-by-step debugging.
   * **Built-in evaluation** assessing two dimensions: **(1) final answer quality** and **(2) execution trajectory** (step-by-step reasoning/tool calls).
3. **Open Ecosystem:** Model-agnostic base interface, **MCP** for data/tools, **A2A** for inter-agent communication, and **tool wrapping** for **LangChain**, **LangGraph**, **CrewAI**, and **LlamaIndex**.
4. **Deployment Ready:** Containerization + native streaming + three deployment targets: **Agent runtime** (managed), **Cloud Run** (serverless containers), or **GKE** (custom container orchestration).

**Takeaway:** ADK treats agent development like software engineering—versioned code, local CLI/web UI debugging, trajectory evaluation, and flexible container/managed deployment.

---

## 2. Agent-to-Agent (A2A) Protocol & Microservice Decoupling `[3.3]`

Monolithic prompts fail when workflows span multiple specialized domains. ADK decomposes them into single-responsibility **agent microservices** communicating via **A2A**:

* **Capability Contract (`/.well-known/agent.json`):** Every A2A server automatically generates and serves an OpenAPI-like ID card at `/.well-known/agent.json` declaring:
  1. **Identity & Description:** Who the agent is and what it does.
  2. **Input Schema / Arguments:** What inputs it accepts.
  3. **Output Schema:** What structured output it promises to return.
* **True Microservice Decoupling:** Orchestrators register remote specialists via URL (`remote_a2a_agent`) without importing their source code. You can mix models (e.g., a fast, cheap model for a Researcher + a large reasoning model for a Content Builder), mix frameworks (e.g., ADK calling a LangChain agent), or rewrite a service from Python to Go without breaking callers.

**Takeaway:** A2A (`/.well-known/agent.json`) decouples agents behind standard web contracts so orchestrators only need a URL, not shared code or languages.

---

## 3. State Management: Stateful Orchestrator vs. Stateless Specialists `[3.1]`, `[3.3]`

```
┌───────────────────────────────────────────────────────────┐
│  Orchestrator Agent ("General Contractor / Clipboard")    │
│  • Holds centralized session state (research + feedback)  │
│  • Streams real-time ADK events by sender to front end    │
└──────────┬──────────────────────────────┬─────────────────┘
           │ remote_a2a_agent(URL)        │ remote_a2a_agent(URL)
           ▼                              ▼
  ┌─────────────────┐            ┌─────────────────┐
  │   Researcher    │            │      Judge      │
  │  (Stateless)    │            │  (Stateless)    │
  └─────────────────┘            └─────────────────┘
```

* **Centralized Session State ("Master Clipboard"):** The orchestrator holds the running workflow state. Developers must define the state schema (e.g., `research findings`, `judge feedback`) *before* coding.
* **Stateless Specialists:** Worker agents hold no state—they receive context, execute one task, return results, and exit. If a worker crashes and restarts, **zero workflow progress is lost**.

**Takeaway:** Keep specialist agents stateless and centralize session state in the orchestrator for fault tolerance and clean recovery.

---

## 4. Orchestration Strategies & Deterministic Code Separation `[3.3]`

ADK provides two complementary orchestration modes:

| Strategy | Agent Type | Behavior | Best-Fit Use Case |
| :--- | :--- | :--- | :--- |
| **Deterministic** | **Sequential agent** | Runs sub-agents in a fixed linear order | Predictable pipelines (`validate → retrieve → report`) |
| **Deterministic** | **Parallel agent** | Runs independent sub-agents concurrently | Fetching financial data, news, and competitor info simultaneously to cut latency |
| **Deterministic** | **Loop agent** | Repeats sub-agent(s) until exit condition or max iterations | Iterative refinement (e.g., research-to-judge loop) |
| **LLM-Driven** | **LLM agent** | Uses model reasoning to dynamically route to tools/sub-agents | Open-ended chat where next steps depend on user input |

* **Single-Parent Tree Constraint:** In hierarchical composition (e.g., Root → Research Agent → Parallel Agent), every sub-agent has **one parent**.
* **Loop Termination (`escalate=True`):** Inside a `LoopAgent`, a judge writes its verdict to shared session state; a lightweight deterministic **escalator checker** agent inspects that state and yields `escalate=True` when status passes to halt the loop cleanly.
* **Event Streaming:** The server inspects the ADK event stream by sender (e.g., *"researcher is working"*) and streams live progress to the front end over a single endpoint.

### Separating Gen AI Flexibility from Deterministic Code
1. **LLM agents:** Handle ambiguity, query decomposition, summarization, and tool selection.
2. **Deterministic code tools:** Execute critical, auditable actions (financial formulas, database writes, secured APIs) in testable Python.
3. **Workflow orchestration:** Enforces sequence, termination, and state transitions.

**Takeaway:** Use Sequential/Parallel/Loop agents for predictable control flow, LLM agents for dynamic routing, and deterministic code tools for math and state-mutating actions.

---

## 5. Worked Example: Cymbal Labs R&D Budget Allocation `[3.1]`, `[3.3]`

* **Problem:** Cymbal Labs manages dozens of R&D projects using static spreadsheets that miss real-time equipment usage and vendor cost changes.
* **Solution:** An ADK custom agent separates reasoning from math:
  1. User asks: *"If we double spending on materials... how much longer does that extend the available budget?"*
  2. **Gemini (LLM)** decomposes the query and pulls live spending/budget data.
  3. A **deterministic Python tool** executes Cymbal Labs' proprietary allocation formula (guaranteeing identical, auditable results on every run—never probabilistic LLM math).
  4. **Gemini** summarizes the exact numerical output in plain language.

---

## Common Confusions

| Term A | Term B | Key Distinction |
| :--- | :--- | :--- |
| **A2A** | **MCP** | **A2A** (`/.well-known/agent.json`) is for *agent-to-agent* collaboration across services; **MCP** is for *agent-to-tool/data* discovery and invocation. |
| **LoopAgent** | **LLM Agent** | **LoopAgent** deterministically repeats a cycle until an `escalate=True` condition or max iterations; an **LLM Agent** dynamically chooses which sub-agent/tool to call next. |
| **Final Answer Eval** | **Trajectory Eval** | Final answer checks *what* the agent returned; trajectory checks *how* it got there (tool calls and reasoning steps). |

*Sources: Agent Development Kit (`gemini-code-1795717575144.md`, theory lessons 1–4 only; `GENAI104` lab parked for Phase 2).*
