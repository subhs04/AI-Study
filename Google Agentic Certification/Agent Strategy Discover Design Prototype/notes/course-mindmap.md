# Course Mindmap: Agent Strategy (Discover, Design, Prototype)

**Module map:** Start here. One-page map of the whole course, how the notes connect, what is exam-relevant and what was deliberately stripped. Honest scoping: this is a **strategy/process course**; its direct overlap with `exam-guide.md` is modest (mostly scenario context for Sections 1, 4, 5). Read in the order listed in section 3.

---

## 1. Mindmap

```
AGENTIC AI TRANSFORMATION FRAMEWORK  (blueprint: pilots → autonomous enterprise)
│
├── WHY: disconnected pilots → silos, technical debt, inconsistent governance
│
├── 3 LAYERS
│   ├── Core: strategy · ecosystems · value  (OKRs; 4 levers: efficiency, cost, CX, revenue)
│   ├── Middle: lifecycle  Discover → Design → Build → Scale
│   └── Outer: verticals (retail...) + horizontals (security, data platforms, AgentOps)
│
├── 1 DISCOVER  "as-is → what-if"
│   └── define outcome → design agentic workflow → allocate tasks (auto / assisted / human-only)
│
├── 2 DESIGN   (de-risk)  ──────────────► notes: discover-design-value-prioritization
│   ├── a. Selection & prioritization
│   │     ├── Gate: AI principles
│   │     ├── Value engineering: AIVE → 4 levers (internal: efficiency, cost | external: CX, revenue)
│   │     └── Models: 2x2 (impact/feasibility) · RICE (R×I×C÷E) · MoSCoW (M/S/C/W)
│   ├── b. User-centric design ────────► notes: design-best-practices-cuj
│   │     ├── 4 golden rules: small/focused · rapid iteration · human-AI (HITL, kill switch) · scale-ready (IAM, modular, one data source)
│   │     └── CUJ = user + goal + tasks → golden path → AI opportunity (remove / augment / synthesize)
│   └── c. Rapid prototyping & validation ► notes: prototyping-tools-and-ucp
│         ├── Why: vision, experimentation, alignment, feasibility
│         ├── Tools: AI Studio (fast) · Gemini Enterprise (org hub) · Agent Platform (production)
│         ├── UCP: commerce agent protocol
│         ├── Walkthrough ───────────────► notes: gemini-enterprise-walkthrough
│         └── Reading list ──────────────► notes: prompt-engineering-tuning-grounding
│
├── 3 BUILD  (single/multi-agent) ──────► notes: framework-and-lifecycle
│   └── architecture → development (ADK, memory) → evaluation → guardrails (HITL) → integration & monitoring
│
└── 4 SCALE  (AgentOps)
    └── targets (golden data) → method (ground truth / supervisor model) → rhythm (sampling, logs) → monitor & alert → CI/CD
```

## 2. How the pieces connect (one running case)

```
Cymbal Mart (1,500 stores, revenue pressure)
  as-is → what-if (concierge, zero-click)
     │
  AIVE: CX/revenue = ROI heavyweight (450 ideas)
     │
  2x2 + RICE + MoSCoW: party planner = smart win; fulfillment orchestration = improvement
     │
  CUJ: event definition → intelligent curating → refinement & checkout
     │   agents: occasion matcher → party planner → occasion refiner
  Prototype: AI Studio → (scale) Gemini Enterprise / Agent Platform
     │
  Build (guardrails, HITL) → Scale (golden data, dashboards, CI/CD)
```

## 3. Reading order and exam relevance

| Order | Note | Exam relevance | Objectives |
|---|---|---|---|
| 1 | `framework-and-lifecycle.md` | Medium: Build/Scale steps echo evaluation, AgentOps, guardrails | 4.1, 4.2, 5.2 (3.1, 3.3 context) |
| 2 | `discover-design-value-prioritization.md` | Low-medium: scenario reasoning (which use case first, which model) | none direct |
| 3 | `design-best-practices-cuj.md` | Medium-high: HITL, kill switch, agent identity/least privilege, single-purpose agents | 5.1, 5.2, 3.3, 3.2, 4.1 |
| 4 | `prototyping-tools-and-ucp.md` | Medium: tool-selection scenarios | 1.1, 1.2, 3.1 |
| 5 | `gemini-enterprise-walkthrough.md` | Medium-high: data stores, identity, pre-built agents | 1.2, 1.1 |
| 6 | `prompt-engineering-tuning-grounding.md` | Medium-high: prompting, grounding vs fine-tuning, parameters | 1.1, 3.1, 3.2, 4.1 |

## 4. Likely exam-style scenario triggers (what to recognize)

| If the scenario says... | Think... |
|---|---|
| Many disconnected pilots, tech debt | Transformation framework: prioritize, govern centrally (rule 4) |
| Choose between use cases; high impact but needs data not yet ready | 2x2 → strategic bet, not smart win |
| Need an objective ranking; stakeholders biased | RICE (watch confidence inflation) |
| Scope creep before launch | MoSCoW |
| Agent can place high-value orders | HITL approval; guardrails on actions; kill switch |
| Each agent needs access to data | Agent as digital employee: own identity, least privilege |
| Accuracy dips in production | Golden dataset eval + sampling + dashboards + alert |
| Business user, no code, enterprise data | Gemini Enterprise; engineer, production tuning/AgentOps → Agent Platform; fast PM prototype → AI Studio |
| Model gives outdated facts | Grounding/RAG (not fine-tuning) |

## 5. Stripped as not exam-relevant
Character dialogue (Clara/Beth/Tomo), course intros/outros, repeated recaps, Cymbal Insurance (Bea/Ann/Ian) storyline, lab promos, QR-code references, UI click-by-click detail beyond the exam-relevant choices.

## Common confusions
See each note's table. Biggest cross-cutting ones: modules ≠ lifecycle stages; HITL ≠ kill switch; grounding ≠ fine-tuning; UCP ≠ A2A/MCP; AI Studio ≠ Gemini Enterprise ≠ Agent Platform.
