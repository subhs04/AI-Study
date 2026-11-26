# 07 — Production Deployment, Scaling & Observability

**Module map:** Covers choosing the right Google Cloud compute/hosting platform for agents, production security perimeters, observability (**Cloud Logging**, **Cloud Trace**, **Cloud Monitoring**, CX analytics), and lifecycle management (versioning, environments, rollbacks).
**Exam objectives:** `4.2` (Deploying and scaling production workloads), `5.1` / `5.2` (partial — production IAM & VPC Service Controls).
**Prerequisites:** [06 — Agent Security, Identity & Governance](06-agent-security-and-governance.md). **Next:** None (final note in module).

---

## 1. Choosing a Production Deployment Runtime `[4.2]`

Selecting where to host an agent depends on control requirements, traffic patterns, hardware needs, and operational overhead:

| Platform | Abstraction & Management | Scaling & Hardware | Best-Fit Scenario |
| :--- | :--- | :--- | :--- |
| **Agent runtime** | Fully managed serverless runtime optimized for **ADK** agents; single-CLI deploy; native **A2A** & memory/sessions | Automatic scaling; zero infrastructure management | Default production path for custom ADK agents needing fast deployment and managed memory/A2A |
| **Cloud Run** | Serverless containers; full control over container dependencies | Rapid autoscaling, **scale-to-zero** (pay-per-use); CPU/GPU allocation | Variable-traffic custom agents exposed as a web UI, REST API, or A2A endpoint |
| **Google Kubernetes Engine (GKE)** | Full Kubernetes container orchestration; highest control over networking/storage/security | **AI-aware autoscaling**: **GKE Inference Gateway**, custom **Horizontal Pod Autoscalers (HPA)**, specialized hardware (GPUs/TPUs) | High-throughput, low-tail-latency, multi-instance or multi-component agent & model architectures |
| **Compute Engine** | IaaS VMs; **highest operational overhead** (OS, patching, scaling, security) | Continuous, predictable resource allocation (no scale-to-zero) | Legacy/licensed software alongside agents, rigid OS/kernel/networking needs, or persistent stateful VMs |
| **Conversational Agents / CX Agent Studio** | Fully managed serverless platform with console snapshots & Dev/Staging/Prod environments | Automatic traffic-spike scaling | External/internal CX bots using **Dialogflow CX Messenger**, telephony/IVR, or messaging channels |

**Takeaway:** Prefer **Agent runtime** for managed ADK, **Cloud Run** for containerized scale-to-zero flexibility, **GKE** for specialized hardware and AI-aware autoscaling, and **Compute Engine** only for legacy OS/kernel constraints.

---

## 2. Production Security Controls Summary `[5.1]`, `[5.2]`

| Security Focus | Google Cloud Tool | Purpose in Production |
| :--- | :--- | :--- |
| **Identity & AuthZ** | **IAM** | Restricts human access (e.g., secured web UI) and enforces **least privilege** on agent service accounts / Agent Identity |
| **Data Perimeter** | **VPC Service Controls** | Creates a network boundary around data stores and vector databases to prevent data exfiltration |
| **Tool Permissions** | **Scoped Credentials & Service Accounts** | Constrains agent API/database/filesystem access strictly to task requirements |

**Takeaway:** Combine IAM least privilege, scoped credentials, and VPC Service Controls to isolate production data and tool blast radius.

---

## 3. Observability & Lifecycle Management `[4.2]`

### Four Observability Pillars
* **Cloud Logging:** Captures structured logs of every agent interaction, tool invocation, and error (fulfills the **agent observability** principle).
* **Cloud Trace:** Visualizes end-to-end conversation latency, breaking down time across **LLM calls**, **tool executions (webhooks)**, and **memory retrieval** to pinpoint bottlenecks.
* **Cloud Monitoring:** Tracks operational metrics—**QPS (queries per second)**, **error rates**, and **average latency**.
* **Agent-Specific Analytics (Conversational Agents):** Tracks business/AI KPIs such as **escalation rates**, **conversation outcomes**, and **tool failure rates**.

### Lifecycle Management
* **Versioning & Snapshots:** Snapshot stable combinations of agent code, models, and configuration in **Agent runtime**, **Cloud Run**, or **Conversational Agents**.
* **Environments & Rollbacks:** Promote snapshots sequentially across **Development → Staging → Production** and roll back immediately if regressions appear.

**Takeaway:** Use **Cloud Trace** to isolate latency bottlenecks (LLM vs. webhook vs. memory), **Cloud Logging** for audit trails, **Cloud Monitoring** for QPS/errors, and **Snapshots** for safe rollbacks.

---

## 4. Worked Example: Diagnosing & Scaling a Production Agent

**Scenario:** A custom ADK agent deployed for variable customer traffic experiences sudden latency spikes during peak hours and occasional webhook timeouts.
* **Hosting choice:** **Cloud Run** (if custom container dependencies + scale-to-zero cost efficiency are needed) or **Agent runtime** (if standard ADK + managed sessions/memory suffice). Upgrade to **GKE** with **GKE Inference Gateway** and custom **HPA** only if specialized GPUs/TPUs or tight tail-latency SLAs require Kubernetes control.
* **Diagnosis:** Inspect **Cloud Trace** to separate LLM inference time from webhook tool execution time, **Cloud Logging** for specific webhook error payloads, and **Cloud Monitoring** for QPS/error-rate correlation.

---

## Common Confusions

| Term A | Term B | Key Distinction |
| :--- | :--- | :--- |
| **Cloud Logging** | **Cloud Trace** | **Cloud Logging** records discrete structured events/errors; **Cloud Trace** measures *end-to-end latency spans* across LLM, tool, and memory steps. |
| **Cloud Run** | **GKE** | **Cloud Run** is serverless pay-per-use (scale-to-zero) with low ops overhead; **GKE** is full Kubernetes orchestration for specialized hardware, Inference Gateway, and complex multi-component topologies. |
| **Compute Engine** | **Agent runtime** | **Compute Engine** gives raw VM/OS control with high ops overhead; **Agent runtime** is a fully managed serverless runtime purpose-built for ADK agents. |

*Sources: Security, Access Control, and Deployment (`security-access-control-and-deployment-raw.md`).*
