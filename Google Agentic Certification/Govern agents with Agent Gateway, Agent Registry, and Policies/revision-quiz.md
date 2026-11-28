# Revision Quiz: Govern agents with Agent Gateway, Agent Registry, and Policies

**Questions:** 16 · **Tip:** Tap **Show Answer** on GitHub Mobile to check your recall.

---

## 1. Centralized Agent Gateway and Dual Access Paths ([note](notes/01-centralized-gateway-and-dual-paths.md))

**Q1. [Compare]** How do **Client-to-Agent (Ingress)** and **Agent-to-Anywhere (Egress)** differ in enforcement mechanism and isolation?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Ingress (Client-to-Agent):** Uses **Identity-Aware Proxy (IAP)** to validate Google credentials/org role and pass a signed **OIDC ID token** to Agent Runtime; only authenticated, role-authorized enterprise users can invoke the agent.
- **Egress (Agent-to-Anywhere):** Binds Agent Runtime to Agent Gateway via a **regional network attachment**, resolves names via **split-horizon DNS peering**, and routes over **PSC Interfaces**; zero direct public internet routing, restricted to destinations in **Agent Registry**.

**Why it matters:** Ingress gates *who* can invoke the agent; Egress gates *where* the agent can send data.
**Ref:** `[5.1][5.2]` · [01-centralized-gateway-and-dual-paths.md](notes/01-centralized-gateway-and-dual-paths.md)

</details>

**Q2. [Compare]** In the Agent Gateway proxy plane, what is the difference between **`REQUEST_AUTHZ`** and **`CONTENT_AUTHZ`**?

<details>
<summary>Show Answer</summary>

**Answer:**
- **`REQUEST_AUTHZ`:** Evaluated at the **HTTP header stage** *before* forwarding; integrates with IAP/IAM to check whether the agent's identity can call the target endpoint/tool.
- **`CONTENT_AUTHZ`:** Evaluated on **request and response bodies** using Model Armor templates (prompt injection/RAI checks inbound; DLP/secret-leakage checks outbound).

**Why it matters:** `REQUEST_AUTHZ` checks *who may call the tool*, while `CONTENT_AUTHZ` checks *what is in the payload*.
**Ref:** `[5.1][5.2]` · [01-centralized-gateway-and-dual-paths.md](notes/01-centralized-gateway-and-dual-paths.md)

</details>

**Q3. [Recall / Cloze]** In the centralized control plane, **Agent Gateway** is the traffic/policy enforcement proxy, whereas **\_\_\_\_\_\_** is the centralized catalog tracking approved agent workloads and authorized destinations/tools.

<details>
<summary>Show Answer</summary>

**Answer:** **Agent Registry**
**Why it matters:** Agent Gateway enforces the allowlist defined in Agent Registry so agents cannot reach unapproved external endpoints.
**Ref:** `[3.2][5.1]` · [01-centralized-gateway-and-dual-paths.md](notes/01-centralized-gateway-and-dual-paths.md)

</details>

**Q4. [True / False]** *Agents running on Agent Runtime can safely use default public internet egress when handling sensitive enterprise databases, as long as Ingress is protected by IAP.*

<details>
<summary>Show Answer</summary>

**Answer:** **False.** A prompt-injected or compromised agent can exfiltrate scanned data by writing it to an external public endpoint if egress is not forced through Agent Gateway.
**Why it matters:** Never allow default public internet egress for agents handling sensitive enterprise data—bind egress to Agent Gateway.
**Ref:** `[5.1][5.2]` · [01-centralized-gateway-and-dual-paths.md](notes/01-centralized-gateway-and-dual-paths.md)

</details>

---

## 2. Enterprise Networking, Private Egress, and Tool Ingress ([note](notes/02-enterprise-networking-and-tool-ingress.md))

**Q5. [Recall / Cloze]** What are the sizing and overlap rules for the **Proxy Only Subnet** vs. the **Agent Gateway Subnet** in the VPC?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Proxy Only Subnet:** **`/24`** active range, required by Google Cloud's regional Application Load Balancer (ALB).
- **Agent Gateway Subnet:** Dedicated **`/28` or larger** (RFC 1918), reserved exclusively for Agent Gateway egress, and **must not overlap** with standard VPC routing.

**Why it matters:** Overlapping the Agent Gateway egress subnet with standard VPC routing breaks private egress via the Network Attachment.
**Ref:** `[5.1][5.2]` · [02-enterprise-networking-and-tool-ingress.md](notes/02-enterprise-networking-and-tool-ingress.md)

</details>

**Q6. [Compare]** What is the difference between **Cloud NAT** and a **Network Attachment (PSC Interface)** in private egress networking?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Cloud NAT:** Lets private VPC resources pull outbound software updates without inbound internet exposure.
- **Network Attachment (PSC Interface):** Lets Agent Gateway push egress MCP/tool calls *into* your VPC using private IPs from the Agent Gateway egress subnet.

**Why it matters:** Don't confuse outbound VPC update access (Cloud NAT) with Agent Gateway-to-VPC tool ingress (Network Attachment).
**Ref:** `[5.1][5.2]` · [02-enterprise-networking-and-tool-ingress.md](notes/02-enterprise-networking-and-tool-ingress.md)

</details>

**Q7. [1-Line Trigger → Choice]** A regulated enterprise forbids any public `*.run.app` endpoints for its internal Cloud Run MCP server and already owns `mcp.corp.example` in Cloud DNS. Which Cloud Run ingress setting and routing topology should you choose?

<details>
<summary>Show Answer</summary>

**Answer:** **`ingress = "internal-and-cloud-load-balancing"`** (Secure: Private Ingress), routed via **Agent Gateway → PSC Network Attachment → Internal regional ALB + Serverless NEG → Cloud Run** (with a regional Google-managed SSL cert).
**Why it matters:** `internal-and-cloud-load-balancing` disables `*.run.app` completely, making the MCP server unreachable from outside the VPC.
**Ref:** `[4.2][5.1][5.2]` · [02-enterprise-networking-and-tool-ingress.md](notes/02-enterprise-networking-and-tool-ingress.md)

</details>

**Q8. [True / False]** *Setting a Cloud Run MCP server to `ingress = "all"` leaves it open to unauthenticated internet traffic.*

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Even with `ingress = "all"`, IAP, IAM, and Agent Gateway service extensions block unauthenticated internet requests; it only fails compliance policies that forbid *any* public URL from existing.
**Why it matters:** `ingress = "all"` is safe for sandbox dev/POCs and avoids custom DNS/SSL overhead while still enforcing authZ.
**Ref:** `[4.2][5.1]` · [02-enterprise-networking-and-tool-ingress.md](notes/02-enterprise-networking-and-tool-ingress.md)

</details>

---

## 3. Identity, Per-Tool Authorization, and Content Guardrails ([note](notes/03-identity-access-control-and-guardrails.md))

**Q9. [Recall / Cloze]** What is the exact **Agent Runtime service identity format** used when authenticating agent-to-tool calls?

<details>
<summary>Show Answer</summary>

**Answer:** `service-[PROJECT_NUMBER]@gcp-sa-aiplatform-re.iam.gserviceaccount.com`
**Why it matters:** Knowing this exact principal format helps you spot and grant IAM/IAP permissions for Agent Runtime in audit logs and policies.
**Ref:** `[5.1]` · [03-identity-access-control-and-guardrails.md](notes/03-identity-access-control-and-guardrails.md)

</details>

**Q10. [Compare]** In `CONTENT_AUTHZ`, how do **`agw-request-template`** and **`agw-response-template`** differ in stage, filters, and actions?

<details>
<summary>Show Answer</summary>

**Answer:**
- **`agw-request-template` (Inbound, before LLM/tool):** Runs **Prompt injection shield** (`Action: BLOCK` on attack) and **RAI safety filters** (`Action: BLOCK` on violence/hate).
- **`agw-response-template` (Outbound, after LLM/tool):** Runs **Sensitive Data Protection (DLP)** (`Action: MASK` SSN/accounts) and **Data leakage shield** (`Action: BLOCK` on secrets leakage).

**Why it matters:** Inbound blocks unsafe prompts before execution; outbound masks PII and blocks leaked secrets before returning responses.
**Ref:** `[5.1][5.2]` · [03-identity-access-control-and-guardrails.md](notes/03-identity-access-control-and-guardrails.md)

</details>

**Q11. [Compare]** When integrating **Cloud DLP (Sensitive Data Protection)** with Model Armor, what is the difference between an **Inspect template** and a **De-identify template**?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Inspect template:** Defines *what* sensitive data types (`infoTypes`, e.g., `US_SOCIAL_SECURITY_NUMBER`) to detect and at what confidence (`minLikelihood`, e.g., `"POSSIBLE"`).
- **De-identify template:** Defines *how* to transform matched data (e.g., mask or redact).

**Why it matters:** Both link into `agw-response-template` so raw PII returned by an internal tool is masked at the gateway boundary.
**Ref:** `[5.1][5.2]` · [03-identity-access-control-and-guardrails.md](notes/03-identity-access-control-and-guardrails.md)

</details>

**Q12. [True / False]** *Standard network firewalls and `REQUEST_AUTHZ` can detect prompt injection attacks and mask PII returned by an internal tool.*

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Network firewalls and `REQUEST_AUTHZ` inspect network/HTTP headers and identity, not natural language payloads; semantic inspection requires **`CONTENT_AUTHZ`** with Model Armor and Cloud DLP.
**Why it matters:** Header-level authZ cannot stop prompt injection or PII leakage—you need `CONTENT_AUTHZ` on the payload.
**Ref:** `[5.1][5.2]` · [03-identity-access-control-and-guardrails.md](notes/03-identity-access-control-and-guardrails.md)

</details>

---

## 4. Observability, Tracing, Auditing, and Rollout Governance ([note](notes/04-observability-tracing-and-governance.md))

**Q13. [Recall / Cloze]** Name the **3 investigation tabs** in the Cloud Console **Traces** dashboard and the **latency overhead** of keeping OpenTelemetry tracing active in production.

<details>
<summary>Show Answer</summary>

**Answer:**
- **Tabs:** `VISUAL TIMELINE` (waterfall latency), `POLICY INSPECTION` (`REQUEST_AUTHZ` pass/fail), and `CONTENT SAFE-AUDIT` (`CONTENT_AUTHZ` / Model Armor scan results).
- **Latency overhead:** Negligible (**less than a few milliseconds**).

**Why it matters:** Keep OpenTelemetry tracing enabled in production—overhead is minimal and it gives full visibility across all 6 execution spans.
**Ref:** `[4.2][5.1]` · [04-observability-tracing-and-governance.md](notes/04-observability-tracing-and-governance.md)

</details>

**Q14. [Compare]** How does **Access Transparency (AxT)** differ from **Cloud Audit Logs**?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Cloud Audit Logs:** Record *your* users' and agents' actions (e.g., IAP/IAM access events).
- **Access Transparency (AxT):** Provides cryptographic logs of when and why *Google Cloud personnel* access your agent VPCs, reasoning engines, gateways, or Model Armor configs (e.g., during a support ticket).

**Why it matters:** Regulated workloads (HIPAA, FedRAMP, GDPR) often require AxT to prove vendor support accountability.
**Ref:** `[5.1][5.2]` · [04-observability-tracing-and-governance.md](notes/04-observability-tracing-and-governance.md)

</details>

**Q15. [Recall / Cloze]** What is the two-step safe IAP rollout workflow for Agent Gateway (which enforcement mode do you start in, and what value do you switch to after confirming zero false positives)?

<details>
<summary>Show Answer</summary>

**Answer:** Start with IAP Enforcement set to **`DRY_RUN`** (logs evaluations without blocking traffic), monitor the Authorization Debugging Dashboard for zero false positives, then switch IAP Enforcement to **`null`** to actively enforce policies.
**Why it matters:** Enforcing strict IAP/IAM policies on day one risks breaking legitimate tool calls; `DRY_RUN` → `null` prevents outages.
**Ref:** `[4.2][5.1]` · [04-observability-tracing-and-governance.md](notes/04-observability-tracing-and-governance.md)

</details>

**Q16. [1-Line Trigger → Choice]** In the Cloud Monitoring Authorization Debugging Dashboard, **Unregistered outbound blocks** is `0`, while **Agent to MCP server 403 denials** spikes. Which layer caused the failure, and where in Cloud Trace do you confirm it?

<details>
<summary>Show Answer</summary>

**Answer:** The host *is* in Agent Registry (`0` outbound blocks), so **IAP / `REQUEST_AUTHZ`** denied the agent's identity (`403`). Confirm the failed span (`Span 4: IAP REQUEST_AUTHZ`) in **Cloud Trace → `POLICY INSPECTION`**.
**Why it matters:** Distinguishing an Agent Registry host block from an IAP `403` denial immediately tells you whether to register the destination or fix IAM roles.
**Ref:** `[4.2][5.1]` · [04-observability-tracing-and-governance.md](notes/04-observability-tracing-and-governance.md)

</details>
