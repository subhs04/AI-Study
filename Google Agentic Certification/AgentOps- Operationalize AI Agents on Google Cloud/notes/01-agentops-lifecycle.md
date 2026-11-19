# AgentOps: Definition and Lifecycle

**Module map:** What AgentOps is, how it differs from DevOps and MLOps, the four-stage lifecycle (build, test, deploy, monitor), who does what, and the environment promotion idea. Covers **4.2** (deploy, monitor, troubleshoot), **4.1** (evaluation-based testing, golden dataset). Context for **3.1**. Related notes: `02-build-and-scale-platform.md` (where agents run), `03-govern-and-optimize-platform.md` (registry, gateway, identity, evaluation, observability), then the CI/CD deep dive: `04-evaluation-as-unit-tests.md`, `05-cicd-pipeline-quality-gates.md`, `06-canary-deployments-and-rollback.md`; then security (`07`, `08`, `09`) and observability (`10`, `11`). Related note from another module: `Agent Strategy Discover Design Prototype/notes/framework-and-lifecycle.md` (a *different* four-stage lifecycle, see Common confusions).

---

## 1. What AgentOps is and why it exists [4.2]

**What/why:** Operating software in production is a discipline. *DevOps* manages application code and infrastructure. *MLOps* manages predictive models and data pipelines. An AI agent is **all three at once**: app code + an AI model + real-world tools (APIs, databases, other services). Neither discipline covers the case where the *model itself chooses the execution path*. **AgentOps** is the discipline adapted from DevOps and MLOps for building, deploying and operating AI agents.

Terms defined:
- **Non-deterministic reasoning:** the same goal can be solved via *different logical paths* (different tool calls, different order) on different runs. Same input does not guarantee same execution.
- **Model drift:** a model's accuracy decays because real-world data changes over time (an MLOps concern).
- **Tool-use pattern:** which tools an agent calls, with what arguments, in what order.

| Discipline | Primary focus | Key challenge | Goal in one phrase |
|---|---|---|---|
| **DevOps** | App code and infrastructure | **Predictability and uptime**: eliminate variance; same code = same result in every environment | Deterministic delivery |
| **MLOps** | Predictive models and data pipelines | **Model drift and retraining**: detect accuracy decay, automate the refresh cycle | Keep the model fresh |
| **AgentOps** | App code + AI models + tools | **Non-deterministic reasoning**: govern unpredictable execution and tool-use patterns | Keep unpredictable reasoning safe, reliable, cost-effective |

**Takeaway:** DevOps forces predictability, MLOps fights data drift, AgentOps tackles non-deterministic reasoning. (Course key takeaway.)

---

## 2. The four-stage lifecycle [4.2][4.1]

**What/why:** A continuous, **iterative loop** (not a one-way pipeline) that takes an agent from development to a production state where it handles complex tasks reliably. Monitor output feeds back into Build.

```
   ┌──────────────────────────── feedback loop ────────────────────────────┐
   ▼                                                                        │
 BUILD ───► TEST ───► DEPLOY ───► MONITOR ──── drift / KPI data ────────────┘
 code,      unit +    canary or    latency/turn,
 prompts,   evaluation blue-green  token spend,
 memory,    vs golden  rollout     hallucination
 tools      dataset                rate
 → container
```

| Stage | What happens | Output / key mechanism |
|---|---|---|
| **1. Build** | Code the agent's **core logic**, **prompt templates** and **memory architecture**; integrate the external **tools and APIs** the agent executes | **Containerized application code** optimized for deployment |
| **2. Test** | Traditional **unit tests** + **evaluation-based testing**: run the agent against a **golden dataset** to measure reasoning quality, alignment and safety | Pass/fail evidence on quality, not just "code runs" |
| **3. Deploy** | Introduce containers to production with **controlled strategies** to limit risk: **canary release** or **blue-green deployment** | Gradual or instantly reversible rollout |
| **4. Monitor** | Track beyond CPU/memory: **agent-specific KPIs** (latency per turn, token spend, hallucination rate). Drift data goes **back to Build** | Corrections before end users are affected |

Terms defined:
- **Golden dataset:** a curated benchmark of prompts paired with ideal responses. It is the yardstick for evaluation-based testing.
- **Evaluation-based testing:** scoring the *quality* of agent behavior against the golden dataset (reasoning, alignment, safety). Needed because an agent can pass every unit test and still reason badly.
- **Canary release:** send a **small fraction of live traffic** to the new version first; widen only if healthy.
- **Blue-green deployment:** run old (blue) and new (green) environments side by side and switch traffic; if anomalies appear, **switch back instantly** (instant rollback).
- **Latency per turn:** response time for one user/agent exchange (not per whole session).
- **Token spend:** tokens consumed by prompts and responses; the direct driver of LLM cost.
- **Hallucination rate:** fraction of responses containing fabricated or ungrounded content.

| Deployment strategy | Mechanism | Best when | Rollback |
|---|---|---|---|
| **Canary** | Fraction of **live traffic** goes to new version | You want real-traffic evidence with limited blast radius | Stop routing the fraction |
| **Blue-green** | Two full environments, traffic switched | You need **instant rollback** if anomalies arise | Flip traffic back to blue |

**Takeaway:** Test = unit tests *plus* golden-dataset evaluation. Deploy = canary or blue-green. Monitor = agent KPIs (latency/turn, token spend, hallucination), with drift feeding back to Build.

> **Gap [4.1]:** The exam guide expects ADK evalset, Gen AI evaluation service, custom autoraters and continuous evaluation pipelines. This module only names the golden dataset and the Agent Evaluation service conceptually. Golden-dataset evaluation and the CI quality gate are expanded in `04-evaluation-as-unit-tests.md` and `05-cicd-pipeline-quality-gates.md`; ADK evalset, autoraters and the evaluation service internals belong to the "Evaluate Agents" course.
>
> **Gap [4.2]:** The exam guide expects choosing a deployment runtime (Agent Runtime vs Cloud Run vs GKE) and troubleshooting drift, tool invocation latency and reasoning loops. This module introduces Agent Runtime only; Cloud Run/GKE comparison is **not in this source**.

---

## 3. Personas and roles [4.2]

**What/why:** AgentOps spans app code, models and infrastructure, so no single team owns it. The split follows the lifecycle.

| Persona | Lifecycle stages | Focus |
|---|---|---|
| **AI engineers and app developers** | **Build** | Write code, develop **prompt chains**, integrate the APIs agents use to act in the real world |
| **DevOps and MLOps engineers** | **Test, Deploy, Monitor** | Infrastructure and automation: **CI/CD pipelines**, configuring Google Cloud with **infrastructure as code (IaC)**, building **monitoring dashboards** |

Terms: **CI/CD** = continuous integration / continuous delivery (automated build-test-release pipelines). **IaC** = defining cloud infrastructure in versioned code/config instead of manual console clicks.

**Takeaway:** Developers build; DevOps/MLOps engineers test-automate, deploy and monitor.

---

## 4. Environments for agent development [4.2]

**What/why:** To ensure stability, code moves through a **sequence of isolated environments** so that failures are caught away from real users.

> **Gap:** The source capture shows three numbered environment markers, but their content was **collapsed and not captured** (items 1-3 empty). Re-capture that page to get the course's exact names.
>
> **Clarification (not in source):** The conventional promotion path is development → test/staging → production. The CI/CD lesson (`05-cicd-pipeline-quality-gates.md`) uses **dev → staging (mirrors production) → production**, which supports this, but the exact names on this page remain uncaptured. Verify against the course page.

---

## 5. Worked example: CymbalMart agent release [4.1][4.2]

| Step | Scenario (*illustrative*) | Cross-check against concept |
|---|---|---|
| Build | AI engineer writes a shopping agent, prompt templates, memory design, inventory tool integration; packaged as a container | Build output = containerized code ✔; persona = AI engineer/app developer ✔ |
| Test | Unit tests pass, but golden dataset shows the agent recommends out-of-stock items 8% of the time (*illustrative number*) | Unit tests alone would have missed it: evaluation-based testing ✔ |
| Deploy | Release the fixed version to 5% of live traffic (canary) | Fraction of live traffic = canary ✔. If the requirement were "instant rollback", blue-green fits instead |
| Monitor | Dashboard shows token spend per conversation rising; tracing shows a loop calling the same tool repeatedly | Agent KPI (token spend) beyond CPU/memory ✔; finding feeds back to Build ✔ |

---

## Common confusions

| Looks like | Difference |
|---|---|
| **AgentOps lifecycle (build, test, deploy, monitor)** vs **Agent Strategy lifecycle (discover, design, build, scale)** | Different courses, different frameworks. AgentOps = *operational* loop for one agent. The strategy framework = *program-level* transformation lifecycle; AgentOps is a horizontal capability in its outer layer and is established in its Scale stage |
| **AgentOps lifecycle stages** vs **platform pillars (build, scale, govern, optimize)** | Stages = *what you do over time*. Pillars = *groups of Google Cloud services* (see platform notes). They overlap in name only (build) |
| **DevOps vs MLOps vs AgentOps** | DevOps = deterministic code; MLOps = model drift/retraining; AgentOps = non-deterministic reasoning and tool use |
| **Unit test vs evaluation-based test** | Unit test checks code behaves as written; evaluation checks answer/reasoning quality against golden data |
| **Canary vs blue-green** | Canary = *fraction of live traffic*; blue-green = *two environments with instant switch-back* |
| **Infrastructure KPIs vs agent KPIs** | CPU/memory are infrastructure; latency per turn, token spend, hallucination rate are agent-specific |

Sources: AgentOps: Operationalize AI Agents on Google Cloud, "Defining the AgentOps lifecycle" (`sources/the-agent-lifecycle-raw.md`).
