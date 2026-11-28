# AgentOps: Operationalize AI Agents on Google Cloud — Revision Quiz

**44 active-recall questions** across 11 notes.
Tap **Show Answer** on GitHub Mobile to check your recall.

---

## 1. AgentOps: Definition and Lifecycle ([note](notes/01-agentops-lifecycle.md))

**Q1. [Compare]** How do **DevOps**, **MLOps**, and **AgentOps** differ in primary focus and key challenge?

<details>
<summary>Show Answer</summary>

**Answer:**
- **DevOps:** App code & infrastructure → predictability and uptime (deterministic delivery).
- **MLOps:** Predictive models & data pipelines → model drift and retraining.
- **AgentOps:** App code + AI models + tools → **non-deterministic reasoning** and unpredictable tool-use patterns.

**Why it matters:** Agents can take different execution paths for the same input, so deterministic-only checks aren't enough.
**Ref:** `[4.2]` · [01-agentops-lifecycle.md](notes/01-agentops-lifecycle.md)

</details>

**Q2. [Recall / Cloze]** Name the **four stages** of the AgentOps lifecycle in order, and list the **three agent-specific KPIs** tracked in the Monitor stage.

<details>
<summary>Show Answer</summary>

**Answer:**
- **Stages:** **Build → Test → Deploy → Monitor** (iterative feedback loop back to Build).
- **Agent KPIs:** **Latency per turn**, **token spend**, and **hallucination rate**.

**Why it matters:** Monitoring only CPU/memory misses silent quality and cost regressions.
**Ref:** `[4.2][4.1]` · [01-agentops-lifecycle.md](notes/01-agentops-lifecycle.md)

</details>

**Q3. [1-Line Trigger -> Choice]** You need **instant rollback** by switching traffic back if anomalies appear, rather than routing a small fraction of live traffic first. Choose **Canary release** or **Blue-green deployment**.

<details>
<summary>Show Answer</summary>

**Answer:** **Blue-green deployment**.
**Why it matters:** Blue-green runs two full environments side by side for instant switch-back; canary routes a small fraction of live traffic first.
**Ref:** `[4.2]` · [01-agentops-lifecycle.md](notes/01-agentops-lifecycle.md)

</details>

**Q4. [True / False]** *Spot the Trap:* "In the AgentOps lifecycle, AI engineers and app developers own Build, Test, Deploy, and Monitor end-to-end."

<details>
<summary>Show Answer</summary>

**Answer:** **False.** AI engineers and app developers own **Build** (code, prompt chains, API integration); **DevOps and MLOps engineers** own **Test, Deploy, and Monitor** (CI/CD, IaC, dashboards).
**Why it matters:** Knowing the persona split helps map automation and IaC responsibilities on scenario questions.
**Ref:** `[4.2]` · [01-agentops-lifecycle.md](notes/01-agentops-lifecycle.md)

</details>

---

## 2. AgentOps on Google Cloud: Build and Scale Pillars ([note](notes/02-build-and-scale-platform.md))

**Q5. [Compare]** **Agent Development Kit (ADK)** vs. **Agent Studio** — how do they differ in interface, core strength, and model lock-in?

<details>
<summary>Show Answer</summary>

**Answer:**
- **ADK:** Programmatic, **model-agnostic** code framework; defines deterministic workflows, memory state, and tool constraints as code.
- **Agent Studio:** **Low-code visual canvas**; fast prototyping, prompt templates, and native enterprise data grounding (e.g., BigQuery, Google Workspace).

**Why it matters:** Choose ADK for code-level control, versioning, and CI/CD; choose Agent Studio for rapid low-code prototyping.
**Ref:** `[3.1]` · [02-build-and-scale-platform.md](notes/02-build-and-scale-platform.md)

</details>

**Q6. [Compare]** **Agent Sessions** vs. **Agent Memory Bank** — what scope, stored data, and memory type does each represent?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Agent Sessions (short-term):** **One interaction/conversation**; turn-by-turn history, cart items, shipping address, applied codes.
- **Agent Memory Bank (long-term):** **Across interactions (weeks or months)**; user purchase history, preferences, loyalty data.

**Why it matters:** Missing Sessions makes an agent forget two turns ago; missing Memory Bank makes it treat a returning user as a stranger.
**Ref:** `[3.1]` · [02-build-and-scale-platform.md](notes/02-build-and-scale-platform.md)

</details>

**Q7. [1-Line Trigger -> Choice]** An agent dynamically generates and executes a Python pricing script or automates a GUI workflow. Which **Scale pillar** service should run it?

<details>
<summary>Show Answer</summary>

**Answer:** **Agent Sandbox**.
**Why it matters:** Agent Runtime hosts and scales the *agent*; Agent Sandbox isolates *untrusted/generated code and GUI automation* from core production systems.
**Ref:** `[2.1][4.2]` · [02-build-and-scale-platform.md](notes/02-build-and-scale-platform.md)

</details>

**Q8. [Recall / Cloze]** What was **Agent Runtime** formerly called, and what are its key runtime capabilities for handling agent traffic?

<details>
<summary>Show Answer</summary>

**Answer:** Formerly **Agent Engine**. It is a fully managed, serverless environment with **sub-second starts**, **autoscaling** up/down, and **stateful support for long-running workloads**.
**Why it matters:** Product rename trap on the exam: Agent Runtime = Agent Engine.
**Ref:** `[4.2]` · [02-build-and-scale-platform.md](notes/02-build-and-scale-platform.md)

</details>

---

## 3. AgentOps on Google Cloud: Govern and Optimize Pillars ([note](notes/03-govern-and-optimize-platform.md))

**Q9. [Compare]** **Agent Registry** vs. **Agent Gateway** vs. **Agent Identity** — what distinct question does each Govern service answer, and what open standard backs Agent Identity?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Agent Registry:** *What agents/tools exist, who owns them, and who may consume them?* (Catalog, contracts, lineage).
- **Agent Gateway:** *How does traffic flow, and is content safe?* (Centralized routing + inline Model Armor).
- **Agent Identity:** *Who exactly performed this call?* (Cryptographic ID per agent backed by the **SPIFFE** open standard for non-repudiation).

**Why it matters:** Together they enforce catalog contracts, network/content boundaries, and tamper-proof audit trails.
**Ref:** `[5.1][5.2][3.2]` · [03-govern-and-optimize-platform.md](notes/03-govern-and-optimize-platform.md)

</details>

**Q10. [Recall / Cloze]** Complete the **Optimize pillar** memory hook:
- **Agent Observability** = ______
- **Agent Evaluation** = ______
- **Agent Simulation** = ______
- **Agent Optimizer** = ______

<details>
<summary>Show Answer</summary>

**Answer:**
- **Observability** = what it *did*
- **Evaluation** = how *well* it did
- **Simulation** = what it *might* do
- **Optimizer** = doing it *better next time*

**Why it matters:** Pre-release uses Simulation + Evaluation; production uses Observability -> Optimizer in a continuous loop.
**Ref:** `[4.1][4.2]` · [03-govern-and-optimize-platform.md](notes/03-govern-and-optimize-platform.md)

</details>

**Q11. [1-Line Trigger -> Choice]** Before releasing a new agent version, you want to generate **synthetic traffic in a sandbox** to catch edge cases and looping errors early. Which Optimize service do you use?

<details>
<summary>Show Answer</summary>

**Answer:** **Agent Simulation**.
**Why it matters:** Simulation stress-tests with synthetic traffic; Evaluation scores outputs against baselines; Observability traces live runs.
**Ref:** `[4.1][4.2]` · [03-govern-and-optimize-platform.md](notes/03-govern-and-optimize-platform.md)

</details>

**Q12. [True / False]** *Spot the Trap:* "Agent Gateway and Model Armor are interchangeable names for the same Google Cloud service."

<details>
<summary>Show Answer</summary>

**Answer:** **False.** **Model Armor** is the sanitization/filter policy engine (blocking prompt injection, toxicity, data leakage); **Agent Gateway** is the traffic routing layer that natively applies Model Armor policies in transit.
**Why it matters:** Exam scenarios distinguish the filter engine from the network boundary enforcing it.
**Ref:** `[5.1][5.2]` · [03-govern-and-optimize-platform.md](notes/03-govern-and-optimize-platform.md)

</details>

---

## 4. Agent Evaluation as Unit Tests ([note](notes/04-evaluation-as-unit-tests.md))

**Q13. [Recall / Cloze]** What **three fields** make up each record in a **golden dataset** (ground truth)?

<details>
<summary>Show Answer</summary>

**Answer:** **User prompt**, **ideal response**, and **expected tool sequence**.
**Why it matters:** Having both ideal response and expected tool sequence lets CI grade both answer quality and reasoning path.
**Ref:** `[4.1]` · [04-evaluation-as-unit-tests.md](notes/04-evaluation-as-unit-tests.md)

</details>

**Q14. [Compare]** **Traditional unit test** vs. **Agent evaluation** — how do they differ in assertion style and result?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Traditional unit test:** Exact-match assertion (e.g., `get_price(...) == 199.99`) → binary pass/fail; breaks on valid phrasing variations.
- **Agent evaluation:** Score against a rubric compared with a **threshold**; handles open-ended phrasing variations and checks both reasoning path and final response.

**Why it matters:** Exact string matching fails valid natural-language responses in CI.
**Ref:** `[4.1]` · [04-evaluation-as-unit-tests.md](notes/04-evaluation-as-unit-tests.md)

</details>

**Q15. [1-Line Trigger -> Choice]** An agent gives the right final price (`$199.99`) by guessing from memory **without calling `get_price`**. Which of the two CI evaluation lenses catches this failure?

<details>
<summary>Show Answer</summary>

**Answer:** **Trajectory and tool use**.
**Why it matters:** The **final response** lens would pass this answer; checking both lenses is mandatory to catch skipped tools.
**Ref:** `[4.1]` · [04-evaluation-as-unit-tests.md](notes/04-evaluation-as-unit-tests.md)

</details>

**Q16. [True / False]** *Spot the Trap:* "You should continuously rewrite and add harder prompts to your golden dataset mid-project to keep CI testing challenging."

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Stabilize/freeze your golden dataset **as early as possible**. If you keep changing it, you cannot tell whether a score drop came from **bad code or a harder test**.
**Why it matters:** Comparable experiments require a fixed ground-truth yardstick.
**Ref:** `[4.1]` · [04-evaluation-as-unit-tests.md](notes/04-evaluation-as-unit-tests.md)

</details>

---

## 5. CI/CD Pipeline with Automated Quality Gates ([note](notes/05-cicd-pipeline-quality-gates.md))

**Q17. [Recall / Cloze]** List the **five steps** of the agent CI/CD pipeline in order, and identify which step hosts the **automated blocking quality gate**.

<details>
<summary>Show Answer</summary>

**Answer:**
1. Validate and test (static analysis + unit tests)
2. Build and deploy to dev (package code + prompts, smoke tests)
3. Manual approval gate (human verification)
4. **Deploy to staging and run evaluation** ← **Automated quality gate**
5. Production deployment (final manual approval + metadata auto-registered)

**Why it matters:** The pipeline has two human approvals (Step 3, Step 5) and one automated AI quality gate (Step 4).
**Ref:** `[4.2][4.1]` · [05-cicd-pipeline-quality-gates.md](notes/05-cicd-pipeline-quality-gates.md)

</details>

**Q18. [Compare]** **Step 1 (Validate & test)** vs. **Step 4 (Staging evaluation)** — why did the Digital Concierge prompt edit pass Step 1 but fail Step 4?

<details>
<summary>Show Answer</summary>

**Answer:** Step 1 runs static analysis and traditional unit tests (no syntax errors, so it passed). Step 4 evaluates behavior against the golden dataset and caught that the new prompt made the LLM skip `check_inventory` (tool-call accuracy `1.2 / 5` vs `4.5` threshold).
**Why it matters:** Code-level unit tests cannot detect behavioral or tool-selection regressions.
**Ref:** `[4.1][4.2]` · [05-cicd-pipeline-quality-gates.md](notes/05-cicd-pipeline-quality-gates.md)

</details>

**Q19. [Recall / Cloze]** How does Cloud Build halt deployment in Step 4 when the Python evaluation script sees a metric below threshold (e.g., `groundedness_score < 4.0`)?

<details>
<summary>Show Answer</summary>

**Answer:** The script returns a **non-zero exit code** (e.g., `exit(1)`), which signals a script error and causes **Cloud Build to fail the whole pipeline**.
**Why it matters:** The quality gate is enforced purely through standard CI exit-code mechanics.
**Ref:** `[4.1]` · [05-cicd-pipeline-quality-gates.md](notes/05-cicd-pipeline-quality-gates.md)

</details>

**Q20. [True / False]** *Spot the Trap:* "Prompt configurations are edited live after deployment and bypass the Cloud Build CI/CD pipeline."

<details>
<summary>Show Answer</summary>

**Answer:** **False.** In Step 2, **prompt configurations are packaged into the artifact** alongside agent code and dependencies, so prompt changes go through the exact same pipeline and quality gate.
**Why it matters:** A prompt tweak can break tool-use accuracy just as severely as a code bug.
**Ref:** `[4.2]` · [05-cicd-pipeline-quality-gates.md](notes/05-cicd-pipeline-quality-gates.md)

</details>

---

## 6. Canary Deployments, Traffic Splitting and Instant Rollback ([note](notes/06-canary-deployments-and-rollback.md))

**Q21. [Compare]** **Offline evaluation** vs. **Online evaluation** — where does each run, what data does it use, and what does it catch?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Offline:** CI/CD staging gate → **golden dataset** → catches **known regressions** with zero user risk.
- **Online:** Production via **canary** → **real live traffic** → catches **unforeseen real-world errors** (slang, multipart prompts) with blast radius limited to the canary slice.

**Why it matters:** Both layers are needed; golden datasets never capture every live user behavior.
**Ref:** `[4.1][4.2]` · [06-canary-deployments-and-rollback.md](notes/06-canary-deployments-and-rollback.md)

</details>

**Q22. [Recall / Cloze]** In canary traffic splitting, what are the **control arm** and **treatment arm**, and what **three metric categories** must be tracked beyond uptime?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Arms:** **Control arm** = stable older version (v1); **Treatment arm** = new unproven version (v2).
- **Three metric categories:** **Business metrics** (conversion, cart abandonment), **Application telemetry** (token usage, latency, output size, DB errors), and **Human feedback** (explicit/implicit signals, asking for a human).

**Why it matters:** An agent can stay "up" (no crashes) while failing silently and hurting conversion.
**Ref:** `[4.2]` · [06-canary-deployments-and-rollback.md](notes/06-canary-deployments-and-rollback.md)

</details>

**Q23. [1-Line Trigger -> Choice]** Within 15 minutes of routing 10% traffic to v2, Cloud Monitoring flags 12s latency vs. 2s on v1. How do you execute an **instant, zero-downtime rollback**?

<details>
<summary>Show Answer</summary>

**Answer:** Update the **environment routing rule in Agent Runtime** (manually or via automated trigger) to route **100% of traffic back to v1**.
**Why it matters:** Rollback is a routing-rule change to the still-running v1, not a slow code rebuild/redeploy.
**Ref:** `[4.2]` · [06-canary-deployments-and-rollback.md](notes/06-canary-deployments-and-rollback.md)

</details>

**Q24. [True / False]** *Spot the Trap:* "In the canary rollback scenario, Cloud Monitoring both detected the 12s latency spike and revealed the root cause (JSON parsing loop)."

<details>
<summary>Show Answer</summary>

**Answer:** **False.** **Cloud Monitoring** *detected* the latency anomaly; **OpenTelemetry logs/traces** isolated from the 10% canary slice *explained* the Inventory API JSON parsing loop.
**Why it matters:** Metrics detect anomalies; distributed traces explain *why* they happened.
**Ref:** `[4.2]` · [06-canary-deployments-and-rollback.md](notes/06-canary-deployments-and-rollback.md)

</details>

---

## 7. IAM for Agents: Least Privilege, Service Accounts, AuthN vs AuthZ ([note](notes/07-iam-service-accounts-authn-authz.md))

**Q25. [Recall / Cloze]** What is the **confused deputy problem** for AI agents, and why does IAM still protect you when prompt-level guardrails fail?

<details>
<summary>Show Answer</summary>

**Answer:** A malicious user tricks a **privileged agent** (the deputy) via **prompt injection** into misusing its own authority (e.g., dropping a table). Least-privilege **IAM** denies the unauthorized call at the **infrastructure level**, regardless of what the model was tricked into requesting.
**Why it matters:** You cannot make an LLM 100% un-trickable; IAM enforces hard boundaries.
**Ref:** `[5.1][5.2]` · [07-iam-service-accounts-authn-authz.md](notes/07-iam-service-accounts-authn-authz.md)

</details>

**Q26. [Compare]** **Authentication (AuthN)** vs. **Authorization (AuthZ)** — what question does each answer, and what is the course analogy?

<details>
<summary>Show Answer</summary>

**Answer:**
- **AuthN:** *Who are you?* (Verify credentials → becomes a known Principal; *"gets your foot in the door"*).
- **AuthZ:** *What are you allowed to do?* (IAM checks roles/permissions on that Principal; *"tells you which rooms you can enter"*).

**Why it matters:** Multi-agent systems act like independent microservices, so both AuthN and AuthZ are required on every call.
**Ref:** `[5.1]` · [07-iam-service-accounts-authn-authz.md](notes/07-iam-service-accounts-authn-authz.md)

</details>

**Q27. [1-Line Trigger -> Choice]** Match each target to its authentication method (**Service account**, **API key**, or **OAuth client ID**):
1. Calling Google Cloud services (BigQuery, Cloud Storage)
2. Calling an external third-party endpoint requiring a key
3. Acting on an end-user's own data with their explicit consent

<details>
<summary>Show Answer</summary>

**Answer:**
1. **Service account** (dedicated per agent, least-privilege IAM roles)
2. **API key** (secured in Secret Manager)
3. **OAuth client ID** (explicit end-user consent)

**Why it matters:** Using a service account for user-consented actions or sharing default service accounts violates least privilege.
**Ref:** `[5.1]` · [07-iam-service-accounts-authn-authz.md](notes/07-iam-service-accounts-authn-authz.md)

</details>

**Q28. [True / False]** *Spot the Trap:* "Assigning `roles/aiplatform.user` to a greeting agent's dedicated service account lets it call Gemini and also query the BigQuery Orders table."

<details>
<summary>Show Answer</summary>

**Answer:** **False.** `roles/aiplatform.user` only permits calling the Gemini model and grants **no data access**. Reading BigQuery requires a data role (e.g., `roles/bigquery.dataViewer` scoped to the Orders table).
**Why it matters:** Dedicated service accounts contain blast radius only when roles are strictly scoped per agent.
**Ref:** `[5.1]` · [07-iam-service-accounts-authn-authz.md](notes/07-iam-service-accounts-authn-authz.md)

</details>

---

## 8. Securing State and Memory, Data Perimeters and VPC Service Controls ([note](notes/08-state-memory-and-data-perimeters.md))

**Q29. [Recall / Cloze]** What are the **two risks** of storing agent state in local container RAM, and which **three managed database types** does this note name for externalizing state?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Risks:** **Reliability** (context lost on scale-down or crash) and **Security** (sensitive data exposed in **plain text** if breached).
- **Externalize to:** **Firestore**, **Cloud SQL**, or **managed vector databases** (for encryption at rest/in transit, IAM, and auditability).

**Why it matters:** Stateless scale-out (e.g., Cloud Run spinning up 50 instances) breaks in-memory sessions immediately.
**Ref:** `[5.2][3.1][4.2]` · [08-state-memory-and-data-perimeters.md](notes/08-state-memory-and-data-perimeters.md)

</details>

**Q30. [Compare]** **VPC** vs. **Cloud NAT** vs. **VPC Service Controls** — what role does each play in the three-layer network defense?

<details>
<summary>Show Answer</summary>

**Answer:**
- **VPC:** Private isolated network hosting agents and internal tools as private APIs.
- **Cloud NAT:** Routes safe outbound internet calls without exposing internal IPs.
- **VPC Service Controls:** Security perimeter around cloud resources that **explicitly blocks data exfiltration** to untrusted external destinations.

**Why it matters:** Cloud NAT *enables* safe outbound calls; VPC SC *restricts* where data is allowed to flow.
**Ref:** `[5.2][5.1]` · [08-state-memory-and-data-perimeters.md](notes/08-state-memory-and-data-perimeters.md)

</details>

**Q31. [1-Line Trigger -> Choice]** A Red Team prompt-injects an agent to read 500 VIP customer records and HTTP POST them to an unauthorized external URL. Which defense layer severs the connection and alerts security?

<details>
<summary>Show Answer</summary>

**Answer:** **VPC Service Controls**.
**Why it matters:** Even when the prompt layer is bypassed, the network perimeter blocks exfiltration outside trusted boundaries (defense in depth).
**Ref:** `[5.2]` · [08-state-memory-and-data-perimeters.md](notes/08-state-memory-and-data-perimeters.md)

</details>

**Q32. [True / False]** *Spot the Trap:* "When externalizing memory to Firestore, the frontend must pass the encrypted conversation history on every turn so any container can serve the user."

<details>
<summary>Show Answer</summary>

**Answer:** **False.** The frontend passes only an opaque/secure **session ID**; the agent fetches and updates the encrypted history server-side from Firestore.
**Why it matters:** Keeping state server-side avoids exposing PII or session contents to the client.
**Ref:** `[5.2][3.1]` · [08-state-memory-and-data-perimeters.md](notes/08-state-memory-and-data-perimeters.md)

</details>

---

## 9. Gateway Access Control, Credential Isolation and Guardrails ([note](notes/09-gateway-credential-isolation-guardrails.md))

**Q33. [Recall / Cloze]** List the **four benefits** of placing containerized agents and tools behind a centralized **API Gateway / Agent Gateway**.

<details>
<summary>Show Answer</summary>

**Answer:**
1. **Authentication** (API keys, OAuth tokens, or service account IAM credentials on every call)
2. **Authorization** (enforces fine-grained access using registry ownership details)
3. **Rate limiting and quotas** (stops runaway agent loops from causing an internal DoS)
4. **Centralized logging** (tamper-proof audit trail of every tool invocation)

**Why it matters:** Rate limits protect your own internal systems from infinite-retry loops.
**Ref:** `[5.1][5.2]` · [09-gateway-credential-isolation-guardrails.md](notes/09-gateway-credential-isolation-guardrails.md)

</details>

**Q34. [1-Line Trigger -> Choice]** A flight-lookup tool needs a third-party API key, and you must guarantee a prompt-injected agent/LLM can **never** see or leak it. How do you isolate the credential?

<details>
<summary>Show Answer</summary>

**Answer:** Store the API key in **Secret Manager** and have a **secure intermediary** (cloud function or dedicated backend service) fetch it at the moment of the tool call—never expose it to the agent or model.
**Why it matters:** A prompt injection cannot make the model reveal a secret it never had.
**Ref:** `[5.1][5.2]` · [09-gateway-credential-isolation-guardrails.md](notes/09-gateway-credential-isolation-guardrails.md)

</details>

**Q35. [Compare]** **Input filtering** vs. **Output filtering** in guardrails — what threats does each protect against?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Input filtering:** Protects against **data poisoning**, **model inversion**, and **adversarial prompt attacks**.
- **Output filtering:** Prevents the agent from leaking **PII** or generating **toxic content**.

**Why it matters:** Guardrails (like Model Armor with Sensitive Data Protection) inspect both sides of the conversation, acting as the probabilistic final layer alongside hard IAM controls.
**Ref:** `[5.2]` · [09-gateway-credential-isolation-guardrails.md](notes/09-gateway-credential-isolation-guardrails.md)

</details>

**Q36. [True / False]** *Spot the Trap:* "Secret Manager and Model Armor with Sensitive Data Protection perform the same job: keeping API keys out of agent responses."

<details>
<summary>Show Answer</summary>

**Answer:** **False.** **Secret Manager** *stores and serves* secrets to authorized tool code; **Model Armor / Sensitive Data Protection** *inspects and redacts* secrets or PII that appear in conversation text.
**Why it matters:** One is a secret vault for backend code; the other is a content-inspection guardrail.
**Ref:** `[5.2]` · [09-gateway-credential-isolation-guardrails.md](notes/09-gateway-credential-isolation-guardrails.md)

</details>

---

## 10. Agent Observability: OpenTelemetry and Cloud Trace ([note](notes/10-agent-tracing-opentelemetry.md))

**Q37. [Recall / Cloze]** In OpenTelemetry Python instrumentation for Cloud Trace, why do you wrap `CloudTraceSpanExporter` inside a **`BatchSpanProcessor`**?

<details>
<summary>Show Answer</summary>

**Answer:** To batch span exports so **telemetry collection does not block the agent's real-time reasoning loop**.
**Why it matters:** Synchronous export on every span would add latency to every reasoning step and tool call.
**Ref:** `[4.2]` · [10-agent-tracing-opentelemetry.md](notes/10-agent-tracing-opentelemetry.md)

</details>

**Q38. [Compare]** **Root span** vs. **Child spans** — what does each represent, and why is **auto-instrumentation** insufficient for AI agents?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Root span:** The whole user transaction.
- **Child spans:** Nested reasoning turns, LLM calls, and tool executions (nesting explains *why* an action happened).
- **Auto-instrumentation:** Only captures inbound HTTP traffic and **misses internal cognitive steps**; you need explicit tracer setup and GenAI semantic conventions.

**Why it matters:** Without nested child spans, a 5-second delay is a black box.
**Ref:** `[4.2]` · [10-agent-tracing-opentelemetry.md](notes/10-agent-tracing-opentelemetry.md)

</details>

**Q39. [1-Line Trigger -> Choice]** Match each Cloud Trace waterfall pattern to its diagnosis:
1. Consecutive linear child spans waiting one after another
2. Root span is green (HTTP 200), but a deep child span is red with a stack trace
3. Deeply repeating `Reasoning → Tool → Reasoning → Tool` nesting

<details>
<summary>Show Answer</summary>

**Answer:**
1. **Staircase progression** (synchronous bottleneck → parallelize independent spans)
2. **Silent failure** (pinpoints failed tool behind a polite 200 OK response)
3. **Infinite loop / tool cascade** (lost context or exit-criteria prompt not halting)

**Why it matters:** Each visual pattern in Cloud Trace maps directly to a distinct fix (parallelize vs. fix tool vs. fix exit criteria).
**Ref:** `[4.2]` · [10-agent-tracing-opentelemetry.md](notes/10-agent-tracing-opentelemetry.md)

</details>

**Q40. [True / False]** *Spot the Trap:* "OpenTelemetry is Google Cloud's proprietary trace viewer, whereas Cloud Trace is the open-source collection standard."

<details>
<summary>Show Answer</summary>

**Answer:** **False** (reversed). **OpenTelemetry (OTel)** is the open-source standard for collecting and exporting traces, metrics, and logs; **Cloud Trace** is the Google Cloud backend/timeline viewer.
**Why it matters:** OTel decouples instrumentation from the backend destination.
**Ref:** `[4.2]` · [10-agent-tracing-opentelemetry.md](notes/10-agent-tracing-opentelemetry.md)

</details>

---

## 11. Structured Logging for Nondeterministic Systems ([note](notes/11-structured-logging-cloud-logging.md))

**Q41. [Recall / Cloze]** What output format must an agent write to stdout so Cloud Logging parses it into a searchable **`jsonPayload`**, and what are the **three metadata layers** in the logging schema?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Format:** **Single-line JSON** (plain text is stored as an unsearchable string).
- **Three layers:** **Configuration** (`temperature`, `top_p`, model version), **Context** (system instructions, prompt templates, user metadata), and **Usage** (input, output, cached token counts).

**Why it matters:** Tying generation to its config and instructions lets you query silent behavioral regressions.
**Ref:** `[4.2]` · [11-structured-logging-cloud-logging.md](notes/11-structured-logging-cloud-logging.md)

</details>

**Q42. [Compare]** **Prompt drift / hallucination** vs. **Configuration anomaly** in Cloud Logging — how do their `temperature` filter indicators differ?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Prompt drift / hallucination:** Bad/unapproved output (e.g., invented discount codes) occurs despite **low `temperature ≤ 0.2`** (rules/prompt failing, not randomness).
- **Configuration anomaly:** Off-brand/tone violations correlate with **`temperature > 0.7`** (environment slip or bad deploy activated high creativity).

**Why it matters:** Checking `temperature` separates a prompt rule failure from a deployment/config error.
**Ref:** `[4.2]` · [11-structured-logging-cloud-logging.md](notes/11-structured-logging-cloud-logging.md)

</details>

**Q43. [1-Line Trigger -> Choice]** Checkouts drop abruptly after a release (`severity = ERROR`). Which field do you group by in **Log Analytics** (SQL) or **Logs Explorer** (LQL) to isolate a system-instruction mismatch?

<details>
<summary>Show Answer</summary>

**Answer:** **`system_instruction_version`**.
**Why it matters:** Grouping errors by instruction version tag measures blast radius and pinpoints which prompt edit triggered downstream failures.
**Ref:** `[4.2]` · [11-structured-logging-cloud-logging.md](notes/11-structured-logging-cloud-logging.md)

</details>

**Q44. [True / False]** *Spot the Trap:* "Because you need exact prompt and output text to debug hallucinations, you should always log raw, unredacted text payloads in production."

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Always **cleanse/redact PII before writing to disk**, and use **conditional logic to toggle deep text payloads by environment** (off in production).
**Why it matters:** Unredacted retail prompts leak addresses, phone numbers, and card details into logs.
**Ref:** `[5.2]` · [11-structured-logging-cloud-logging.md](notes/11-structured-logging-cloud-logging.md)

</details>
