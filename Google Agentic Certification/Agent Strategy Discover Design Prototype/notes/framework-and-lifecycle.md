# Agentic AI Transformation Framework and Lifecycle

**Module map:** The course's backbone: the three-layer framework, the four-stage development lifecycle (discover, design, build, scale), and each stage's activities, inputs and outputs. Covers **4.1, 4.2, 5.2** (build/scale stages touch evaluation, AgentOps, guardrails) and sets context for **3.1/3.3** (single vs multi-agent). This is a **strategy/process** course: the exam guide has no objective named after the framework, so treat this as scenario context. Start with `course-mindmap.md`. Next notes: `discover-design-value-prioritization.md`, `design-best-practices-cuj.md`.

---

## 1. Why the framework exists

**What/why:** Generative AI answers questions (chatbot). *Agentic AI* acts: it reasons, plans and executes tasks autonomously. Organizations rush into **disconnected pilots** (every department builds its own), creating **silos, technical debt and inconsistent governance**, so nothing scales. The **Agentic AI Transformation Framework** (Google Cloud) is a structured blueprint to move from isolated pilots to a cohesive "autonomous enterprise".

Terms defined:
- **Agent vs agentic system:** a simple agent does one thing at a time; an agentic AI system runs as an **autonomous workflow** (several agents/steps).
- **Agentic commerce:** retail example of the shift from *click-through* to *zero-click*: the customer delegates purchasing to an autonomous agent.
- **Technical debt:** future rework cost caused by shortcuts (here: fragmented, ungoverned pilots).

**Takeaway:** The framework is the antidote to fragmented pilots: prioritized project list tied to goals, roadmap that limits technical debt, and a success framework that proves ROI.

---

## 2. Three interconnected layers

```
┌───────────────────────────────────────────────────────────────┐
│ OUTER: foundation                                             │
│   Vertical industries: retail, healthcare, finance, automotive│
│   Horizontal capabilities: security, data platforms, AgentOps │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │ MIDDLE: agentic AI development lifecycle (iterative)    │  │
│  │   Discover → Design → Build → Scale  (loops back)       │  │
│  │  ┌───────────────────────────────────────────────────┐  │  │
│  │  │ CORE: strategy, ecosystems, value                 │  │  │
│  │  │   align agents to OKRs; 4 value drivers:          │  │  │
│  │  │   efficiency · cost · customer experience · revenue│ │  │
│  │  └───────────────────────────────────────────────────┘  │  │
│  └─────────────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────────────┘
```

| Layer | Role | Key content |
|---|---|---|
| **Core**: strategy, ecosystems, value | The compass: diagnose business challenges | Align agent potential to **OKRs** (objectives and key results); evaluate via four value drivers |
| **Middle**: development lifecycle | The engine: ideas become reality | 4 stages, iterative |
| **Outer**: foundation | What makes a pilot an enterprise capability | Verticals (industries) + horizontals (security, data platforms, **AgentOps** = agent operations) |

**Takeaway:** Core = *why* (value), middle = *how* (lifecycle), outer = *what enables it* (industry + security/data/AgentOps).

---

## 3. The four stages at a glance [4.1][4.2][5.2]

| Stage | One-line goal | Key activities | Inputs → Outputs |
|---|---|---|---|
| **1. Discover** | Reimagine processes: **as-is → what-if** | Define the outcome → design the agentic workflow → allocate tasks | (see discover note) → a fundamentally new business process |
| **2. Design** | De-risk; bridge vision to a working system | (a) Selection and prioritization (b) User-centric design (CUJ) (c) Rapid prototyping and validation | → detailed **user stories**, **functional architecture diagram**, **validated prototype** |
| **3. Build** | Construct single and multi-agent systems that are scalable and replicable | 5 steps (below) | APIs, data classification policies, working prototype → **codebase**, architecture diagrams, **CI plan** |
| **4. Scale** | Quality and alignment at enterprise scale (AgentOps) | 4 steps (below) | golden examples, tech landscape assessment → **quality framework docs, automated pipelines, KPI dashboards** |

### Stage 3 Build: the five steps [3.1][3.3][5.2]
Objective: scalable, easy-to-replicate agents with consistent quality checks and continuous monitoring; **simplest solution that works** (no "superbrain" if a specialized agent suffices).

| # | Step | Content |
|---|---|---|
| 1 | **System design and architecture** | Single agent vs multi-agent. Example: **hierarchical pattern**: a manager agent delegates to a sales-analyst agent and a logistics agent |
| 2 | **Development** | Pick tech stack (Gemini + **Agent Development Kit, ADK**), build the **reasoning loop**, add **memory**: *short-term* = remembers the current chat; *long-term* = learns from past interactions |
| 3 | **Evaluation** | Rigorous automated testing and continuous assessment against predefined quality metrics before full deployment |
| 4 | **Guardrail implementation** | Screening modules check **inputs and outputs**; example: agent tries a \$1M order when the limit is \$10K → **human-in-the-loop (HITL) approval** triggered |
| 5 | **Integration and monitoring** | Deploy into the environment, detailed logging: every decision tracked to optimize over time |

Roles in Build (course quiz): **AI/ML engineer** = leads design, development, optimization of the agent; **product manager** = owns the use-case backlog, represents user needs; **end user** = participates in **UAT** (user acceptance testing) and gives real-world feedback.

### Stage 4 Scale: the four steps [4.1][4.2]
Objective: a **scalable evaluation infrastructure**: move from "vibes-based testing" to a methodical approach; standardized metrics cover raw performance (e.g., response speed), **safety** and **alignment** with business goals.

| # | Step | Content |
|---|---|---|
| 1 | **Pick your targets** | Define success; automated systems test agents against **golden datasets** (perfect expert answers) |
| 2 | **Choose your method** | Compare responses to **ground truth**, or compare answers **side by side using a supervisor model** (a model acting as judge) |
| 3 | **Set the rhythm** | How often to check: every hour or every code update; automated pipelines **sample real interactions** and **log every production action** |
| 4 | **Monitor and alert** | Raw data → dashboards; accuracy below threshold → instant alert to the engineering team |

Deployment = integrate the agent into existing **CI/CD** (continuous integration / continuous delivery) pipelines.

```
Golden dataset ──► automated eval pipeline ──► KPI dashboard ──► threshold breach ──► alert ──► engineers fix
        ▲                                        ▲
 production sampling + action logs ──────────────┘
```

> **Gap:** Exam objective 4.1 expects ADK evalset, Gen AI evaluation service, custom autoraters, continuous evaluation of *tool execution*. The course only gives the conceptual evaluation loop (golden data, ground truth, supervisor model). Details belong in the "Evaluate Agents" and "AgentOps" course notes.
>
> **Clarification (not in source):** "Supervisor model comparing answers side by side" is the LLM-as-judge idea (an "autorater" in Google terms). Verify exact tool names in official docs.

**Takeaway:** Build = turn *what-if* into *how-to* with guardrails; Scale = prove it keeps working via golden data, sampling, dashboards, alerts, CI/CD.

---

## 4. Worked example: Cymbal Mart purchasing agent through Build and Scale

| Stage activity | Applied to the \$1M-order scenario | Cross-check |
|---|---|---|
| Design/architecture | Manager agent + supplier-ordering sub-agent | Hierarchical pattern from step 1 ✔ |
| Guardrail | Order limit \$10K; \$1M order → HITL approval | Guardrail on *action*, human at high-stakes decision ✔ (also golden rule 3, see `design-best-practices-cuj.md`) |
| Evaluation | Golden set of purchase scenarios incl. over-limit edge cases (*illustrative*) | Test before deployment ✔ |
| Monitoring | Log each order decision; alert if accuracy dips | Step 5 of Build + step 4 of Scale ✔ |

---

## Common confusions

| Looks like | Difference |
|---|---|
| Course's 3 modules (Discover, Design, Prototype) vs framework's 4 stages | Modules are *course chapters*. Prototyping is **phase 3 of the Design stage**, not a separate lifecycle stage. Build and Scale are only overviewed (inside the Discover module) |
| "Design" as a stage vs "design" as the CUJ step | Design **stage** = 3 phases (prioritize, user-centric design, prototype). CUJ is phase 2 |
| Agent Ops (agent operations) vs Scale stage | Scale stage is *where* AgentOps practices (evaluation, monitoring) are established |
| Golden dataset vs ground truth | Golden dataset = the curated set of expert answers; ground truth = the correct answer you compare a response to (the dataset supplies it) |
| HITL vs kill switch | HITL = human approval at defined decision points; kill switch = manual override to stop the agent entirely (see best-practices note) |

Sources: Agent Strategy course transcripts (`sources/DiscoverModule.md`).
