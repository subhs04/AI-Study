# Canary Deployments, Traffic Splitting and Instant Rollback

**Module map:** Why offline evaluation is not enough, how to run a **canary release** with traffic splitting on Agent Runtime, the **three metric categories** to watch, and how an **instant rollback** works. Covers **4.2** (deploy and scale, troubleshoot latency and loops, monitor), **4.1** (online evaluation). Prerequisites: `01-agentops-lifecycle.md` (canary vs blue-green), `02-build-and-scale-platform.md` (Agent Runtime), `05-cicd-pipeline-quality-gates.md`. Next: `07-iam-service-accounts-authn-authz.md` (security half), then `10-agent-tracing-opentelemetry.md` for the tracing and logging behind the Cloud Monitoring/OpenTelemetry mentions here.

---

## 1. Offline vs online evaluation [4.1][4.2]

**What/why:** The quality gate (`05`) is **offline evaluation**: it catches *known* regressions using a golden dataset. But no dataset fully simulates live human behavior (slang, multipart questions, unexpected combinations). A **100% all-at-once rollout** is therefore a major operational risk. **Online evaluation** tests the new version on real traffic, in small doses.

| | Offline evaluation | Online evaluation |
|---|---|---|
| Where | CI/CD staging gate | Production, via canary |
| Data | Golden dataset | Real live customers |
| Catches | Known regressions | Unforeseen, real-world errors |
| Risk to users | None | Limited to the canary slice |

**Takeaway:** Quality gates + canary = dual-layered safety: offline catches known bugs, online catches the unpredictable.

---

## 2. Canary deployment with traffic splitting [4.2]

**What it is:** Release the new version to a **small slice of live traffic** while the rest stays on the stable version. The source also calls this **A/B experimentation in production**. **Agent Runtime** (managed, autoscaling) provides built-in services for **environment routing** to do the split.

```
                 ┌── 90% ──► v1 (control arm, stable)
 live traffic ───┤
                 └── 10% ──► v2 (treatment arm, new, unproven)
                         │
                 monitor KPIs in aggregate ──► healthy? ► ramp to 100%
                                            └ anomaly?  ► route 100% back to v1
```

| Term | Meaning |
|---|---|
| **Control arm** | Users still on the older stable version (v1), e.g. 90% |
| **Treatment arm** | Users on the new version (v2), e.g. 10% |
| **Traffic splitting** | Routing a defined percentage of requests to each version |

Benefit: unforeseen errors only hit a small fraction of users, and you can watch KPIs **in aggregate** before a full rollout.

> **Clarification (not in source):** Strictly, *canary* (limit blast radius during rollout) and *A/B test* (compare two variants) are distinct ideas; the source treats them as one in this lesson. For the exam, if a question says "send a small share of live traffic to the new version," that is the canary pattern.

**Takeaway:** Canary = control (v1) vs treatment (v2) via Agent Runtime routing, limiting blast radius.

---

## 3. What to measure during a canary [4.2]

**Why:** Uptime dashboards are not enough: an agent can be **fully operational yet fail silently**, giving confident but inaccurate or low-quality answers (an AI "keeps talking with absolute confidence" instead of crashing). So track three categories:

| Category | Measures | Examples from source | Failure signal |
|---|---|---|---|
| **Business metrics** | High-level outcomes: is the agent driving intended behavior/value? | Cart abandonment, conversion | New version **20% worse** than baseline → consider reverting |
| **Application telemetry** | Performance health "under the hood" | **Token usage, response latency, output size, database connection errors / backend errors** | Lag spikes, token blowups, backend drops |
| **Human feedback** | **Explicit** and **implicit** user signals; sentiment | User says the agent isn't listening, asks for a human representative | Agent failing even though **no code errors** fired |

**Takeaway:** Business metrics (value) + application telemetry (health) + human feedback (sentiment). All three, not just uptime.

---

## 4. Instant rollback [4.2]

**What/why:** Generative AI mistakes are sneaky and you cannot wait hours to ship a code fix. With **environment routing in Agent Runtime**, operations can (or an **automated trigger** can) update a **routing rule** to send traffic back to the stable version immediately, with **zero downtime**.

Source scenario, step by step:

| Step | Event |
|---|---|
| 1 Release | DevOps routes **10%** of mobile app traffic to concierge **v2** |
| 2 Anomaly | Within **15 minutes**, **Cloud Monitoring** detects v2 latency **12 s** vs v1 **2 s** |
| 3 Rollback | Ops team (or automated trigger) updates the Agent Runtime routing rule: **100% back to v1**, zero downtime |
| 4 Investigation | Team isolates the **OpenTelemetry logs** from the canary's 10% of traffic; trace data shows v2's new formatting instructions made the agent **loop while parsing the Inventory API's JSON response** |
| Result | Nondeterministic performance issue found and resolved; **90% of users never affected**; isolated traces preserved for safe debugging |

**Takeaway:** Rollback is a routing change, not a redeploy, and the canary's traces remain isolated for root-cause analysis.

---

## 5. Worked example: decide and act [4.2]

Scenario (*illustrative numbers except where stated*): v2 is at 10% traffic. Dashboards show: CPU normal, no errors, latency 3 s vs 2 s, cart conversion down 22%, three chat transcripts asking for a human.

| Signal | Category | Verdict |
|---|---|---|
| CPU normal, no errors | Infra uptime | Looks fine but is **insufficient** evidence |
| Conversion −22% | Business metric | Worse than the 20% revert example → **roll back** |
| Human-agent requests | Human feedback | Silent failure confirmed ✔ |
| Latency 3 s vs 2 s | Telemetry | Supports the decision |

Action: route 100% back to v1 via Agent Runtime routing rule; investigate traces from the 10% slice. Cross-check: the example would have been missed by uptime dashboards alone (key point of §3).

---

## Common confusions

| Looks like | Difference |
|---|---|
| **Canary vs blue-green** (from `01`) | Canary = fraction of live traffic to new version; blue-green = two full environments, instant switch. Here, the rollback is also instant because it's a routing-rule change |
| **Offline vs online evaluation** | Offline = golden dataset in CI/CD; online = live traffic canary |
| **Control vs treatment arm** | Control = old stable v1; treatment = new v2 |
| **Rollback vs redeploy** | Rollback re-routes traffic to the still-running stable version; it does not require rebuilding |
| **Application telemetry vs business metrics** | Telemetry = tokens, latency, DB errors; business = cart abandonment, conversion |
| **Cloud Monitoring vs OpenTelemetry logs** | In the source, Cloud Monitoring *detected* the latency anomaly; OpenTelemetry logs/traces *explained* it |

> **Gap [4.2]:** The source does not detail how to configure routing in Agent Runtime, nor compare it with Cloud Run/GKE traffic splitting. The exam expects runtime selection (Agent Runtime vs Cloud Run vs GKE). Cover from official docs or the deployment course. Cloud Logging/Cloud Trace (exam in-scope) are not named here; they are covered in `10-agent-tracing-opentelemetry.md` and `11-structured-logging-cloud-logging.md`.

Sources: AgentOps course, "Canary deployments and traffic splitting" (`sources/ci-cd-and-quality-assurance-raw.md`).
