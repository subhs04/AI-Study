# Structured Logging for Nondeterministic Systems (Cloud Logging)

**Module map:** Designing JSON logs that tie each response to its execution parameters, handling PII in logs, and querying Cloud Logging to diagnose hallucinations, configuration anomalies and system-instruction mismatches. Covers **4.2** (troubleshoot drift, hallucinations, monitor and optimize), **5.2** (data privacy in logs). Last note of the module and the course. Prerequisites: `10-agent-tracing-opentelemetry.md`, `01-agentops-lifecycle.md`.

---

## 1. Why plain logs fail for agents [4.2]

**What/why:** Traditional apps crash with clear errors (database error, missing field, timeout). An agent can return **HTTP 200** and still fail: hallucinate a nonexistent discount code, or ignore its system instructions. A line like `[INFO] Agent generated text` is useless. Logs must be **machine-readable, contextualized and searchable**.

**How Cloud Logging handles it:** plain text to stdout is treated as a simple **string**. Output **single-line JSON** and Cloud Logging **parses keys/values into a searchable `jsonPayload`**.

**Takeaway:** Structured JSON logs turn text history into an active, queryable database (micro view from `10`).

---

## 2. Logging schema: three metadata layers [4.2]

The schema must tie raw generation to the exact parameters that produced it.

| Layer | Fields (source) | Why it matters |
|---|---|---|
| **Configuration** | `temperature`, `top_p`, **exact model version** | Randomness settings explain behavior swings; model version identifies drift |
| **Context** | **System instructions**, **prompt templates**, active **user metadata** (e.g., customer tier) | Reconstruct what the LLM actually received |
| **Usage** | **Token counts**: input, output, **cached** | Track cost alongside behavior |

Terms: **temperature** = sampling randomness (low = more deterministic, high = more creative); **top_p** = nucleus-sampling cutoff controlling output diversity (brief clarification: the source only names it).

---

## 3. PII: cleanse before writing [5.2]

**Problem:** To analyze hallucinations you need to know exactly what the LLM saw and produced, but raw prompts in retail often contain **PII** (home addresses, phone numbers, credit card details).

**Requirement (source):** implement a **pipeline that cleanses raw text before writing it to disk**, and apply **conditional logic to toggle deep text payloads by environment** (e.g., full text only where appropriate, not in production).

| Practice | Reason |
|---|---|
| Cleanse/redact before write | Logs would otherwise store PII |
| Toggle deep payloads per environment | Keep detail for debugging without exposing production customer data |

> **Clarification (not in source):** The source does not name the redaction tool in this lesson. `09` shows **Model Armor with Sensitive Data Protection** inspecting and redacting sensitive data in conversation; that is a plausible fit but not stated here for logging. Verify in docs.

---

## 4. Querying anomalies in Cloud Logging [4.2]

**Tools:** **Log Analytics** (SQL syntax) or Google Cloud's **logging query language (LQL)** inside **Logs Explorer**. Because logs are parsed JSON, you can group behaviors by configuration fields.

| Production symptom | Primary filter fields | Target indicators | Root cause |
|---|---|---|---|
| Agent provides **invalid discount codes** | `temperature ≤ 0.2` + `model_output` text | Keywords "discount", "coupon", "free" | **Prompt drift / hallucination**: bypassing strict rules and inventing promotions despite low, deterministic temperature |
| Agent **off-brand / violates tone** | `temperature > 0.7`, `severity = INFO` | Baseline slips, accidental high-creativity rollouts | **Configuration anomaly**: environment slip or bad deploy activated an unapproved creative setting |
| **Abrupt drops in checkouts** / broken paths | `severity = ERROR`, `system_instruction_version` | Correlated spikes on a specific instruction version tag | **System-instruction mismatch**: recent wording tweak to core system prompt triggers downstream validation-loop failures |

Diagnostic logic for each:
- **Prompt drift/hallucination:** low temperature should make outputs stable; unapproved promo text under low temperature means the *prompt/rules* are failing, not randomness.
- **Temperature anomaly:** first step is to isolate whether an **environment configuration error** or a **bad code deployment** raised randomness.
- **Instruction mismatch:** measure **blast radius** by version tag after rolling out changes to rules/guardrails.

### Worked example [4.2]
Symptom (*illustrative*): after Tuesday's release, cart-drop rate doubles; no crashes.

| Step | Query/action | Cross-check |
|---|---|---|
| 1 | Filter `severity = ERROR`, group by `system_instruction_version` | Matches row 3 of table ✔ |
| 2 | Spike concentrates on version `v14` (*illustrative*) | Points to instruction mismatch, not infra |
| 3 | Compare `temperature` across versions: unchanged | Rules out configuration anomaly ✔ |
| 4 | Roll back to prior instruction version (`06` routing rollback) and fix wording, re-run the golden dataset (`05`) | Closes the AgentOps loop: monitor → build ✔ |

**Takeaway:** Log config + instruction version + usage with each response; filter by those fields to map silent behavioral slips to a concrete root cause.

---

## Common confusions

| Looks like | Difference |
|---|---|
| **Structured vs plain stdout logs** | Plain text = opaque string; single-line JSON = parsed `jsonPayload`, searchable by field |
| **Log Analytics vs Logs Explorer/LQL** | Log Analytics queries with SQL; Logs Explorer uses LQL (the query language) |
| **Prompt drift vs configuration anomaly** | Drift: bad output despite low temperature (rules failing); anomaly: temperature unexpectedly high (config/deploy error) |
| **Configuration vs context vs usage fields** | Config = temperature/top_p/model; context = instructions/templates/user metadata; usage = token counts |
| **Logs vs traces** | Logs = searchable event records with context; traces = nested timeline across steps (`10`) |
| **Deep payload in prod vs dev** | Toggle deep text by environment; always cleanse PII before writing |

> **Gap [4.2]:** The exam also expects monitoring for cost and reliability (alerts, dashboards) and troubleshooting drift in depth; this module gives diagnostic queries but no alert configuration. `Hill Climb Generative and Agentic Systems` (Good to Have) is a suggested follow-up.

Sources: AgentOps course, "Structured logging for nondeterministic systems" (`sources/agent-observability-raw.md`).
