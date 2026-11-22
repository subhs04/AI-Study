# Design Best Practices and the Critical User Journey (CUJ)

**Module map:** The four golden rules for agent transformation, then the CUJ: the design artifact that turns a prioritized use case into an agent blueprint, with the full Cymbal Mart party-planner example (three stages, three agents). Covers **5.1/5.2** (agent identity, least privilege, HITL, kill switch, governance from day one), **3.3** (single-purpose vs multi-agent; hand-offs), **3.2** (single source of truth/grounding), **4.1** (performance baseline, feedback loops). Prerequisite: `discover-design-value-prioritization.md`. Next: `prototyping-tools-and-ucp.md`.

---

## 1. The four golden rules

**What/why:** Guardrails against technical debt and fragmentation when moving from a ranked list to an operating agent workforce.

| # | Rule | Practices | Objective hook |
|---|---|---|---|
| 1 | **Start small, think focused** | **Target quick wins** (discrete processes with clear, immediate ROI, e.g., vendor onboarding, financial-crime detection). **Single-responsibility agents**: narrow scope, one clear goal (broad prompts/wide scopes often *decrease accuracy*). **Iterative complexity**: start with simple *level-one* single-purpose agents before *level-three* multi-agent systems | 3.1, 3.3 |
| 2 | **Prioritize rapid iteration** | **Performance baseline**: build the first prototype with the **most capable model available** to set a high benchmark. **Continuous learning loops**: closed-loop feedback: capture outcomes of agent actions to retrain/fine-tune automatically. **Prototype to de-risk**: test assumptions with real users and confirm feasibility before full engineering | 3.1, 4.1 |
| 3 | **Optimize human-AI collaboration** | **Design for human strengths**: agents take repetitive, data-heavy work; humans keep judgment, creative problem solving, relationships. **HITL** (human-in-the-loop): humans at critical decision points (validating major outputs, approving high-stakes financial actions) for accountability and trust. **Kill switch**: manual override/shutdown to stop agents in reasoning loops or unsafe actions | 5.2 |
| 4 | **Build a scale-ready foundation** | Governance and security are **baked in from the discover stage**, "not a wrapper added at the end". **IAM**: treat every agent as a **digital employee** with its own managed account and **least-privilege** access. **Modular architecture**: components evolve independently; handles load growth. **Unified data strategy**: all agents use a **single source of truth**, grounding reasoning in verified enterprise data to curb hallucinations | 5.1, 3.2 |

```
Rule 1 (small)  ─►  Rule 2 (iterate, baseline model, feedback)  ─►  Rule 3 (HITL + kill switch)
                                  └────────────►  Rule 4 (IAM, modular, single data source) ◄────┘
```

> **Gap:** Exam objectives 5.1/3.2 expect **Agent Identity, Principal Access Boundary policies, Agent Gateway, Agent Registry, OAuth 2.0**. This course states only the *principle* (agent = digital employee, least privilege). Product-level detail comes from the governance modules; verify in official docs.
>
> **Clarification (not in source):** The source mentions "level-one" and "level-three" agents but does not define level two or the full level taxonomy; do not assume a definition.

Why "most capable model first" (Rule 2) is not a contradiction with cost control (exam scenario trap): it sets a *quality ceiling* for the prototype; later you can test cheaper/smaller models against that baseline. (*Interpretation, not stated in source.*)

**Takeaway:** Small and single-purpose; iterate with a strong baseline; humans + kill switch in the loop; identity, modularity and one data source from day one.

---

## 2. Critical User Journey (CUJ)

**What/why:** A CUJ is the combination of a user's **critical goal** and the **sequence of tasks** they take to achieve it. It is the fundamental design artifact translating "start small" into a step-by-step agent flow, and it forces design around the *journey* rather than product features, so agents deliver real user value and avoid wasted dev time and technical debt. The **golden path** = the end-to-end flow from first observation (e.g., low-stock alert) to final action (confirmed supplier order).

### Three components
| Component | Question | Example |
|---|---|---|
| **User** | Who (persona/role) | Retail customer; busy bride-to-be |
| **Goal** | Why (main problem/need) | "I need to plan a party" |
| **Tasks** | How (specific steps) | Search, compare, check availability, checkout |

Practice prompt from the course: "As a **[user]**, I want to **[goal]**" + list **tasks**, then ask how an agent could **remove or augment** one task to create a smart win.

### Finding AI opportunities along the golden path
An agent can:
1. **Remove friction**: automate repetitive steps
2. **Augment humans**: e.g., deep research
3. **Synthesize** disparate data into proactive, personalized recommendations

Aim: evolve from **reactive chatbot** → **proactive concierge** that anticipates needs.

### Limitation (engineer's caveat)
A CUJ is the **happy/golden path**. A single static design can miss **edge cases, non-happy paths and security risks** (where agents often fail). Mitigate with continuous user research and rigorous QA. [4.1][5.2]

**Takeaway:** CUJ = user + goal + tasks; map the golden path, then decide where agents remove, augment or synthesize; test beyond the happy path.

---

## 3. Worked example 1: wedding planner (curated look)

| | Traditional | Agentic |
|---|---|---|
| User / goal | Busy bride-to-be: "complete look for my mountain wedding" | same |
| Tasks | Search dresses → research mountain weather → find matching boots → check availability | Agent **synthesizes weather data + style history** → generates a **curated lookbook** → adds all items to cart in one step → user **visually fits/adjusts** outfit → agent **orders on user's behalf** |

---

## 4. Worked example 2: Cymbal Mart party planner (prototype CUJ)

User = customers, especially busy hosts. Goal = plan a **complete, budget-conscious, themed event** without the stress of cross-referencing multiple platforms.

| Stage | User tasks | Agent (AI role) | What it does |
|---|---|---|---|
| **1. Event definition** | Choose event type (birthday, wedding, housewarming, anniversary); set **budget**, **theme** (e.g., fairy-tale), **guest count**, **special requests** (e.g., backyard) | **Occasion matcher** (*augments*) | Uses natural language to understand intent ("outdoor 10-year-old girl's birthday") and fills the **conversation state** (guest count, budget) |
| **2. Intelligent curating** | Turn theme into a shopping list, compute quantities, handle dietary restrictions | **Party planner** (*transforms/replaces* manual search) | **Directed query expansion**: pulls relevant catalog products and **ranks by relevance** |
| **3. Refinement and checkout** | Adjust products, watch real-time budget total, buy | **Occasion refiner** (*integrates capabilities*) | Instantly **re-filters** (e.g., food category when an egg-allergy restriction is added) |

```
User intent ─► [Occasion matcher] ─► conversation state (budget, guests, theme)
                                          │
                                   [Party planner] ─► ranked product list (query expansion over catalog)
                                          │
                                   [Occasion refiner] ─► constraint changes ─► re-filter ─► checkout
```
Outcome: plan in **minutes vs hours**. Business results: **customer empowerment** (less cognitive burden) and **enterprise growth** (retention, larger basket size).

**Cross-check against the rules:** three narrow, single-responsibility agents (Rule 1 ✔); a prototype to validate (Rule 2 ✔); purchase only after user approval within a budget (Rule 3 ✔); agents share one catalog/data source and talk through a common protocol (Rule 4, see UCP in `prototyping-tools-and-ucp.md`) ✔. This is also a multi-agent setup: occasion matcher → party planner → occasion refiner (sequential hand-offs; **3.3** context).

---

## Common confusions

| Looks like | Difference |
|---|---|
| CUJ vs user story | CUJ = whole goal-driven journey (user, goal, tasks). User stories are a *Design stage output* derived from it (course lists both outputs; it does not define user story further) |
| HITL vs kill switch | HITL = approval at planned decision points; kill switch = emergency stop |
| Agent IAM vs end-user IAM | Agent is its own "digital employee" identity with least privilege, not the user's credentials (principle; product detail in governance notes) |
| Golden path vs golden dataset | Golden path = ideal user flow (design); golden dataset = expert answers used for evaluation (scale) |
| Rule 2 baseline (most capable model) vs final model | Baseline sets the benchmark; production model may differ |
| Single-responsibility agent vs multi-agent system | Rule 1: start with the former, evolve to the latter |

Sources: Agent Strategy course transcripts (`sources/DesignModule.md`, `sources/ProtototypeModule.md`).
