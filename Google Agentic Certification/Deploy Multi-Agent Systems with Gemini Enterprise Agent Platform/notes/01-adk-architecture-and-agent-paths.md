# 01 — ADK Architecture & Google Cloud Agent Paths

**Module map:** Covers Google Cloud's four agent-building paths, **Agent Development Kit (ADK)** positioning, ADK's four core agent components (Models, Tools, Orchestration, Runtime), and ADK's foundational primitives (Agent, Tools, Sessions/State vs. Memory, Events, Runner, Artifacts, Streaming, Planning, Cloud Trace).
**Exam objectives:** `3.1` (Designing and building agentic workflows in code), `4.2` (partial — Agent Runtime & Cloud Trace observability).
**Prerequisites:** None. **Next:** [02 — ADK Setup & Execution Modes](02-adk-setup-and-execution-modes.md)

---

## 1. Google Cloud's Four Agent-Building Paths `[3.1]`

**What it is:** Google Cloud offers a spectrum of agent-building options ranging from out-of-the-box applications (low effort, low customization) to fully custom code (high effort, maximum control).

**Why it exists:** Different enterprise use cases trade off speed-to-market against architectural flexibility, multi-agent orchestration, and infrastructure ownership.

| Path | Best-Fit Use Case | Flexibility vs. Effort | Infrastructure Management |
| :--- | :--- | :--- | :--- |
| **1. Customer Engagement Suite (Conversational Agents)** | External-facing customer support integrating with human support teams, telephony, and communication platforms | Out-of-the-box; easy to use, lower customization | Fully managed |
| **2. Gemini Enterprise app** | Internal enterprise search and AI assistant across Drive, Chat, Mail, ticketing platforms, and databases | Out-of-the-box; easy to use, lower customization | Fully managed |
| **3. Custom / Open-Source on Agent Runtime** | Bespoke agents built from scratch using the **Google Gen AI SDK** or low-level frameworks (**LangChain**, **Genkit**) | Maximum flexibility; highest configuration/implementation effort | Managed via **Agent Runtime** (or self-hosted if not using Agent Runtime) |
| **4. ADK + Agent Runtime** | Multi-agent systems needing deterministic control, shared state, tool reuse, evaluation, and zero infrastructure management | Balance of custom code freedom and built-in multi-agent convenience | Fully managed via **Agent Runtime** (autoscaling & resource allocation) |

**Clarification (not in source):** In the current exam guide terminology, **Agent Runtime** was formerly called *Agent Engine*, and **Agent Platform** refers to *Vertex AI*.

**Takeaway:** Choose **Conversational Agents** for external/telephony support, **Gemini Enterprise app** for internal knowledge search, and **ADK + Agent Runtime** when you need code-first multi-agent control without managing infrastructure.

---

## 2. What ADK Is & Its Four Core Components `[3.1]`

**What it is:** **Google Agent Development Kit (ADK)** is a client-side Python SDK (also supporting an open ecosystem) for building, managing, evaluating, and deploying AI-powered multi-agent systems with a code-first approach.

**Why it exists:** Building multi-agent systems from raw LLM calls requires boilerplate for conversation history, state sharing, tool invocation, evaluation, and debugging. ADK provides these primitives while letting developers reuse tools from other frameworks (**LangChain**, **CrewAI**) and integrate third-party/open-source models (**Gemini**, **Claude**, or custom models via its base LLM interface).

Every ADK agent relies on **four main components**:

```
┌────────────────────────────────────────────────────────────────┐
│  1. MODELS (Reasoning)                                         │
│     • Gemini (optimized), Claude, or custom/OSS via base LLM   │
│     • Reasons over goals, plans steps, generates responses     │
├────────────────────────────────────────────────────────────────┤
│  2. TOOLS (Action)                                             │
│     • Fetch data, call external APIs, run code, or invoke      │
│       other agents (Agent Tool)                                │
├────────────────────────────────────────────────────────────────┤
│  3. ORCHESTRATION (Control Flow & Context)                     │
│     • Configures steps, manages state/memory, routes between   │
│       sub-agents (LLM-driven or deterministic workflow agents) │
├────────────────────────────────────────────────────────────────┤
│  4. RUNTIME (Execution)                                        │
│     • Executes the system when invoked by an end-user query    │
│     • Local (Web UI / CLI / API Server) or Agent Runtime       │
└────────────────────────────────────────────────────────────────┘
```

**Takeaway:** An ADK agent separates **Models** (reasoning), **Tools** (external actions), **Orchestration** (control flow and state), and **Runtime** (execution environment).

---

## 3. Core ADK Primitives `[3.1]`, `[4.2]`

Before building or deploying ADK agents, understand how ADK names its runtime building blocks:

| Primitive | Definition & Purpose |
| :--- | :--- |
| **Agent** | Fundamental worker unit for a specific task. Can use an LLM for reasoning or act as a deterministic workflow controller. Delegates via LLM-driven transfer or explicit **Agent Tool** invocation. |
| **Tools** | Capabilities beyond conversation: custom Python functions, **Agent Tools** (wrapping another agent), built-in **Code Execution** (generating and running code for complex calculations), external APIs/data, and **long-running tools** for asynchronous operations. |
| **Event** | The basic unit of communication in a session—represents a user message, agent reply, LLM response, or tool call/result, forming the conversation history. |
| **Session & State** | **Session** tracks the context and event history of a *single conversation*. **State** is the agent's short-term working memory for that conversation. |
| **Memory** | Long-term context enabling agents to recall information about a user *across multiple sessions* (distinct from short-term session state). |
| **Artifact Management** | Saves, loads, and manages **versioned** files or binary data (images, documents, generated reports) tied to a session or user, enabling agent collaboration on documents. |
| **Runner** | The execution engine that orchestrates agent interactions based on events and coordinates with backend **Session** and **Artifact** services. |
| **Callbacks** | Custom Python functions registered at specific lifecycle points (agent, model, or tool) for logging, checks, caching, guardrails, or modifying behavior. |
| **Planning** | Advanced capability where agents break complex goals into smaller steps (e.g., a **ReAct planner**). |
| **Native Streaming** | Bidirectional text and audio streaming, integrating with the **Gemini Live API** via simple configuration changes. |
| **Dev Tooling & Cloud Trace** | Local **CLI** and **Web UI** for debugging and visualization; **Cloud Trace** captures distributed latency traces across LLM and tool calls in Agent Runtime. |

### Worked Example: Choosing State vs. Memory vs. Artifacts
*Scenario (illustrative):* A financial research assistant chats with a user across multiple weeks.
1. During today's chat, the agent stores the ticker symbol currently being analyzed in **Session State** (short-term working memory for this conversation).
2. Two weeks later, the user starts a new chat and asks, *"Use my usual risk profile."* The agent retrieves the user's preferences from **Memory** (cross-session long-term context).
3. When the agent generates a downloadable PDF report, it saves it via **Artifact Management** so it is versioned and accessible to other agents in the workflow.
*Cross-check:* Session State is scoped to one conversation; Memory spans multiple conversations; Artifacts store versioned binary/file outputs rather than key-value conversational state.

**Takeaway:** ADK distinguishes short-term **Session/State** (single conversation), long-term **Memory** (across sessions), and **Artifacts** (versioned files/binary data), all coordinated by the **Runner** over **Events**.

---

## Common Confusions

| Term A | Term B | Key Distinction |
| :--- | :--- | :--- |
| **Session State** | **Memory** | **Session State** is short-term working memory for a *single* conversation; **Memory** is long-term user context recalled *across multiple sessions*. |
| **ADK** | **Agent Runtime** | **ADK** is the client-side Python SDK for building and orchestrating agents; **Agent Runtime** is the fully managed Google Cloud service that hosts, autoscales, and secures them in production. |
| **Gemini Enterprise app** | **Conversational Agents (CES)** | **Gemini Enterprise app** targets *internal* enterprise search/knowledge exchange; **Conversational Agents (CES)** targets *external-facing* customer support with telephony and human handoff. |

*Sources: `Deploy Multi-Agent Systems with Gemini Enterprise Agent Platform/Sources/Raw.md` (Course intro; Lessons 1–2).*
