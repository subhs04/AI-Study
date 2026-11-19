# AgentOps on Google Cloud: Govern and Optimize Pillars

**Module map:** The **govern** services (Agent Registry, Agent Gateway, Agent Identity) and **optimize** services (Agent Evaluation, Agent Simulation, Agent Observability, Agent Optimizer): purpose, role, and how to tell them apart. Covers **5.1**, **5.2** (registry, gateway, identity, Model Armor), **3.2/3.3** (registry, identity) and **4.1**, **4.2** (evaluation, observability, troubleshooting). Prerequisites: `01-agentops-lifecycle.md`, `02-build-and-scale-platform.md`. Next: `04-evaluation-as-unit-tests.md` (how evaluation is applied in CI), then `05`, `06`. Related: `Model Armor/notes/overview-threats-filters.md`.

---

## 1. Govern: why [5.1][5.2]

**What/why:** Enterprise compliance means knowing **exactly what an agent did, why, and that it was cleared to do it**. With many agents and tools, you need a catalog (what exists), a boundary (how traffic flows and what is blocked), and identity (who is acting).

```
              ┌──────────── Agent Registry (catalog: what exists, who owns, who may call) ───────────┐
              │                                                                                     │
 Agent A ──► AGENT GATEWAY ──► tool / other agent / 3rd-party API        Agent Identity (SPIFFE ID)
 (has unique    │  routing rules + Model Armor policies on traffic        stamps every call → audit trail
  identity)     └► blocks prompt injection, toxic content, data leakage before leaving the perimeter
```
(Study-aid diagram combining the three services.)

---

## 2. Agent Registry [5.1][3.2][3.3]

**What it is:** A **centralized catalog that inventories all available agents and tools**. Analogy from the course: **Model Registry in MLOps** (a central, versioned record of models), and equally foundational in production.

| Capability | What it gives | Example |
|---|---|---|
| **Discovery and reusability** | Single point of access to search for existing agents/tools; less duplicate work | Stops five teams building their own "check inventory" tool |
| **Standardization** | Enforces an **enterprise contract**: consistent formats for **tool metadata, inputs/outputs, error handling** → interoperability across the microservice mesh | Different agents can understand each other's tools |
| **Security and access control** | Stores **ownership details and consumer subscriptions**; enforces **granular access controls** | Recommendation agent **cannot** call the customer refund tool |
| **Auditability and lineage** | **Version-controlled record** of agent updates, usage, and **data sources each agent accessed** | Compliance proof; easier debugging |

**Takeaway:** Registry = who/what exists + contract + who may use it + history. Think "Model Registry for agents and tools".

---

## 3. Agent Gateway [5.1][5.2]

**What it is:** A **secure, centralized traffic management and network routing layer**, "air traffic control" for all agent communications; the **secure boundary wall** of the agent ecosystem.

- **Routing:** enforces operational routing rules across distributed environments.
- **Security:** **natively applies Model Armor policies** to intercept data in transit. Blocks **prompt injection**, filters **toxic content**, prevents **sensitive enterprise data leakage** before it leaves the network perimeter.

**Takeaway:** Gateway = routing + inline Model Armor enforcement at the network boundary. Model Armor is the *filter engine*; the Gateway is *where it is applied* here.

---

## 4. Agent Identity [5.1][3.2]

**What it is:** A security framework giving **every individual agent a unique, cryptographically verifiable ID backed by the SPIFFE open standard**.

Terms defined:
- **SPIFFE:** an open standard for issuing verifiable workload identities (a cryptographic ID for a piece of software rather than a person).
- **Non-repudiation:** an actor cannot credibly deny having performed an action, because the action is bound to a verifiable identity.
- **Audit trail:** tamper-proof log of who did what.

**Role:** An immutable cryptographic identity per agent produces an **explicit, tamper-proof audit trail** for every database query, internal microservice call or third-party API tool execution the agent initiates. It also enables **fine-grained access control** across the agent mesh.

**Takeaway:** Identity = *who is calling* (provable); Registry = *what they may call*; Gateway = *the path and filters they pass through*.

> **Gap [5.1][3.2]:** The exam guide expects **Principal Access Boundary (PAB) policies using Agent Identity**, **OAuth 2.0** for agent-to-tool calls, and identity propagation. None are in this source. Cover from the "Govern agents with Agent Gateway, Agent Registry, and Policies" course or official docs.

### Govern services compared

| | Agent Registry | Agent Gateway | Agent Identity |
|---|---|---|---|
| Role | **Catalog** and contract | **Traffic boundary** | **Per-agent credential** |
| Answers | What agents/tools exist? Who owns/may consume them? | How does traffic flow, and is content safe? | Who exactly performed this call? |
| Mechanism | Metadata, subscriptions, versions, lineage | Routing rules + Model Armor | SPIFFE-backed cryptographic ID |
| Key outcome | Reuse, standardization, access control, lineage | Blocks injection, toxicity, data leakage in transit | Non-repudiation, tamper-proof audit trail |
| MLOps analogy | Model Registry | (network perimeter / API gateway idea) | Service identity |

---

## 5. Optimize: why [4.1][4.2]

**What/why:** Multi-step reasoning chains are **notoriously hard to debug**: failure may be in the prompt, a tool call, retrieval or a loop. Optimize services give visibility and a **continuous feedback loop of testing, monitoring and refinement**.

| Service | Role | Does what | Time focus |
|---|---|---|---|
| **Agent Evaluation** | **Quality gatekeeper** | Scores outputs on **groundedness, relevance, safety** against baselines **before release** | How *well* it did |
| **Agent Simulation** | **Stress tester** | Generates **synthetic traffic in a sandbox** to catch edge cases and **looping errors** early | What it *might* do |
| **Agent Observability** | **Debugger** | Traces **step-by-step reasoning, tool calls, latency, token use** to show exactly why errors happen | What it *did* |
| **Agent Optimizer** | **Performance tuner** | Uses observability data to **automatically refine prompts, tool descriptions, response speeds** | Doing it *better next time* |

Memory hook (course): **Observability = what it did; Evaluation = how well; Simulation = what it might do; Optimizer = better next time.**

```
 pre-release                     production
 Simulation ─► Evaluation ─► [deploy] ─► Observability ─► Optimizer ─┐
 (what might)  (how well)               (what did)       (do better) │
      ▲                                                              │
      └───────────── refined prompts / tool descriptions ◄───────────┘
```

Term: **Groundedness** = response is supported by the provided data/sources rather than invented. (A hallucination is a groundedness failure.)

| Symptom (*illustrative*) | Use | Why |
|---|---|---|
| Agent loops calling the same tool in production; need to see exact steps | **Observability** | Traces reasoning and tool calls |
| New version must be blocked unless groundedness/safety clear baseline | **Evaluation** | Gate before release |
| Want to find looping bugs before real users arrive | **Simulation** | Synthetic traffic in sandbox |
| Trace data shows verbose, slow prompts; want automated improvement | **Optimizer** | Refines prompts, tool descriptions, speed |

**Takeaway:** Simulate and evaluate *before* release, observe *in production*, optimize from observed data, and loop.

> **Gap [4.1]:** Not covered: ADK evalset, Gen AI evaluation service, custom autoraters, building continuous evaluation pipelines. Google Cloud Observability (Cloud Logging, Cloud Trace) is in-scope for the exam; its implementation is in `10-agent-tracing-opentelemetry.md` and `11-structured-logging-cloud-logging.md`. Identity, access and data security detail is in `07`-`09`.
>
> **Clarification (not in source):** "Agent Evaluation" and "Agent Observability" are the names used in this course. Whether they correspond exactly to the exam guide's "Gen AI evaluation service" and "Cloud Logging/Cloud Trace" is unconfirmed. Verify in the official docs before assuming equivalence.

---

## 6. Worked example: investigating a CymbalMart incident [5.1][4.2]

Incident: the *product recommendation agent* invoked the *customer refund tool* and issued refunds.

| Question | Service | Cross-check |
|---|---|---|
| Which agent made the call, provably? | **Agent Identity** (unique SPIFFE-backed ID, audit trail) | Non-repudiation ✔ |
| Why was it allowed? | **Agent Registry** access controls/subscriptions were too broad | Registry enforces which agents may trigger sensitive tools ✔ |
| How to prevent injected prompts triggering tools in transit | **Agent Gateway** with Model Armor | Blocks prompt injection before action ✔ |
| Which exact reasoning step selected the refund tool? | **Agent Observability** trace | Traces reasoning and tool calls ✔ |
| How to ensure the fix doesn't regress | **Agent Evaluation** on golden prompts incl. refund edge cases | Pre-release quality gate ✔ |

---

## Common confusions

| Looks like | Difference |
|---|---|
| **Agent Registry vs Agent Identity** | Registry = catalog and access/ownership records; Identity = cryptographic ID of each agent |
| **Agent Gateway vs Model Armor** | Model Armor = prompt/response sanitization policies; Gateway = traffic layer that natively applies them to data in transit |
| **Agent Registry vs Model Registry** | Model Registry (MLOps) catalogs models; Agent Registry catalogs agents and tools. Analogy, not same product |
| **Evaluation vs Simulation** | Evaluation scores outputs against baselines; Simulation generates synthetic traffic to find edge cases and loops |
| **Observability vs Optimizer** | Observability = tracing/visibility (read-only insight); Optimizer = acts on that data to refine prompts/tools |
| **Gateway vs Registry for access control** | Registry stores *who may use what* (policy data: ownership, subscriptions); Gateway routes and filters *traffic*. (Exact enforcement split is not detailed in source; verify) |

Sources: AgentOps: Operationalize AI Agents on Google Cloud, "AgentOps on Google Cloud: Govern and optimize" (`sources/the-agent-lifecycle-raw.md`).
