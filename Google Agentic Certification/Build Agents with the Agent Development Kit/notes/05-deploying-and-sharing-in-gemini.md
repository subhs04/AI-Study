# Deploying and Sharing ADK Agents in Gemini

**Module map:** Covers `[4.2]` (Agent Runtime deployment & scaling), `[3.1]` (Agents CLI, partial), and `[5.1]` (IAM, cross-project & Workforce Identity sharing, partial).  
**Prerequisites:** [01-adk-core-model-and-setup.md](01-adk-core-model-and-setup.md), [03-multi-agent-orchestration-and-adk2.md](03-multi-agent-orchestration-and-adk2.md).

---

## The Gemini Platform Pillars and Product Boundaries `[4.2]`

The **Gemini platform** (consolidating Agent Builder and Agent Engine) organizes agent lifecycle into four pillars:

| Pillar | Primary Component | Role |
|---|---|---|
| **BUILD** | **ADK** | Code-first framework to define agent behavior, tools, and orchestration. |
| **SCALE** | **Agent Runtime** *(formerly Agent Engine)* | Managed hosting, scaling, cold-start optimization, and session persistence. |
| **GOVERN** | **Agent Registry** | Enterprise governance, discovery, and policy enforcement. |
| **OPTIMIZE** | Evaluation & Observability | Continuous agent quality and performance optimization. |

- **Three distinct steps:** Deploying to **Agent Runtime** does **not** automatically expose the agent in **Gemini** (the end-user employee web app), nor does it register it in **Agent Registry**. Registration in **Gemini** is an explicit separate step.

**Takeaway:** Build in **ADK** -> Deploy to **Agent Runtime** -> Explicitly register in **Gemini** (for end users) and **Agent Registry** (for governance).

---

## Deploying to Agent Runtime `[4.2]` `[3.1]`

**Agent Runtime** deploys an ADK project as a managed **`reasoningEngine`** resource:
`projects/PROJECT_ID/locations/LOCATION/reasoningEngines/RESOURCE_ID`

- **Multi-agent single-unit deployment:** Deploying the project rooted at `root_agent` packages the coordinator and all sub-agents together behind **one** `reasoningEngine` resource, preserving orchestration unchanged.
- **Prerequisites:** Billing-enabled GCP project, **Agent Platform API** and **Cloud Resource Manager API** enabled, authenticated `gcloud` CLI, and a standard ADK directory.

### Choosing a Deployment Path

| Path | When to Use | Notes |
|---|---|---|
| **ADK CLI** (`adk deploy agent_engine`) | Standard deployment with an already-configured project and direct parameter control. | Prints the `reasoningEngine` resource path. |
| **Agents CLI** | Need a production-ready environment with **CI/CD pipelines** and **Terraform (IaC)** scaffolded automatically. | Review generated IaC against org security standards before production. |
| **Gemini Platform SDK** (`client.agent_engines.create()`) | Need custom deployment sources or runtime tuning (`min_instances`, `max_instances`, `resource_limits`, identity/secrets, networking/compliance). | Both CLIs wrap this SDK method. |

- **SDK deployment sources:** In-memory agent object (notebooks), local source/`Dockerfile` (CI/CD, no GCS bucket needed), prebuilt **Artifact Registry** image, or connected Git repo via **Developer Connect**.
- **Resource handle:** `remote_agent.api_resource.name` returns the `reasoningEngine` path needed for Gemini registration.

### Worked Example: Programmatic SDK Deployment

```python
remote_agent = client.agent_engines.create(
    agent=local_agent,
    config={
        "display_name": "Order Support Agent",
        "min_instances": 1,
        "max_instances": 10,
        "resource_limits": {"cpu": "4", "memory": "8Gi"},
    },
)
```

**Cross-check:** `min_instances=1` avoids cold starts for latency-sensitive support traffic, `max_instances=10` caps scale/cost, and `remote_agent.api_resource.name` yields the `reasoningEngine` path for registration.

> **Gap:** The source capture listed *Identity and Secrets* and *Networking and Compliance* as `config` categories without listing the exact parameter names. Verify exact keys in the official Agent Runtime SDK docs if needed.

**Takeaway:** Use `adk deploy agent_engine` for direct deploys, **Agents CLI** for CI/CD + Terraform scaffolding, and `client.agent_engines.create()` for fine-grained scaling/container control.

---

## Registering and Sharing in Gemini `[4.2]` `[5.1]`

To make a deployed `reasoningEngine` available to employees in the **Gemini** web app:

- **Prerequisites:** **Gemini Admin** role, **Discovery Engine API** enabled, an existing **Gemini app**, and the `reasoningEngine` resource URL.
- **Regional compatibility rule:** A **`us`** Gemini app requires an agent deployed in a **`us-*`** region; a **`global`** Gemini app accepts **any** region.
- **Role of `description`:** Just like a coordinator reads a sub-agent's `description` to route tasks, Gemini's platform model reads the registered `description` to decide when to invoke your agent.

### Extended Registration & Sharing Controls
1. **Acting on behalf of users:** Include an optional **authorization block** in the registration payload when the agent accesses Google Cloud resources using end-user credentials.
2. **Cross-project deployment:** If the agent's `reasoningEngine` lives in a different GCP project than the Gemini app, grant the **Gemini service agent** the **Discovery Engine Service Agent** IAM role in the agent's hosting project.
3. **User-permissions sharing:** An admin grants access in the agent's user-permissions view to:
   - Individual users or groups (by email)
   - Single identities or pools from **Workforce Identity Federation**
   - All users in the organization

**Takeaway:** Match the agent region to the Gemini app location (`us` -> `us-*`, `global` -> any), write a crisp `description` for routing, and grant **Discovery Engine Service Agent** for cross-project invocation.

---

## Common Confusions

| Confusion | Reality |
|---|---|
| `Agent Engine` vs `Agent Runtime` | Same product—**Agent Runtime** is the new name, though CLI (`adk deploy agent_engine`), SDK (`client.agent_engines.create()`), and resource paths (`reasoningEngines`) still use the old name. |
| Deploying to Agent Runtime = live in Gemini | False—you must explicitly register the `reasoningEngine` in Gemini (and separately in Agent Registry for governance). |
| Deploying multi-agent systems | Deploys as **one** `reasoningEngine` rooted at `root_agent`, not one endpoint per sub-agent. |
| Cross-project Gemini invocation | Requires granting the **Gemini service agent** the **Discovery Engine Service Agent** role in the agent's hosting project. |
