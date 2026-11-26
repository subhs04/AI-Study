# Online Monitoring and Quality Drift Alerting

**Module map:** Covers **[4.1]** (continuous production evaluation) and **[4.2]** (monitoring live agents, reading dashboard/trace quality, troubleshooting monitors, and alerting on quality drift).
**Prerequisites:** [05-offline-evaluation-and-triage.md](05-offline-evaluation-and-triage.md). **Next:** [07-failure-clustering-and-optimization.md](07-failure-clustering-and-optimization.md).

---

## 1. Why Online Monitoring Exists `[4.1]` `[4.2]`

**What it is:** An **online monitor** (backed by an `OnlineEvaluator` resource) is a scheduled job that continuously queries a sample of live production traces from **Cloud Trace** and **Cloud Logging**, evaluates them against registered metrics, and plots the scores over time.

**Why it exists:** Offline evaluation measures a version against a fixed suite, but user behavior and data shift in production. Quality can **drift** downward even when the underlying model and code have not changed. Online monitoring catches drift as a falling score before it becomes a support escalation.

> **Takeaway:** Online monitors detect production **quality drift** over time—even when your deployed model and code haven't changed.

---

## 2. Creating, Securing, and Managing Online Monitors `[4.2]`

Navigate to `Evaluation > Online monitors > New monitor`. The form has three sections:

1. **Filter:**
   - Select the **Agent engine** (the console UI label for a deployed **Agent Runtime**).
   - Choose **All traces** or specify **Filter criteria** (e.g., `Duration > 2` or token usage).
   - Use **Initial Inspection** timeframe to preview which live traces match before saving.
2. **Metrics:** Select metrics from the Metric Registry (e.g., `response quality`, `safety`, `hallucination`, `tool use quality`).
3. **Sampling (Cost Control):** Scoring 100% of production traffic is expensive. Set **Sampling percentage** to score a fraction of traffic and **Max samples per run** to cap total volume.

### Security & IAM Warning
An `OnlineEvaluator` runs under the **project service account**. Anyone with permission to create a monitor can attach it to *any* agent in the project—restrict monitor-creation IAM permissions to authorized administrators.

### Ongoing Lifecycle Management
From the **Online monitors** list (`More options` on each row):
- **Pause / Resume** scheduled runs without deleting the configuration.
- **Duplicate** an existing monitor to iterate safely on filters, metrics, or sampling rates.
- **Create alerting policy** directly from a monitor.
- **View traces** link: jumps to the `Traces` tab pre-filtered to that monitor's sampled traffic.

### Troubleshooting an Empty Monitor (Three Checks)
If a monitor produces no scores, do not assume traffic is healthy:
1. **Verify telemetry:** Open a live trace in **Cloud Trace** and confirm `gen_ai.agent.name`, `gen_ai.conversation.id` (invoke-agent span), and `gen_ai.input.messages`, `gen_ai.output.messages` (inference events) are present.
2. **Re-check the filter:** Confirm the **Agent engine** and **Filter criteria** match real traffic using **Initial Inspection**.
3. **Read diagnostic logs:** Check the monitor's error logs in **Logs Explorer** (`error.type` and `error.message`).

> **Takeaway:** Control online monitoring cost with **Sampling percentage** and **Max samples per run**, restrict monitor-creation IAM permissions, and diagnose empty monitors via Cloud Trace, Initial Inspection, and Logs Explorer (`error.type`/`error.message`).

---

## 3. Reading Production Quality and Alerting on Drift `[4.2]`

### Dashboard Trends → Per-Trace Inspection
1. **Aggregate trend (`Deployment > Dashboard > Evaluation`):** Charts each metric over time with **one line per online monitor**. A dip in a line pinpoints when quality dropped. (If no monitor is active, a zero-state banner flags the coverage gap.)
2. **Per-trace diagnosis (`Deployment > Traces > select session/trace > Evaluation tab`):** Shows per-metric scores and the **judge's rationale**, which explicitly **names the turn behind the score**—letting you separate real regressions from statistical noise.

### Three Ways to Create a Quality Alert

| Approach | Console / Tooling Path | Best For |
| :--- | :--- | :--- |
| **1. Broad coverage** | `Deployments > Dashboard > Evaluation > Recommended Alerts` (`Online Monitor - Low evaluation score`) | Baseline guardrail across **all** active monitors |
| **2. Targeted control** | `Online monitors > More options > Create alerting policy` (one template per metric on that monitor) | Tighter thresholds on critical metrics (e.g., task success) on a **single** monitor |
| **3. Version-controlled policy** | `gcloud` CLI or **Cloud Monitoring API** | Infrastructure-as-code / GitOps alert management |

### Notification Channels & Incident Routing
- Attach **Notification Channels** (**Slack**, **email**, or **Pub/Sub**) so alerts page responders automatically.
- If **Use notification channels** is left unchecked, the policy still records incidents under **Cloud Monitoring (`Monitoring > Alerting`)**—where you can enable, disable, or edit policies—but pages no one.
- Every incident carries the **originating monitor's labels**, allowing responders to jump directly back to the conversations that triggered the drift.

**Worked Example (Google Cloud Support Agent):**
You enable **Recommended Alerts** (`Online Monitor - Low evaluation score`) for baseline coverage, plus a targeted per-monitor policy on `Agent Multi-turn Task Success` routed to Slack.
- *Cross-check:* When a dip appears on the Dashboard Evaluation chart, the Slack alert fires with the monitor's labels. Opening `Traces > Evaluation` shows the judge's rationale naming Turn 1 as the failing turn.

> **Takeaway:** Combine **Recommended Alerts** for broad baseline coverage with per-monitor policies on critical metrics, and attach **Notification Channels** (Slack, email, Pub/Sub) so drift pages someone instead of sitting silently in `Monitoring > Alerting`.

---

## Common Confusions

| Item A | Item B | Key Difference |
| :--- | :--- | :--- |
| **Agent engine** (UI label) | **Agent Runtime** | *Agent engine* is the console filter label for a deployed *Agent Runtime*. |
| **Recommended Alerts** | **Per-monitor alerting policy** | Recommended Alerts covers all active monitors broadly; per-monitor (`More options > Create alerting policy`) targets one metric on one monitor. |
| **Unchecked `Use notification channels`** | **Disabled policy** | Unchecking notification channels still creates and logs incidents in `Monitoring > Alerting`, it just doesn't page anyone. |
