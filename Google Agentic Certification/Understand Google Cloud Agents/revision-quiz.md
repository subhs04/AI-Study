# Revision Quiz — Understand Google Cloud Agents

**Questions:** 28 (4 per note, across 7 notes)
Tap **Show Answer** on GitHub Mobile to check your recall.

---

## 1. Agent Foundations, Architecture & Platform Selection ([note](notes/01-agent-foundations-and-architecture.md))

**Q1 (Compare):** When should you choose **Grounding / RAG** vs. **Fine-tuning** for an enterprise agent?

<details>
<summary>Show Answer</summary>

**Answer:** Use **Grounding / RAG** when the agent needs live, verifiable enterprise or real-time public facts at runtime without retraining; use **fine-tuning** to adapt model weights on static training data for style or domain adaptation.
**Why it matters:** Fine-tuning cannot supply real-time facts (like live supplier hours) and goes stale as soon as source documents change.
**Ref:** `[1.2], [3.2]` · [01-agent-foundations-and-architecture.md](notes/01-agent-foundations-and-architecture.md)

</details>

**Q2 (Recall / Cloze):** What are the **three architectural pillars** of an AI agent, and what does **MCP (Model Context Protocol)** bundle that a traditional REST API does not?

<details>
<summary>Show Answer</summary>

**Answer:** **Models**, **Tools (MCP)**, and **Memory (active context management)**. Unlike a traditional REST API, MCP embeds rich semantic metadata and usage instructions alongside the endpoint—specifying *how* it is used, the *meaning* of inputs/outputs, and *when/where* to invoke it.
**Why it matters:** Self-describing tool semantics let the model discover and invoke external capabilities reliably without brittle hardcoding.
**Ref:** `[3.1], [3.2]` · [01-agent-foundations-and-architecture.md](notes/01-agent-foundations-and-architecture.md)

</details>

**Q3 (1-Line Trigger -> Choice):** A main agent manages a multi-stage invoice pipeline, passes only targeted context to a parsing specialist, and then resumes control. Which core agentic pattern is this, and how does it differ from the **Orchestrator (Router)** pattern?

<details>
<summary>Show Answer</summary>

**Answer:** **Sub-Agent Pattern** (selective context handoff in multi-stage pipelines, then resumes). By contrast, the **Orchestrator (Router) Pattern** uses an entry-point router to classify intent and forward/triage to the best downstream specialist.
**Why it matters:** Exam scenarios distinguish multi-stage pipeline delegation (Sub-Agent) from multi-domain intent triage (Orchestrator/Router).
**Ref:** `[3.3]` · [01-agent-foundations-and-architecture.md](notes/01-agent-foundations-and-architecture.md)

</details>

**Q4 (True / False):** *Conversational Agents (formerly Dialogflow CX) is a generative-first environment built on ADK, whereas CX Agent Studio is a deterministic state-machine-first platform.*

<details>
<summary>Show Answer</summary>

**Answer:** **False.** **Conversational Agents** is state-machine-first (with generative add-ons) for predictable, structured flows; **CX Agent Studio** is generative-first and built on ADK for open-ended, non-linear multi-agent customer journeys.
**Why it matters:** Reversing these two platforms is a classic platform-selection trap.
**Ref:** `[1.1], [3.1]` · [01-agent-foundations-and-architecture.md](notes/01-agent-foundations-and-architecture.md)

</details>

---

## 2. Gemini Enterprise Search, Assistant & Agentic Workflows ([note](notes/02-gemini-enterprise-search-and-workflows.md))

**Q5 (Recall / Cloze):** In Gemini's execution flow (`Prompt` → `Assistant / Search / Actions / Agents` ↔ `Data stores & External systems` → `Response`), what does the **Actions** stage do, and how are data permissions enforced?

<details>
<summary>Show Answer</summary>

**Answer:** **Actions** interfaces with external systems to execute side effects (e.g., sending emails, posting to Slack, updating a CRM/ERP via API). All search results and agent responses strictly enforce **source-system ACLs and RBAC**.
**Why it matters:** Grounded intranet search never bypasses underlying permissions—an analyst only sees public pitch summaries while a manager sees financial/IP models.
**Ref:** `[1.2]` · [02-gemini-enterprise-search-and-workflows.md](notes/02-gemini-enterprise-search-and-workflows.md)

</details>

**Q6 (Compare):** Distinguish **Agent Designer (Workflow Builder)**, **Agent Gallery**, and **AI agent finder**.

<details>
<summary>Show Answer</summary>

**Answer:** **Agent Designer** is the no-code visual tool to *build* internal agents (define purpose → connect data → natural-language instructions → iterate); **Agent Gallery** is the catalog where published internal agents are *discovered and accessed*; **AI agent finder** discovers *partner-made* third-party agents.
**Why it matters:** Knowing which tool builds internal no-code agents vs. finds third-party partner agents maps directly to enterprise workflow questions.
**Ref:** `[1.1], [1.2]` · [02-gemini-enterprise-search-and-workflows.md](notes/02-gemini-enterprise-search-and-workflows.md)

</details>

**Q7 (1-Line Trigger -> Choice):** An employee needs immediate, ready-to-use Google tools for (a) source-grounded research/writing and (b) comprehensive web + enterprise reports, without building a custom agent. Which **premade** Gemini agents fit?

<details>
<summary>Show Answer</summary>

**Answer:** (a) **Gemini Notebook** for source-grounded research and writing; (b) **Deep Research** for comprehensive web + enterprise reports.
**Why it matters:** Premade Google-built agents solve generic research and synthesis tasks out of the box without no-code or ADK development.
**Ref:** `[1.1], [1.2]` · [02-gemini-enterprise-search-and-workflows.md](notes/02-gemini-enterprise-search-and-workflows.md)

</details>

**Q8 (True / False):** *Gemini Enterprise agents can autonomously conduct live customer presentations, close deals, and negotiate final legal contract terms.*

<details>
<summary>Show Answer</summary>

**Answer:** **False.** High-stakes external commitments—conducting live customer presentations, automatically closing deals, and negotiating/finalizing legal terms—remain **human-owned**.
**Why it matters:** Agents automate research, synthesis, drafting, and routine operational actions, leaving final deal closure and legal negotiation to humans.
**Ref:** `[1.2]` · [02-gemini-enterprise-search-and-workflows.md](notes/02-gemini-enterprise-search-and-workflows.md)

</details>

---

## 3. Conversational Agents & CX Agent Studio ([note](notes/03-conversational-agents-and-cx-studio.md))

**Q9 (Compare):** In **Conversational Agents**, when should you use **Generative Fallback**, **Generators**, **Data Store Agents**, and **Playbooks**?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Generative Fallback:** Repairs `no-match` errors (intent/form-filling failures, interruptions).
- **Generators:** Gen AI prompts at **fulfillment** points (routes, event handlers, parameters) for summarization, Q&A, or extraction.
- **Data Store Agents:** Grounded FAQ service over websites/documents.
- **Playbooks:** Natural-language task orchestrators replacing rigid flows/intents when step order is flexible.
**Why it matters:** Each feature targets a distinct layer of the state machine (`no-match` repair vs. fulfillment hook vs. FAQ grounding vs. flexible flow).
**Ref:** `[1.1]` · [03-conversational-agents-and-cx-studio.md](notes/03-conversational-agents-and-cx-studio.md)

</details>

**Q10 (Recall / Cloze):** In Conversational Agents, what are **Snapshots**, and how do they differ from **Git Export/Restore**?

<details>
<summary>Show Answer</summary>

**Answer:** **Snapshots** are immutable point-in-time captures of flow/playbook versions used to promote across **Development, Staging, and Production** environments and perform rapid rollbacks. **Git Export/Restore** syncs configuration bidirectionally with external Git repositories for CI/CD and collaboration.
**Why it matters:** Platform-native promotion/rollback uses Snapshots + Environments, while external CI/CD pipelines use Git Export/Restore.
**Ref:** `[1.1], [4.2]` · [03-conversational-agents-and-cx-studio.md](notes/03-conversational-agents-and-cx-studio.md)

</details>

**Q11 (1-Line Trigger -> Choice):** A customer starts with device troubleshooting and mid-chat pivots to asking about enterprise bulk pricing, requiring a seamless handoff to a sales agent with troubleshooting context retained. Which CX platform fits best?

<details>
<summary>Show Answer</summary>

**Answer:** **CX Agent Studio** (uses generative-first routing and advanced state management across specialized agents in open-ended, non-linear journeys).
**Why it matters:** Conversational Agents excels at predictable, single-system structured flows; CX Agent Studio handles unpredictable cross-function pivots.
**Ref:** `[1.1], [3.3]` · [03-conversational-agents-and-cx-studio.md](notes/03-conversational-agents-and-cx-studio.md)

</details>

**Q12 (True / False):** *In CX Agent Studio, "Start with AI" only generates a system prompt; builders must manually create all agent nodes, tools, guardrails, and evaluation datasets.*

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Describing the desired agent, attaching grounding files, and clicking **Build a preview** generates the **root agent**, specialized **agent nodes**, **instructions**, **tools**, **guardrails**, and **evaluation datasets** (inspectable in **Build → Graph view**).
**Why it matters:** "Start with AI" scaffolds the entire multi-agent graph and evaluation baseline, not just a single prompt.
**Ref:** `[1.1], [3.3]` · [03-conversational-agents-and-cx-studio.md](notes/03-conversational-agents-and-cx-studio.md)

</details>

---

## 4. Gemini Platform & Enterprise Connectivity ([note](notes/04-gemini-platform-and-enterprise-connectivity.md))

**Q13 (Compare):** What is the difference between **Agent garden** and **Model Garden**?

<details>
<summary>Show Answer</summary>

**Answer:** **Agent garden** is a centralized library of pre-built *agent* blueprints/samples (with GitHub source code) and reusable tools; **Model Garden** is a catalog of **hundreds (100s) of curated third-party and open-source LLMs**.
**Why it matters:** Pick Agent garden to accelerate agent architecture from blueprints, and Model Garden to select specialized LLMs.
**Ref:** `[3.1]` · [04-gemini-platform-and-enterprise-connectivity.md](notes/04-gemini-platform-and-enterprise-connectivity.md)

</details>

**Q14 (Compare):** In the Gemini Platform, where do **inner-loop tools** live versus **outer-loop orchestration**?

<details>
<summary>Show Answer</summary>

**Answer:** **Inner-loop tools** (e.g., code interpreters, search) are exposed inside the **Gemini API**; **outer-loop orchestration** (multi-step reasoning, tool selection, multi-agent coordination) is managed by **ADK**.
**Why it matters:** Separates model-level execution tools (Gemini API) from application-level workflow orchestration (ADK).
**Ref:** `[3.1]` · [04-gemini-platform-and-enterprise-connectivity.md](notes/04-gemini-platform-and-enterprise-connectivity.md)

</details>

**Q15 (Recall / Cloze):** Name at least **four** production capabilities provided by **Agent runtime** (formerly Agent Engine).

<details>
<summary>Show Answer</summary>

**Answer:** Serverless auto-scaling, sessions & short-term memory, **long-term memory bank**, **Example Store** (few-shot examples), **Agentic Autorater** (automated QA), code execution/simulation, and trace logging.
**Why it matters:** Agent runtime is the managed hosting/execution layer—distinct from ADK, which is the open-source development framework.
**Ref:** `[3.1], [4.1], [4.2]` · [04-gemini-platform-and-enterprise-connectivity.md](notes/04-gemini-platform-and-enterprise-connectivity.md)

</details>

**Q16 (1-Line Trigger -> Choice):** A custom agent needs (a) live local business hours, (b) internal device usage logs and tables, and (c) proprietary enterprise APIs. Which connectivity/grounding options map to each?

<details>
<summary>Show Answer</summary>

**Answer:** (a) **Google Maps** for live business locations/hours; (b) **BigQuery Data Agents** for internal tables/usage logs; (c) **Apigee** (>800K custom APIs) and **90+ pre-built connectors** (e.g., Salesforce, SAP).
**Why it matters:** Maps handles real-time public location status, BigQuery Data Agents bridges warehouse tables, and Apigee/connectors expose enterprise systems.
**Ref:** `[3.2]` · [04-gemini-platform-and-enterprise-connectivity.md](notes/04-gemini-platform-and-enterprise-connectivity.md)

</details>

---

## 5. ADK Multi-Agent Architecture & Orchestration ([note](notes/05-adk-multi-agent-and-orchestration.md))

**Q17 (Recall / Cloze):** In the **Agent-to-Agent (A2A)** protocol, what endpoint does every A2A server automatically serve, and what **three** items does it declare?

<details>
<summary>Show Answer</summary>

**Answer:** `/.well-known/agent.json`, declaring **(1) Identity & Description**, **(2) Input Schema / Arguments**, and **(3) Output Schema**.
**Why it matters:** A2A decouples agents behind standard web contracts so orchestrators only need a URL (`remote_a2a_agent`), not shared code or languages.
**Ref:** `[3.3]` · [05-adk-multi-agent-and-orchestration.md](notes/05-adk-multi-agent-and-orchestration.md)

</details>

**Q18 (Compare):** Why does ADK pair a **Stateful Orchestrator** ("Master Clipboard") with **Stateless Specialists**?

<details>
<summary>Show Answer</summary>

**Answer:** The orchestrator holds centralized session state (with a pre-defined state schema), while specialist workers hold no state—receiving context, executing one task, and exiting. If a specialist crashes and restarts, **zero workflow progress is lost**.
**Why it matters:** Centralizing state in the orchestrator ensures fault tolerance and clean recovery across distributed agent microservices.
**Ref:** `[3.1], [3.3]` · [05-adk-multi-agent-and-orchestration.md](notes/05-adk-multi-agent-and-orchestration.md)

</details>

**Q19 (1-Line Trigger -> Choice):** Match each ADK requirement to its agent type: (a) fetch financial data, news, and competitor info simultaneously to cut latency; (b) repeat a research-to-judge cycle until a checker yields `escalate=True`; (c) dynamically route open-ended user chat.

<details>
<summary>Show Answer</summary>

**Answer:** (a) **Parallel agent**, (b) **Loop agent**, (c) **LLM agent**.
**Why it matters:** Sequential, Parallel, and Loop agents give deterministic control flow, while LLM agents handle dynamic routing.
**Ref:** `[3.3]` · [05-adk-multi-agent-and-orchestration.md](notes/05-adk-multi-agent-and-orchestration.md)

</details>

**Q20 (True / False):** *ADK's built-in evaluation only checks final answer quality, and proprietary financial allocation formulas should be computed directly by the LLM.*

<details>
<summary>Show Answer</summary>

**Answer:** **False.** ADK evaluates both **(1) final answer quality** and **(2) execution trajectory** (step-by-step reasoning and tool calls), and critical auditable math or state-mutating actions must live in **deterministic code tools**, never probabilistic LLM math.
**Why it matters:** Separating LLM reasoning from deterministic Python tools guarantees identical, auditable results on every run.
**Ref:** `[3.3], [4.1]` · [05-adk-multi-agent-and-orchestration.md](notes/05-adk-multi-agent-and-orchestration.md)

</details>

---

## 6. Agent Security, Identity & Governance ([note](notes/06-agent-security-and-governance.md))

**Q21 (Compare):** How do **Model Armor** and **Deterministic Tool Policies** complement each other in hybrid defense-in-depth?

<details>
<summary>Show Answer</summary>

**Answer:** **Model Armor** screens *prompts and responses* in real time (prompt injection/jailbreak detection, **Cloud DLP** PII masking/tokenization/redaction before LLM processing, and content safety classifiers). **Deterministic tool policies** run *outside* the model to intercept *tool/API calls* prior to execution against hard rules (e.g., blocking purchases > $500).
**Why it matters:** Because LLMs are non-deterministic, prompt/response screening alone is insufficient without hard pre-execution tool guardrails.
**Ref:** `[5.1], [5.2]` · [06-agent-security-and-governance.md](notes/06-agent-security-and-governance.md)

</details>

**Q22 (Compare):** Why is **Agent Identity** preferred over the **Default Service Account** (`AI Platform Reasoning Engine Service Agent`) on Agent runtime?

<details>
<summary>Show Answer</summary>

**Answer:** **Agent Identity** is automatically provisioned per individual agent, matches the agent's lifespan, **cannot be impersonated**, uses no long-lived keys, restricts tokens to the trusted runtime, and provides exact per-agent attribution in Cloud Audit Logs—whereas the Default SA is project-wide and shared.
**Why it matters:** Per-agent isolation prevents cross-agent privilege sharing and gives unambiguous audit trails.
**Ref:** `[5.1], [3.2]` · [06-agent-security-and-governance.md](notes/06-agent-security-and-governance.md)

</details>

**Q23 (1-Line Trigger -> Choice):** Choose the right credential type for each target: (a) reading a Cloud Storage bucket, (b) calling a third-party external API, (c) accessing an end user's Google Calendar on their behalf.

<details>
<summary>Show Answer</summary>

**Answer:** (a) **Service Account** (or Agent Identity) with least-privilege IAM roles; (b) **API Key** stored in **Secret Manager**; (c) **OAuth Client ID** (requires explicit end-user consent).
**Why it matters:** Never use OAuth Client ID for backend GCP bucket access or store raw API keys in code—match credential type to principal and target.
**Ref:** `[5.1], [3.2]` · [06-agent-security-and-governance.md](notes/06-agent-security-and-governance.md)

</details>

**Q24 (Recall / Cloze):** What are Google's **three core principles** for agent security, and what lifecycle threat occurs when an agent saves summarized "facts" from a malicious document into persistent memory?

<details>
<summary>Show Answer</summary>

**Answer:** Three principles: **(1) Well-defined human controllers**, **(2) Limited powers (Least Privilege)**, and **(3) Agent observability**. Saving tainted facts from a malicious document into persistent memory is **memory poisoning**.
**Why it matters:** Memory poisoning compromises future, unrelated interactions even after the original malicious document is gone.
**Ref:** `[5.1], [5.2]` · [06-agent-security-and-governance.md](notes/06-agent-security-and-governance.md)

</details>

---

## 7. Production Deployment, Scaling & Observability ([note](notes/07-production-deployment-and-observability.md))

**Q25 (Compare):** Match each production runtime—**Agent runtime**, **Cloud Run**, **GKE**, and **Compute Engine**—to its best-fit scenario.

<details>
<summary>Show Answer</summary>

**Answer:**
- **Agent runtime:** Default managed serverless path for ADK agents needing fast deploy + native A2A and memory.
- **Cloud Run:** Variable-traffic custom containers needing **scale-to-zero** pay-per-use and full dependency control.
- **GKE:** High-throughput, low-tail-latency workloads needing **GKE Inference Gateway**, custom **HPA**, or GPUs/TPUs.
- **Compute Engine:** Legacy/licensed software or rigid OS/kernel constraints (highest ops overhead, no scale-to-zero).
**Why it matters:** Picking GKE or Compute Engine when Cloud Run or Agent runtime suffices adds unnecessary operational overhead.
**Ref:** `[4.2]` · [07-production-deployment-and-observability.md](notes/07-production-deployment-and-observability.md)

</details>

**Q26 (Compare):** Which observability tool—**Cloud Logging**, **Cloud Trace**, or **Cloud Monitoring**—isolates whether a latency spike is caused by the **LLM call**, a **webhook tool**, or **memory retrieval**?

<details>
<summary>Show Answer</summary>

**Answer:** **Cloud Trace** (visualizes end-to-end latency spans across LLM calls, tool webhooks, and memory retrieval). **Cloud Logging** records discrete structured events/errors; **Cloud Monitoring** tracks QPS, error rates, and average latency.
**Why it matters:** Monitoring tells you *that* average latency spiked; Trace tells you *which step* inside the conversation caused it.
**Ref:** `[4.2]` · [07-production-deployment-and-observability.md](notes/07-production-deployment-and-observability.md)

</details>

**Q27 (Recall / Cloze):** Beyond infrastructure metrics, what **three** business/AI KPIs do **Agent-Specific Analytics** in Conversational Agents track?

<details>
<summary>Show Answer</summary>

**Answer:** **Escalation rates**, **conversation outcomes**, and **tool failure rates**.
**Why it matters:** Healthy QPS and low latency don't guarantee agent quality if escalation rates or tool failures are high.
**Ref:** `[4.2]` · [07-production-deployment-and-observability.md](notes/07-production-deployment-and-observability.md)

</details>

**Q28 (True / False):** *Least-privilege IAM roles alone prevent data exfiltration from production data stores and vector databases; network-level perimeters are redundant.*

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Production deployments pair IAM least privilege and scoped credentials with **VPC Service Controls** to create a network data perimeter around data stores and vector databases to block exfiltration.
**Why it matters:** Defense-in-depth requires both identity-based AuthZ (IAM) and network-boundary isolation (VPC Service Controls).
**Ref:** `[5.1], [5.2]` · [07-production-deployment-and-observability.md](notes/07-production-deployment-and-observability.md)

</details>
