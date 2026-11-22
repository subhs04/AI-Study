# Prototyping with Google Tools and UCP

**Module map:** Why prototype at all, which Google tool fits which user and maturity stage (Google AI Studio vs Gemini Enterprise vs Agent Platform/Vertex AI), and what UCP is. Covers **1.1/1.2** (Gemini Enterprise as low-code hub), **3.1** (platform choice), context for **3.3** (inter-agent protocols). Prerequisite: `design-best-practices-cuj.md`. Next: `gemini-enterprise-walkthrough.md`, `prompt-engineering-tuning-grounding.md`.

---

## 1. Why prototype when AI can already "vibe code" a product? [Design stage, phase 3]

| Reason | Meaning |
|---|---|
| **Vision creation** | Abstract idea → tangible product vision that inspires stakeholders |
| **Rapid experimentation** | Quick, low-cost tests of features before full development |
| **Stakeholder alignment** | Concrete communication tool; secures buy-in, clarifies scope |
| **Feasibility check** | De-risks the build by confirming the **core agent logic** is technically viable |

**Takeaway:** Prototype = vision, experiments, alignment, feasibility; it validates before you commit engineering.

---

## 2. Choosing the tool: three layers [1.1][1.2][3.1]

| | **Google AI Studio** (fast track) | **Gemini Enterprise** (enterprise hub) | **Vertex AI / Agent Platform** (engineering powerhouse) |
|---|---|---|---|
| Audience | Individual developers and PMs | Broader organization, business users | Engineers and technical PMs |
| Nature | **No-code springboard**: idea → functional agent in minutes; abstracts infrastructure; define goals, iterate prompts, test API integrations | Centralized "mission control" for AI productivity; secure all-in-one workspace with powerful models, **deep research tools** and **NotebookLM**; business users deploy agents for complex workflows; corporate data stays protected | Comprehensive platform for **build and scale** stages; production-grade: **custom model tuning, advanced agent ops, rigorous security guardrails** |
| Stage | Prototype (the party planner lab) | Deploy/use across the org | Production |
| Choose when | Speed, low risk, test the what-if | Business-user automation with enterprise data security | Heavy-duty infra, tuning, AgentOps, guardrails |

```
Idea ─► Google AI Studio (test what-if, low risk) ─┬─► Gemini Enterprise (business users, secure org-wide)
                                                   └─► Agent Platform / Vertex AI (engineers: build, scale, govern)
```
Google frames this as solving the **"product Darwinism"** problem (too many overlapping tools) by giving clear pathways per stage and user need.

> **Product note:** Vertex AI is now **Agent Platform** (per `AGENTS.md`). The transcript says "Vertex AI or agent platform".
>
> **Gap:** The exam guide (1.1) names **Gemini Enterprise Workflow Builder** and **CX Agent Studio** as the low-code tools for state-based workflows (pages, transition routes, event handlers). This course does **not** cover them; its low-code content is Gemini Enterprise's pre-built agents and NotebookLM (see walkthrough note). Revisit in the Agent Designer/Workflow Builder material.

**Takeaway:** AI Studio = fast individual prototyping; Gemini Enterprise = secure business-user hub; Agent Platform = production engineering.

---

## 3. UCP (agentic commerce protocol) [3.3 context]

**What it is (course):** Google's agentic-commerce documentation specifies **UCP**, the "foundational language" that lets different agents (occasion matcher, party planner, occasion refiner) **talk to each other and safely take actions across enterprise systems**, so they form a cohesive ecosystem rather than silos.

> **Clarification (not in source):** The transcript spells UCP as "Universal *Communication* Protocol". Google's published name is the **Universal *Commerce* Protocol**, an open standard for agentic commerce (merchant discovery, cart, checkout, post-purchase), designed to complement AP2 (Agent Payments Protocol), MCP and A2A. It is **not** in the exam guide's in-scope list (which names **A2A** and **MCP**). Treat as low priority; verify in official docs.

| Protocol | Scope (exam-relevant) |
|---|---|
| **MCP** | Agent ↔ tools/data (in scope, see other modules) |
| **A2A** | Agent ↔ agent (in scope, see other modules) |
| **UCP** | Commerce-domain standard (retail); not an exam tool |

**Takeaway:** UCP = common language for commerce agents; memorize only that it exists for agentic commerce interoperability.

---

## Common confusions

| Looks like | Difference |
|---|---|
| Google AI Studio vs Vertex AI Studio vs Agent Platform | AI Studio: lightweight no-code prototyping. Vertex AI Studio (older name, now within Agent Platform): enterprise prompt-to-production environment (see prompt note). Agent Platform: full engineering platform |
| Gemini Enterprise vs Gemini (consumer) | Gemini Enterprise = secure, org-wide workspace connected to company data with admin identity setup |
| Prototype vs production build | Prototype validates logic/value; build stage adds guardrails, evaluation, CI/CD |
| UCP vs A2A vs MCP | UCP commerce standard; A2A agent-to-agent; MCP agent-to-tool |

Sources: Agent Strategy course transcript (`sources/ProtototypeModule.md`); UCP name clarification from web search (verify).
