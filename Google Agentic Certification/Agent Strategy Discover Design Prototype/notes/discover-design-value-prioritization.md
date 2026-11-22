# Discover and Design: Reimagining, Value Engineering, Prioritization

**Module map:** How to go from "what could we build?" to "what do we build first?": the as-is → what-if mindset, value engineering (AIVE, four value levers), and the three prioritization models (2x2 matrix, RICE, MoSCoW) with the Cymbal Mart worked example. Covers scenario-style **strategy** content; indirect support for **4.1** (confidence/ROI measurement) and **5.2** (responsible-AI gate). Prerequisite: `framework-and-lifecycle.md`. Next: `design-best-practices-cuj.md`.

---

## 1. Stage 1 Discover: as-is → what-if

**What/why:** The common trap is wrapping a clunky manual process in a chatbot (e.g., making messy spreadsheets update faster). Discover asks: *if you built the business today with agents that can reason and act, would you still do it this way?* The output is a **fundamentally new process**, not an automated old one.

Three steps:

| Step | What you do | Example |
|---|---|---|
| **Define the outcome** | Core business goals | Zero stockouts; 30% faster resolution |
| **Design the agentic workflow** | Agents do heavy data gathering and repetitive actions; humans focus on high-value judgment | Agent audits inventory for thousands of items each morning |
| **Allocate tasks** | Classify every task: **fully automated / agent-assisted / human-only** | Auto: stock counts; assisted: reorder proposal; human-only: supplier negotiation (*illustrative*) |

### Cymbal Mart as-is → what-if (the course's running case)
Cymbal Mart = national retail chain, 1,500+ stores, revenue pressure from competition; vision = one proactive **digital concierge** combining shopping and customer service.

| Area | As-is | What-if (agentic) |
|---|---|---|
| Procurement and merchandising | Category managers set reorder points/planograms from static history → stockouts or overstock | Specialized agents watch real-time sales, social trends, local events; adjust stock, place optimized orders, rearrange products |
| Supply chain and inventory | Reactive: a network-ops engineer diagnoses and reroutes **after** a fault | Orchestrator **predicts** disruptions (weather, inventory dips) and acts preemptively: reroute, order parts, create POs |
| Customer service and selling | Customers browse manually; reps reactively process returns/FAQs | Concierge manages the whole lifecycle: proactive hyper-personalized suggestions, **zero-click purchase** on price drop, autonomous replacements/goodwill credits, real-time guidance to human reps for complex exceptions |

Customer-journey view used in the prototype module: **pre-purchase** (proactive discovery), **during purchase** (zero-click execution within approved price range), **post-purchase** (automated lifecycle: reroute delayed shipment, goodwill credit, vision-based return inspection).

**Takeaway:** Discover = redesign the process around agents; classify tasks as automated / assisted / human-only.

---

## 2. Stage 2 Design: three phases

```
Selection & prioritization ──► User-centric design (CUJ) ──► Rapid prototyping & validation
  value levers + priority         golden path + building           functional representation of the
  matrix (impact/feasibility)     blocks                           agent before production code
```

**User-centric building blocks of an agent:** *reasoning and planning* (how it thinks), *synthesizing* (how it reads information), *taking action* (how it uses tools). Example: a demand-sensing agent synthesizes social-media trends and local weather to produce a new shipping plan, not just sales numbers.

Design outputs: user stories, functional architecture diagram, **validated prototype**. Quiz answer: after identifying concepts, the next step is **value engineering assessment + priority matrix (impact vs feasibility)**, never "write production code immediately" or "digitize the as-is spreadsheet".

---

## 3. Value engineering [no direct objective; scenario context]

**What/why:** Moves from "I think it's a good idea" to quantified ROI so teams don't build agents "because they can". Google uses **AI Value Engine (AIVE)**: you feed it concepts; it researches the industry, defines value propositions and drafts a structured impact estimate. The engineering team's worry about "black-box ROI" is answered by transparent **value levers**.

### Four value levers (lens for every project)
Grouped: **internal operational focus** (efficiency, cost) vs **external market-facing impact** (customer experience, revenue).

| Lever | Question it asks | Course sub-label | Example |
|---|---|---|---|
| **Efficiency** | Reduce risk, centralize security, run operations better? | "Trust and governance" | Supply-chain agents |
| **Cost** | Lower **TCO** (total cost of ownership): eliminate tool sprawl, optimize infrastructure? | "Operate smarter" | Internal procurement agent |
| **Customer experience** | Shorten **time to value** (idea → deployed agent → realized impact); better experience? | "Innovate faster" | Digital concierge |
| **Revenue** | Create new revenue? | "Drive growth" | Party-planner agent that upsells full outfits |

> **Clarification (not in source):** The sub-labels in the lesson (e.g., customer experience = "time to value") read oddly against the generic lever names, and another lesson says supply-chain agents serve efficiency/cost while shopping agents serve revenue/CX. For the exam, use the generic meaning (internal vs external impact) and the examples; verify the sub-label mapping in current Google material.

### Results shown in the course (source's illustrative figures, not guarantees)
- 450 ideas run through AIVE → supply chain showed significant efficiency wins; **customer experience** was the clear ROI heavyweight.
- Similar Google Cloud transformations: up to **17% higher email open rate**, **7% boost in conversion** from hyper-personalized real-time recommendations; another example: "3,600 developer hours saved".

**Takeaway:** Value engineering scores each use case on four levers (internal: efficiency, cost; external: CX, revenue) and ties it to OKRs.

---

## 4. Prioritization: gate first, then three models

**Gate:** Before ranking, each project must align with Google's AI principles (as stated in the course: bold and responsible, socially beneficial, safe, sustainable). *Verify exact wording in official Google AI principles.* This is a go/no-go filter, not a score.

### The three models

| Model | Type | Question answered | Best for |
|---|---|---|---|
| **2x2 impact vs feasibility** | Visual categorization | "Where does each idea sit?" | Fast, high-level alignment ("gut check") |
| **RICE** | Quantitative stack rank | "In what order?" | Defending a roadmap, removing bias |
| **MoSCoW** | Scope governance | "What do we cut under pressure?" | Controlling scope creep, launch readiness |

#### 2x2 matrix
Vertical axis = **impact** (strategic value: revenue, cost savings, CX). Horizontal axis = **feasibility** (likelihood of successful implementation: **data readiness, technical complexity, AI model reliability**).

```
 Impact
  high │ STRATEGIC BETS      │ SMART WINS
       │ (high impact,       │ (high impact, high feasibility)
       │  harder to build)   │  → do first
  low  │ DROP                │ IMPROVEMENTS
       │ (low/low: remove)   │ (easy, lower impact)
       └─────────────────────┴────────────► Feasibility
          low                    high
```
Action: smart wins first; balance strategic bets with improvements; remove low/low.

#### RICE
**Score = (Reach × Impact × Confidence) ÷ Effort**

| Component | Meaning |
|---|---|
| **Reach** | How many customers will actually use the agent |
| **Impact** | Contribution to strategic goals, minimal → massive |
| **Confidence** | Percentage (e.g., 80%, 50%): how **predictable** the model is: reliably performs the task, stays in scope, honors security guardrails. "The AI special" |
| **Effort** | Total **person-months** to deliver |

- Confidence is a **subjective input** that quantifies certainty and guards against black-box ROI.
- **Pitfall:** overly optimistic confidence scores bias the ranking, favouring strategic bets on overstated ROI.

#### MoSCoW
| Letter | Meaning (as taught) |
|---|---|
| **M**ust-have | Non-negotiable, e.g., meeting safety guardrails |
| **S**hould-have | Important, but V1 can launch without |
| **C**ould-have | Nice-to-have if things go faster than expected |
| **W**on't-have | Explicitly out of the current scope |

**Takeaway:** 2x2 categorizes, RICE ranks with data, MoSCoW decides what to cut.

---

## 5. Worked example: choosing the prototype (Cymbal Mart)

Candidates (both target customer experience):
- **A** shopping agent as **party planner** (revenue + CX)
- **B** **proactive fulfillment orchestration** agent (cost + CX)

Principle used: reimagine the **front end** (visible, drives revenue) before fixing the back end.

| Model | A: party planner | B: fulfillment orchestration |
|---|---|---|
| 2x2 | **Smart win**: high impact (converts browsers → buyers); high feasibility (LLMs good at style/intent) | **Improvement**: back-end, reactive (fixes a delay, creates no new sale) |
| RICE | High reach and impact; confidence **75%** pending multimodal validation; effort relatively low | High confidence, lower strategic urgency |
| MoSCoW | **Should-have** for major holiday push, not must-have for immediate launch | **Should-have** for later |
| Decision | **Prototype first** | Follow later |

**Cross-check with illustrative RICE numbers** (*illustrative, not from source*):

| | Reach | Impact (1-3) | Confidence | Effort (person-months) | Score |
|---|---|---|---|---|---|
| A | 50,000 | 2 | 0.75 | 4 | 50,000×2×0.75÷4 = **18,750** |
| B | 20,000 | 1 | 0.90 | 6 | 20,000×1×0.90÷6 = **3,000** |

A wins despite B's higher confidence because reach, impact and low effort dominate: consistent with the 2x2 verdict ✔. Note A's confidence (75%) is the soft spot: inflating it would further overstate A, which is exactly the pitfall above.

---

## Common confusions

| Looks like | Difference |
|---|---|
| Smart win vs strategic bet | Both high impact; smart win is also highly feasible (data and APIs exist); bet is harder |
| Improvement vs smart win | Improvement is easy but lower impact |
| RICE confidence vs model accuracy | Confidence = your subjective certainty of the *expected result* (predictability, scope adherence, guardrail alignment); not a measured accuracy score |
| MoSCoW vs priority matrix | MoSCoW governs scope/requirements of a chosen project; matrix selects *which project* |
| Efficiency/cost vs CX/revenue | Internal operational vs external market-facing levers |
| AIVE vs RICE | AIVE estimates business value/ROI per idea; RICE ranks ideas using a formula |

Sources: Agent Strategy course transcripts (`sources/DiscoverModule.md`, `sources/DesignModule.md`, `sources/ProtototypeModule.md`).
