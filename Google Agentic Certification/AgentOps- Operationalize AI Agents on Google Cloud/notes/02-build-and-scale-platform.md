# AgentOps on Google Cloud: Build and Scale Pillars

**Module map:** The Gemini Enterprise Agent Platform's **build** services (Agent Development Kit, Agent Studio) and **scale** services (Agent Runtime, Agent Sandbox, Agent Sessions, Agent Memory Bank): what each is and when to pick it. Covers **3.1** (ADK, sessions, memory), **4.2** (runtime), with context for **2.1** (sandbox). Prerequisite: `01-agentops-lifecycle.md`. Next: `03-govern-and-optimize-platform.md`. Agent Runtime's traffic routing is used in `06-canary-deployments-and-rollback.md`.

**Product names:** Agent Runtime (formerly Agent Engine). The course says "Gemini Enterprise Agent Platform" and also "Agent Platform"; treat them as the same platform (the source flags the variant). Agent Platform was formerly Vertex AI.

---

## 1. Why a platform: the four pillars [4.2]

**What/why:** Production agents need tool management, orchestration and memory state, plus a central system to **discover, register, administer and securely route traffic** across a mesh of tools and agents. Google Cloud groups its answer into four pillars.

| Pillar | Question it answers | Services (this module) |
|---|---|---|
| **Build** | How do teams build multi-agent workflows without rewriting core infrastructure? | Agent Development Kit, Agent Studio |
| **Scale** | Where do agents run, hold state and execute code? | Agent Runtime, Agent Sandbox, Agent Sessions, Agent Memory Bank |
| **Govern** | Who can do what, and how do we prove it? | Agent Registry, Agent Gateway, Agent Identity |
| **Optimize** | Is it working, and how do we improve it? | Agent Evaluation, Agent Simulation, Agent Observability, Agent Optimizer |

Govern and optimize are in `03-govern-and-optimize-platform.md`.

```
 BUILD            SCALE (runtime layer)                 GOVERN        OPTIMIZE
 ADK / Studio ──► Agent Runtime ◄── Sessions (short-term) ... Registry     Evaluation
                      │        ◄── Memory Bank (long-term)    Gateway      Simulation
                      └──► Sandbox (untrusted code)           Identity     Observability
                                                                           Optimizer
```
(Diagram is a study aid, not an official architecture.)

---

## 2. Build pillar [3.1]

**What/why:** Without standard frameworks every team re-implements orchestration, memory and tool plumbing. The build pillar standardizes this.

| Service | What it is | Who/when |
|---|---|---|
| **Agent Development Kit (ADK)** | **Programmatic, model-agnostic** framework. Backend engineers define **deterministic workflows**, **memory state** and **tool constraints** with standard software code | Engineers who need code-level control, versioning, testing, CI/CD |
| **Agent Studio** | **Low-code visual canvas** for rapid prototyping, setting up **prompt templates**, and managing **enterprise data grounding** natively (examples: BigQuery, Google Workspace) | Fast prototyping, less code, data-grounded agents |

| Decision | ADK | Agent Studio |
|---|---|---|
| Interface | Code | Visual canvas |
| Strength | Deterministic workflow, memory and tool constraints as code | Speed of prototyping, built-in grounding |
| Model lock-in | **Model-agnostic** (source) | Not stated |
| Typical persona | Backend engineer / AI engineer | Prototyper, low-code builder |

**Takeaway:** ADK = code-first, model-agnostic, deterministic control. Agent Studio = low-code canvas for prototypes and grounding.

> **Clarification (not in source):** The exam guide lists **Workflow Builder** (formerly Agent Designer) and **CX Agent Studio** as the low-code tools for objective 1.1. This module's "Agent Studio" may or may not be the same product as CX Agent Studio. Verify in the official docs; do not assume equivalence.

---

## 3. Scale pillar [4.2][3.1]

**What/why:** Autonomous agents run **long-running execution loops** and must keep **state**. Ordinary stateless compute does not fit well, so the platform offers managed runtimes, state stores and an isolated place to run generated code.

| Service | One-liner | What it is | AgentOps role (CymbalMart example) |
|---|---|---|---|
| **Agent Runtime** | Fully managed, serverless, scalable execution environment | High-performance serverless infrastructure to deploy and scale agents with **sub-second starts**, **maintaining state for long-running workloads** | **Autoscaling** for traffic surges (flash sales, holiday rush); scales back down to **minimize cost** in quiet periods |
| **Agent Sandbox** | Secure, isolated environment for code execution | Customizable, isolated playground where agents **generate code, run scripts, automate GUI workflows** | Run dynamic logic (inventory availability, discount rule validation, dynamic pricing scripts) **without exposing core production systems** to errors |
| **Agent Sessions** | Managed service for conversation history | Stores and manages **individual interaction history**: the definitive source for **conversation context and multi-turn logic** | Continuity inside **one multi-step transaction** (cart items, shipping address, applied discount codes): no repeated questions |
| **Agent Memory Bank** | Persistent storage for long-term agent context | Managed service giving **persistent, high-accuracy context** so agents remember users and personalize from history spanning **weeks or months** | Long-term "brain" of personalized assistants: purchase history, preferences, loyalty data → tailored recommendations and cross-sell |

### Sessions vs Memory Bank

| | Agent Sessions | Agent Memory Bank |
|---|---|---|
| Scope | **One interaction/conversation** (multi-turn) | **Across interactions**, weeks or months |
| Holds | Turn-by-turn history, cart, address, applied codes | User history, preferences, loyalty data |
| Memory type (course term elsewhere) | Short-term | Long-term |
| Fails if missing | Agent forgets what you said two turns ago | Agent treats a returning customer as a stranger |
| Example | "Use the same address as before" within this checkout | "Recommend based on your last 3 years of purchases" |

> Short-term vs long-term memory wording matches the Agent Strategy notes (`framework-and-lifecycle.md`: short-term = current chat, long-term = learns from past interactions).

### Which scale service for which scenario

| Scenario | Service | Why |
|---|---|---|
| Traffic spikes 20x during a sale, then idles | **Agent Runtime** | Serverless autoscale up and down, cost control |
| Agent writes and runs a Python pricing script generated at runtime | **Agent Sandbox** | Isolated execution protects production systems |
| Agent must remember items in the cart during checkout | **Agent Sessions** | Single-transaction conversation context |
| Agent should greet a returning shopper with relevant recommendations | **Agent Memory Bank** | Persistent cross-session personalization |
| Agent must automate a GUI workflow safely | **Agent Sandbox** | GUI automation is listed as a sandbox capability |

**Takeaway:** Runtime = *where agents run and scale*; Sandbox = *where untrusted/generated code runs*; Sessions = *this conversation*; Memory Bank = *this user over time*.

> **Gap [4.2]:** Exam objective 4.2 asks you to select Agent Runtime vs **Cloud Run vs GKE** by use case, requirements and cost. This source gives only Agent Runtime's strengths (serverless, autoscale, state, sub-second starts). No Cloud Run/GKE comparison here. Cover it from the deployment course or official docs.
>
> **Gap [3.1]:** Configuring Sessions and Memory Bank (APIs, retention, extraction) is named in the exam guide but **not covered**; this module is conceptual only. "Craft ADK Agents with Persistent Memories" (Good to Have) is the detailed source.

---

## 4. Worked example: picking services for a CymbalMart assistant [3.1][4.2]

Requirement: a shopping assistant must (a) handle Black Friday spikes cheaply, (b) keep checkout details through a purchase, (c) recall preferences from last year, (d) compute discount combinations from a generated script.

| Requirement | Pick | Cross-check |
|---|---|---|
| (a) spikes and cost | Agent Runtime | Serverless scale-up/down ✔ |
| (b) checkout details | Agent Sessions | Within single multi-step transaction ✔ |
| (c) last year's preferences | Agent Memory Bank | Weeks/months history ✔ (Sessions would be wrong: scope is one interaction) |
| (d) generated script | Agent Sandbox | Isolated execution ✔ (running it in Runtime directly would expose core systems) |

---

## Common confusions

| Looks like | Difference |
|---|---|
| **Agent Sessions vs Agent Memory Bank** | Sessions = conversation context in one interaction; Memory Bank = persistent cross-interaction user memory |
| **Agent Runtime vs Agent Sandbox** | Runtime hosts and scales the **agent**; Sandbox isolates **code the agent generates/executes** |
| **ADK vs Agent Studio** | ADK = code framework (deterministic workflows, model-agnostic); Agent Studio = low-code canvas |
| **Agent Runtime vs Agent Engine** | Same product: Agent Runtime is the new name for Agent Engine |
| **Build pillar vs Build stage** | Pillar = services for developing; stage = first step of the lifecycle loop |

Sources: AgentOps: Operationalize AI Agents on Google Cloud, "AgentOps on Google Cloud: Build and scale" (`sources/the-agent-lifecycle-raw.md`).
