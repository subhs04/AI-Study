# 06 — Agent Security, Identity & Governance

**Module map:** Covers agentic lifecycle threats (rogue actions, data disclosure, memory poisoning, XSS), Google's three security principles, hybrid defense-in-depth, **Model Armor** + **Cloud DLP**, **AuthN vs. AuthZ**, **Agent Identity** vs. service accounts, and credential selection.
**Exam objectives:** `5.1` (Configuring agent security & governance), `5.2` (Implementing secure agent behavior & execution), `3.2` (partial — Agent Identity & permissions).
**Prerequisites:** [05 — ADK Multi-Agent Architecture & Orchestration](05-adk-multi-agent-and-orchestration.md). **Next:** [07 — Production Deployment & Observability](07-production-deployment-and-observability.md)

---

## 1. Agent Security Risks & Lifecycle Threats `[5.1]`, `[5.2]`

Unlike standard LLMs that only generate text, AI agents perceive environments, plan, and execute autonomous actions against APIs, databases, and browsers. This introduces **two major risks**:
1. **Rogue Actions:** Unintended, harmful, or policy-violating actions—typically triggered by **prompt injection** where malicious data hijacks the agent using the user's privileges.
2. **Sensitive Data Disclosure:** Leaking confidential data, IP, or PII via exfiltration actions or insecurely rendered outputs.

### Threats Across the 5 Lifecycle Stages
* **Input & Perception:** Mixing trusted system instructions with untrusted user queries or retrieved external data enables **prompt injection** and **jailbreak attacks**.
* **Reasoning & Planning:** Flawed logic or hijacked data in iterative planning compounds errors across steps.
* **Orchestration & Tool Use:** Compromised plans with over-privileged tools can delete files or make unauthorized purchases.
* **Agent Memory:** Storing summarized "facts" from a malicious document poisons future, unrelated interactions (**memory poisoning**).
* **Response Rendering:** Unescaped/unsanitized outputs expose apps to **Cross-Site Scripting (XSS)** or exfiltration via crafted URLs/image tags.

**Takeaway:** Every stage—from prompt ingestion and memory persistence to tool execution and UI rendering—is an attack surface requiring isolation and validation.

---

## 2. Security Principles, Hybrid Defense & Model Armor `[5.1]`, `[5.2]`

### Google's Three Core Principles for Agent Security
1. **Well-defined human controllers:** Accountability and human oversight for critical actions.
2. **Limited powers (Least Privilege):** Dynamically constrain permissions to only what the task requires; prevent privilege escalation.
3. **Agent observability:** Comprehensive, auditable logging of all inputs, reasoning steps, tool actions, and outputs.

### Hybrid Defense-in-Depth
Because LLMs are non-deterministic, Google pairs **reasoning-based defenses** (dynamic AI judgment) with **traditional deterministic measures**—runtime policy enforcement *outside* the model that intercepts tool calls before execution against hard rules (e.g., *"Block purchases > $500"* or *"Require user confirmation for high-risk actions"*).

### Model Armor & Cloud DLP
**Model Armor** integrates with the Gemini platform to screen prompts (inputs) and responses (outputs) in real time:
* **Prompt Injection & Jailbreak Detection:** Blocks inputs attempting to override system instructions.
* **Sensitive Data Leakage (`Cloud DLP`):** Scans prompts for PII/IP (e.g., credit cards, emails) and **masks, tokenizes, or redacts** them *before* LLM processing.
* **Content Safety:** Uses pre-trained safety classifiers against configurable harm thresholds (harassment, hate speech, dangerous content), backed by a 4-step workflow: (1) assess risks → (2) run safety tests → (3) configure safety filters → (4) collect feedback and monitor.

**Takeaway:** Pair deterministic pre-execution tool policies with **Model Armor** (+ **Cloud DLP**) to block prompt injections, redact PII before inference, and enforce content safety.

---

## 3. Authentication, Authorization & Agent Identity `[5.1]`, `[3.2]`

Google Cloud applies a two-step model to every agent request:
* **Authentication (AuthN — *Who are you?*):** Verifies identity via credentials, establishing a **Principal**.
* **Authorization (AuthZ — *What can you do?*):** **IAM** checks the **IAM Roles** bound to that principal (e.g., `storage.objects.get`), managed via Console (one-off) or `gcloud` CLI (automated/bulk).

### Service Accounts vs. Agent Identity

| Identity Type | How It Works | Security & Audit Trade-off |
| :--- | :--- | :--- |
| **Default Service Account** | `AI Platform Reasoning Engine Service Agent` (`roles/aiplatform.reasoningEngineServiceAgent`) auto-provisioned per project | Broad default permissions; shared across workloads |
| **Custom Service Account** | User-created SA assigned at deployment with minimal IAM roles | Enforces **least privilege**, but still shared if multiple agents use the same SA |
| **Agent Identity** | Automatically provisioned per individual agent on **Agent runtime**; lifecycle matches the agent's lifespan | **Best isolation:** independent IAM principal, **cannot be impersonated**, no long-lived keys, tokens valid only inside trusted runtime, exact per-agent attribution in Cloud Audit Logs |

### Choosing the Right Authentication Method

| Credential Type | Target Resource | Key Requirement |
| :--- | :--- | :--- |
| **Service Account** | Internal Google Cloud resources (Cloud Storage, BigQuery, Gemini platform) | Grant least-privilege IAM roles on project/resource |
| **API Keys** | External / third-party APIs | Store and retrieve securely via **Secret Manager** |
| **OAuth Client ID** | End-user accounts / data on a user's behalf (e.g., Calendar) | Requires explicit **end-user consent** |

* **Network & Data Protection:** Use **VPC Service Controls** (data perimeter around data stores/vector DBs to block exfiltration), **CMEK** (customer-managed encryption keys), and **data residency** controls.

**Takeaway:** Use **Agent Identity** (or a least-privilege custom service account) for GCP resources, **API keys in Secret Manager** for third-party APIs, and **OAuth Client ID** when acting on behalf of an end user.

---

## 4. Worked Example & Quiz Cross-Checks

* **Observability principle question:** Mandating that all agent inputs, actions, and outputs be logged and auditable = **Agent observability** (not *Limited powers* or *Human controllers*).
* **Cloud Storage access question:** An agent needs to read a Cloud Storage bucket = **Service Account** (not *OAuth Client ID*, which is for end-user delegated data, and not *Secret Manager*, which stores secrets rather than acting as a GCP IAM credential).

---

## Common Confusions

| Term A | Term B | Key Distinction |
| :--- | :--- | :--- |
| **Service Account** | **OAuth Client ID** | **Service Account** authenticates the *agent itself* to GCP resources; **OAuth Client ID** obtains *end-user consent* to access user-owned data. |
| **Agent Identity** | **Default Service Account** | **Agent Identity** is unique per agent, non-impersonatable, and key-less; the **Default SA** (`AI Platform Reasoning Engine Service Agent`) is project-wide. |
| **Model Armor** | **Deterministic Tool Policies** | **Model Armor** screens *prompts and responses* (injection, PII via Cloud DLP, safety); **deterministic policies** intercept *tool/API calls* prior to execution (e.g., spend limits). |

> [!NOTE]
> **Gap (`5.1`, `5.2`):** The exam guide also expects **Principal Access Boundary (PAB) policies**, **Agent Gateway**, and **Agent Registry** policy enforcement, plus detailed **HITL** patterns. Those services are covered in dedicated governance modules (*Govern agents with Agent Gateway, Agent Registry, and Policies* and *Model Armor*).

*Sources: Security, Access Control, and Deployment (`security-access-control-and-deployment-raw.md`).*
