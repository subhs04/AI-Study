# Revision Quiz: Agent Strategy (Discover, Design, Prototype)

**Total questions:** 29 · **Notes covered:** 7
Tap **Show Answer** on GitHub Mobile to check your recall.

---

## 1. Course Mindmap ([note](notes/course-mindmap.md))

**Q1.** *(Recall)* What are the **three layers** of the Agentic AI Transformation Framework, and what is the role of each layer?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Core (Strategy, ecosystems, value):** The *why*—aligns agents to OKRs and four value drivers (efficiency, cost, CX, revenue).
- **Middle (Development lifecycle):** The *how*—iterative stages: Discover → Design → Build → Scale.
- **Outer (Foundation):** *What enables it*—vertical industries + horizontal capabilities (security, data platforms, AgentOps).

**Why it matters:** Keeps pilots from turning into disconnected silos by tying every agent to business value and shared enterprise foundations.
**Ref:** `[strategy]` · [course-mindmap.md](notes/course-mindmap.md)

</details>

**Q2.** *(1-Line Trigger → Choice)* An organization has many disconnected AI pilots creating silos, technical debt, and inconsistent governance. Which framework approach addresses this?

<details>
<summary>Show Answer</summary>

**Answer:** Apply the **Agentic AI Transformation Framework**—prioritize projects against OKRs/value levers and govern centrally from day one (Golden Rule 4: scale-ready foundation).
**Why it matters:** Exam scenarios with fragmented pilots and tech debt point to centralized governance, single source of truth, and prioritized roadmaps rather than ad-hoc building.
**Ref:** `[5.1, 5.2]` · [course-mindmap.md](notes/course-mindmap.md)

</details>

**Q3.** *(Spot the Trap — True/False)* The course's three modules (**Discover, Design, Prototype**) map 1-to-1 to the first three stages of the four-stage Agentic AI Development Lifecycle.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** The modules are *course chapters*. **Prototyping** is actually **Phase 3 of the Design stage**, not a separate lifecycle stage; **Build** and **Scale** are overviewed inside the Discover module.
**Why it matters:** Don't confuse course navigation with the four lifecycle stages (Discover → Design → Build → Scale).
**Ref:** `[strategy]` · [course-mindmap.md](notes/course-mindmap.md)

</details>

---

## 2. Agentic AI Transformation Framework and Lifecycle ([note](notes/framework-and-lifecycle.md))

**Q4.** *(Compare)* In Stage 3 (**Build**), how do **short-term memory** and **long-term memory** differ?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Short-term memory:** Remembers the **current chat** / session context.
- **Long-term memory:** Learns from **past interactions** across sessions.

**Why it matters:** Part of Step 2 (Development) alongside Gemini + ADK and the reasoning loop.
**Ref:** `[3.1]` · [framework-and-lifecycle.md](notes/framework-and-lifecycle.md)

</details>

**Q5.** *(Recall)* What are the **four steps** of Stage 4 (**Scale / AgentOps**) in order?

<details>
<summary>Show Answer</summary>

**Answer:**
1. **Pick your targets:** Test against **golden datasets** (expert answers).
2. **Choose your method:** Compare to **ground truth** or use a **supervisor model** side-by-side.
3. **Set the rhythm:** Sample real interactions and log every production action on a schedule (e.g., hourly or per code update).
4. **Monitor and alert:** Surface KPIs on dashboards and alert engineers on threshold breaches.

**Why it matters:** Moves evaluation from "vibes-based testing" to automated, continuous AgentOps integrated with CI/CD.
**Ref:** `[4.1, 4.2]` · [framework-and-lifecycle.md](notes/framework-and-lifecycle.md)

</details>

**Q6.** *(Compare)* **Golden dataset** vs. **Ground truth** — how do they differ in Scale-stage evaluation?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Golden dataset:** The curated collection of expert-authored test scenarios and answers.
- **Ground truth:** The specific correct/preferred answer you compare an agent's response against (supplied by the golden dataset).

**Why it matters:** Golden datasets are the evaluation asset; ground truth is the benchmark answer inside them.
**Ref:** `[4.1]` · [framework-and-lifecycle.md](notes/framework-and-lifecycle.md)

</details>

**Q7.** *(Spot the Trap — True/False)* In the Build stage, a **guardrail** only screens model outputs before they reach the user, whereas **HITL** is used to stop runaway reasoning loops.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Guardrails screen **both inputs and outputs** (and enforce action limits, e.g., triggering **HITL approval** if an agent tries a $1M order against a $10K limit). A **kill switch**—not HITL—is the manual override to stop runaway loops.
**Why it matters:** Exam questions often swap HITL (planned decision approval) with kill switch (emergency stop).
**Ref:** `[5.2]` · [framework-and-lifecycle.md](notes/framework-and-lifecycle.md)

</details>

---

## 3. Discover and Design: Value Engineering & Prioritization ([note](notes/discover-design-value-prioritization.md))

**Q8.** *(Recall)* In Stage 1 (**Discover**), every task in an agentic workflow is allocated into one of **three categories**. What are they?

<details>
<summary>Show Answer</summary>

**Answer:**
1. **Fully automated** (agent executes end-to-end)
2. **Agent-assisted** (agent prepares/proposes, human decides)
3. **Human-only** (high-value judgment, negotiation, relationships)

**Why it matters:** Discover is about reimagining *as-is → what-if*, not wrapping an old manual spreadsheet in a chatbot.
**Ref:** `[strategy]` · [discover-design-value-prioritization.md](notes/discover-design-value-prioritization.md)

</details>

**Q9.** *(Compare)* Group the **four value levers** used in value engineering (AIVE) into **internal operational focus** vs. **external market-facing impact**.

<details>
<summary>Show Answer</summary>

**Answer:**
- **Internal operational focus:** **Efficiency** (reduce risk, centralize governance, run operations better) and **Cost** (lower TCO, eliminate tool sprawl).
- **External market-facing impact:** **Customer experience (CX)** (faster time to value, better journey) and **Revenue** (new growth/upsell).

**Why it matters:** Value levers tie agent ROI to OKRs and counter engineering fears of "black-box ROI."
**Ref:** `[strategy]` · [discover-design-value-prioritization.md](notes/discover-design-value-prioritization.md)

</details>

**Q10.** *(Recall / Cloze)* State the **RICE** formula and explain what **Confidence** specifically measures in an AI/agent project.

<details>
<summary>Show Answer</summary>

**Answer:** **RICE = (Reach × Impact × Confidence) ÷ Effort** (person-months). **Confidence** (%) measures how **predictable** the model is: reliably performing the task, staying in scope, and honoring security guardrails.
**Why it matters:** Confidence is a subjective input ("the AI special"); inflating it biases rankings toward risky strategic bets on overstated ROI.
**Ref:** `[4.1 context]` · [discover-design-value-prioritization.md](notes/discover-design-value-prioritization.md)

</details>

**Q11.** *(1-Line Trigger → Choice)* Stakeholders are experiencing **scope creep** right before launch and need to decide which features to cut under pressure. Which prioritization model fits best—**2x2 matrix**, **RICE**, or **MoSCoW**?

<details>
<summary>Show Answer</summary>

**Answer:** **MoSCoW** (Must-have, Should-have, Could-have, Won't-have).
**Why it matters:** 2x2 categorizes ideas (Smart wins vs. Strategic bets), RICE stack-ranks them, and **MoSCoW** governs launch scope.
**Ref:** `[strategy]` · [discover-design-value-prioritization.md](notes/discover-design-value-prioritization.md)

</details>

---

## 4. Design Best Practices and the Critical User Journey ([note](notes/design-best-practices-cuj.md))

**Q12.** *(Recall)* List the **four golden rules** for agent transformation.

<details>
<summary>Show Answer</summary>

**Answer:**
1. **Start small, think focused** (quick wins, single-responsibility agents).
2. **Prioritize rapid iteration** (strong baseline model, continuous feedback loops, prototype to de-risk).
3. **Optimize human-AI collaboration** (HITL at critical decisions + kill switch).
4. **Build a scale-ready foundation** (governance from day one, agent IAM, modular architecture, single source of truth).

**Why it matters:** These four rules map directly to single-vs-multi-agent design (3.3), grounding (3.2), and security/governance (5.1, 5.2).
**Ref:** `[3.1, 3.2, 3.3, 5.1, 5.2]` · [design-best-practices-cuj.md](notes/design-best-practices-cuj.md)

</details>

**Q13.** *(Spot the Trap — True/False)* Under Golden Rule 2 (**Prioritize rapid iteration**), you should build your first prototype with the **most capable model available** to set a high performance baseline.

<details>
<summary>Show Answer</summary>

**Answer:** **True.** Using the most capable model first establishes a quality benchmark; you can test smaller/cheaper models against that baseline later.
**Why it matters:** Exam trap—don't mistake "start with the most capable model for prototyping" as contradicting production cost optimization.
**Ref:** `[3.1, 4.1]` · [design-best-practices-cuj.md](notes/design-best-practices-cuj.md)

</details>

**Q14.** *(Compare)* **HITL (Human-in-the-Loop)** vs. **Kill switch** — what is the difference?

<details>
<summary>Show Answer</summary>

**Answer:**
- **HITL:** Planned human review/approval at critical decision points (e.g., high-stakes financial actions).
- **Kill switch:** Manual emergency override/shutdown to stop an agent in a reasoning loop or unsafe action.

**Why it matters:** Both are required under Golden Rule 3 for accountability, trust, and runtime safety.
**Ref:** `[5.2]` · [design-best-practices-cuj.md](notes/design-best-practices-cuj.md)

</details>

**Q15.** *(Recall)* Under Golden Rule 4 (**Build a scale-ready foundation**), how should an agent's **IAM identity** be modeled?

<details>
<summary>Show Answer</summary>

**Answer:** Treat every agent as a **digital employee** with its own managed account and **least-privilege** access—not using the end user's credentials.
**Why it matters:** Principle behind Agent Identity and Principal Access Boundary policies in Objective 5.1.
**Ref:** `[5.1]` · [design-best-practices-cuj.md](notes/design-best-practices-cuj.md)

</details>

**Q16.** *(Compare)* **Critical User Journey (CUJ) / Golden path** vs. **Golden dataset** — how do they differ, and what is a key limitation of relying solely on a CUJ?

<details>
<summary>Show Answer</summary>

**Answer:**
- **CUJ / Golden path:** Design artifact mapping **user + goal + tasks** along the ideal end-to-end flow.
- **Golden dataset:** Curated expert answers used for automated evaluation in Scale.
- **CUJ limitation:** It only models the **happy path**, so it can miss edge cases, non-happy paths, and security risks unless paired with continuous user research and QA.

**Why it matters:** Exam scenarios often test where happy-path designs fail in production.
**Ref:** `[4.1, 5.2]` · [design-best-practices-cuj.md](notes/design-best-practices-cuj.md)

</details>

---

## 5. Prototyping with Google Tools and UCP ([note](notes/prototyping-tools-and-ucp.md))

**Q17.** *(1-Line Trigger → Choice)* Match each scenario to the right tool (**Google AI Studio**, **Gemini Enterprise**, or **Vertex AI / Agent Platform**):
- (a) Individual developer or PM needing a fast, no-code springboard in minutes
- (b) Business users deploying agents across the org with corporate data protection
- (c) Engineers needing custom model tuning, advanced AgentOps, and rigorous security guardrails

<details>
<summary>Show Answer</summary>

**Answer:**
- (a) **Google AI Studio** (fast-track prototyping)
- (b) **Gemini Enterprise** (secure enterprise hub)
- (c) **Vertex AI / Agent Platform** (production engineering powerhouse)

**Why it matters:** Google's three-layer tool choice resolves "product Darwinism" by matching tool to user persona and lifecycle stage.
**Ref:** `[1.1, 1.2, 3.1]` · [prototyping-tools-and-ucp.md](notes/prototyping-tools-and-ucp.md)

</details>

**Q18.** *(Recall)* What are the **four reasons** to build a prototype in Design Phase 3 even when AI can "vibe code" a product?

<details>
<summary>Show Answer</summary>

**Answer:**
1. **Vision creation** (tangible product vision)
2. **Rapid experimentation** (low-cost feature tests)
3. **Stakeholder alignment** (buy-in and clear scope)
4. **Feasibility check** (de-risks core agent logic before engineering)

**Why it matters:** Prototyping validates logic and value before committing to full Build-stage engineering.
**Ref:** `[strategy]` · [prototyping-tools-and-ucp.md](notes/prototyping-tools-and-ucp.md)

</details>

**Q19.** *(Compare)* **UCP** vs. **A2A** vs. **MCP** — what is the scope of each protocol, and which ones are in scope for the exam?

<details>
<summary>Show Answer</summary>

**Answer:**
- **MCP:** Agent ↔ tools/data (**in exam scope**).
- **A2A:** Agent ↔ agent (**in exam scope**).
- **UCP (Universal Commerce Protocol):** Retail/commerce-domain standard for agentic commerce (**not** in the exam guide's in-scope tool list).

**Why it matters:** Don't pick UCP as a general-purpose agent-to-agent or agent-to-tool protocol on the exam—use A2A and MCP.
**Ref:** `[3.3 context]` · [prototyping-tools-and-ucp.md](notes/prototyping-tools-and-ucp.md)

</details>

**Q20.** *(Spot the Trap — True/False)* Gemini Enterprise's **pre-built agents** and **NotebookLM** are the low-code tools named in Exam Objective 1.1 for building state-based workflows with pages, transition routes, and event handlers.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Exam Objective 1.1 names **Gemini Enterprise Workflow Builder** (formerly Agent Designer) and **CX Agent Studio** for state-based workflows—those are not covered in this course.
**Why it matters:** Pre-built agents are ready-made; Workflow Builder / CX Agent Studio are for configuring custom state-based flows.
**Ref:** `[1.1]` · [prototyping-tools-and-ucp.md](notes/prototyping-tools-and-ucp.md)

</details>

---

## 6. Gemini Enterprise Walkthrough ([note](notes/gemini-enterprise-walkthrough.md))

**Q21.** *(Recall)* What are the **four setup steps** for a Gemini Enterprise app, and what must be confirmed *before* sharing the app URL?

<details>
<summary>Show Answer</summary>

**Answer:**
1. Create app (name it in Cloud Console).
2. Set up identity (choose Google Identity → **Confirm Workforce Identity** *before* sharing).
3. Connect data stores (Drive, Calendar, Cloud Storage, etc.).
4. Share the unique URL with the workforce.

**Why it matters:** Identity controls *who* can sign in; data stores control *what* internal content the app can search.
**Ref:** `[1.2]` · [gemini-enterprise-walkthrough.md](notes/gemini-enterprise-walkthrough.md)

</details>

**Q22.** *(1-Line Trigger → Choice)* When connecting a **Cloud Storage** bucket of static PDF, HTML, and TXT files to Gemini Enterprise, what **Data type** and **Sync frequency** should you choose?

<details>
<summary>Show Answer</summary>

**Answer:** Data type = **Documents**; Sync frequency = **One time**.
**Why it matters:** Unstructured static files (PDF/HTML/TXT) map to Documents and a one-time ingest rather than recurring sync.
**Ref:** `[1.2]` · [gemini-enterprise-walkthrough.md](notes/gemini-enterprise-walkthrough.md)

</details>

**Q23.** *(Compare)* **Idea Generation** vs. **Deep Research** pre-built agents in Gemini Enterprise — when do you use each, and what common pattern do both follow?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Idea Generation:** Team of agents that plans, generates, and evaluates ideas on a topic.
- **Deep Research:** In-depth analysis producing a comprehensive **report + audio summary** on complex topics.
- **Common pattern:** **Plan → human review/modify → execute** (light HITL).

**Why it matters:** Shows low-code built-in HITL before an agent runs multi-step work.
**Ref:** `[1.1]` · [gemini-enterprise-walkthrough.md](notes/gemini-enterprise-walkthrough.md)

</details>

**Q24.** *(Compare)* **NotebookLM** vs. **Gemini Enterprise chat** — how do their data sources and search scope differ?

<details>
<summary>Show Answer</summary>

**Answer:**
- **NotebookLM:** Answers strictly from the **sources you upload** (Google Docs, URLs, YouTube, text, local files) and converts them in the Studio tab (audio/video overviews, mind maps, reports).
- **Gemini Enterprise chat:** Searches **connected enterprise data stores** (and optionally external sources, toggleable via *Enhance your conversation*).

**Why it matters:** Use NotebookLM for source-grounded personal/team synthesis; use Gemini Enterprise chat for org-wide data-store search.
**Ref:** `[1.1, 1.2]` · [gemini-enterprise-walkthrough.md](notes/gemini-enterprise-walkthrough.md)

</details>

---

## 7. Prompt Engineering, Model Parameters, Grounding and Tuning ([note](notes/prompt-engineering-tuning-grounding.md))

**Q25.** *(Compare)* **Zero-shot** vs. **Few-shot** vs. **Chain-of-thought** prompting — how do they differ?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Zero-shot:** Task only (no examples).
- **Few-shot:** Task + **examples** showing desired format/behavior.
- **Chain-of-thought:** Prompting the model to **reason step by step**.

**Why it matters:** Core prompt-engineering techniques in Objective 1.1; combine with role/context (system instructions) and delimiters for complex tasks.
**Ref:** `[1.1]` · [prompt-engineering-tuning-grounding.md](notes/prompt-engineering-tuning-grounding.md)

</details>

**Q26.** *(Compare)* **Temperature** vs. **Top-K** vs. **Top-P** — how does each control token sampling, and what is **Top-K's** weakness?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Temperature:** Reshapes overall randomness (low = predictable for Q&A/summarization; high = creative).
- **Top-K:** Fixed-size shortlist of the **K** most probable tokens. **Weakness:** On a skewed distribution (e.g., 80% / 10%), a fixed K can still pick a poor token.
- **Top-P:** Dynamic-size shortlist—smallest set whose cumulative probability ≥ **P**.

**Why it matters:** Top-P adapts to distribution confidence, whereas Top-K is a rigid count.
**Ref:** `[3.1]` · [prompt-engineering-tuning-grounding.md](notes/prompt-engineering-tuning-grounding.md)

</details>

**Q27.** *(1-Line Trigger → Choice)* A model gives **outdated facts** or lacks private company knowledge. Should you use **Grounding/RAG** or **Supervised Fine-Tuning (SFT)**?

<details>
<summary>Show Answer</summary>

**Answer:** **Grounding/RAG** (links the model to external, real-time or private data at response time without retraining).
**Why it matters:** Use SFT to teach a consistent *skill/format* from hundreds of labeled examples—not to inject changing facts.
**Ref:** `[3.1, 3.2]` · [prompt-engineering-tuning-grounding.md](notes/prompt-engineering-tuning-grounding.md)

</details>

**Q28.** *(Recall / Cloze)* List the **three levels** of the model-customization ladder (lowest to highest compute/cost), and state the dataset **file format** required for **Supervised Fine-Tuning (SFT)**.

<details>
<summary>Show Answer</summary>

**Answer:**
1. **Prompt design** (no parameter changes; lowest cost)
2. **Parameter-efficient tuning / adapter tuning** (updates a small subset of parameters)
3. **Full fine-tuning** (updates all parameters; highest tune and serve cost)
- **SFT data format:** **JSONL** (each row = input text prompt + output text expected response).

**Why it matters:** Always climb the ladder only as far as needed; SFT requires hundreds of labeled JSONL examples and deploys via Model Registry to an endpoint.
**Ref:** `[3.1]` · [prompt-engineering-tuning-grounding.md](notes/prompt-engineering-tuning-grounding.md)

</details>

**Q29.** *(Spot the Trap — True/False)* **Grounding** and **RAG** are identical—grounding always means running a vector-search RAG pipeline that updates the model's internal weights.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** **Grounding** is the *concept* of checking responses against reliable external data (e.g., Google real-time search or your own data), and **RAG** is *one implementation* of grounding. Neither changes model weights (unlike fine-tuning).
**Why it matters:** Classic exam distinction: Grounding = goal/concept; RAG = retrieval implementation; Fine-tuning = weight updates.
**Ref:** `[3.1, 3.2]` · [prompt-engineering-tuning-grounding.md](notes/prompt-engineering-tuning-grounding.md)

</details>
