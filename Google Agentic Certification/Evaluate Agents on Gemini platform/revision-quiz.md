# Revision Quiz: Evaluate Agents on Gemini Platform

**32 active-recall questions** covering all 8 notes in this module.
*Tip: Tap **Show Answer** on GitHub Mobile to check your recall.*

---

## 1. Quality Flywheel and Evaluation Modes ([note](notes/01-quality-flywheel-and-evaluation-modes.md))

**Q1 (Compare):** How does a **Trace** differ from a **Session** on the Gemini platform?

<details>
<summary>Show Answer</summary>

**Answer:** A **Trace** is an immutable record of a single agent invocation (one conversational turn), whereas a **Session** is a complete multi-turn conversation grouping one or more traces.
**Why it matters:** Single-turn tool bugs live in a trace, while context loss and compounding errors only show up across a session.
**Ref:** `[4.1]` · [01-quality-flywheel-and-evaluation-modes.md](notes/01-quality-flywheel-and-evaluation-modes.md)

</details>

**Q2 (True / False — Spot the Trap):** Running an evaluation through the Google Cloud console wizard uses different metric definitions than running it via the Agent Platform SDK Client in CI/CD.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Both the console wizard and the Agent Platform SDK Client invoke the **exact same underlying evaluation engine**, guaranteeing identical metric definitions and measurements.
**Why it matters:** You can explore interactively in the console and automate the exact same checks in CI/CD without metric drift.
**Ref:** `[4.1]` `[4.2]` · [01-quality-flywheel-and-evaluation-modes.md](notes/01-quality-flywheel-and-evaluation-modes.md)

</details>

**Q3 (Recall / Cloze):** In the Per-Deployment Workspace (`Deployments > <agent>`), which tab summarizes session/invocation counts and online quality trends, and which tab stores recorded turns for inspection and ad-hoc evaluation?

<details>
<summary>Show Answer</summary>

**Answer:** The **Dashboard** tab summarizes activity and displays quality trend charts (under its Evaluation subsection), while the **Traces** tab stores recorded traces and sessions for turn-by-turn inspection and ad-hoc evaluation.
**Why it matters:** Central controls (Experiments, Metrics, Online monitors) live under `Gemini platform > Agents > Evaluation`, while per-agent raw telemetry lives under the deployment workspace.
**Ref:** `[4.1]` `[4.2]` · [01-quality-flywheel-and-evaluation-modes.md](notes/01-quality-flywheel-and-evaluation-modes.md)

</details>

**Q4 (1-Line Trigger → Choice):** You need to gate a CI/CD release with regression tests before deployment, and later detect quality drift on live production traffic. Which two evaluation modes do you use?

<details>
<summary>Show Answer</summary>

**Answer:** Use **offline evaluation** for pre-release CI/CD regression gates (scoring existing traces, sessions, or simulated sessions), and **online monitoring** to sample and score live production traffic on a recurring schedule.
**Why it matters:** Both modes share the same evaluation engine and metrics, differing only in *when* they run and *what data* they score.
**Ref:** `[4.1]` `[4.2]` · [01-quality-flywheel-and-evaluation-modes.md](notes/01-quality-flywheel-and-evaluation-modes.md)

</details>

---

## 2. Telemetry Contract and Trace Debugging ([note](notes/02-telemetry-contract-and-trace-debugging.md))

**Q5 (Recall / Cloze):** Which **three** environment variables must be enabled on a deployed ADK agent so Agent Runtime sends spans to Cloud Trace and captures message content?

<details>
<summary>Show Answer</summary>

**Answer:**
- `GOOGLE_CLOUD_AGENT_ENGINE_ENABLE_TELEMETRY='true'`
- `OTEL_SEMCONV_STABILITY_OPT_IN='gen_ai_latest_experimental'`
- `OTEL_INSTRUMENTATION_GENAI_CAPTURE_MESSAGE_CONTENT='EVENT_ONLY'`
**Why it matters:** Agents do not record message content by default, and the New experiment banner's `:true` shorthand is not enough—deployment requires the `gen_ai_latest_experimental` + `EVENT_ONLY` pair.
**Ref:** `[4.1]` `[4.2]` · [02-telemetry-contract-and-trace-debugging.md](notes/02-telemetry-contract-and-trace-debugging.md)

</details>

**Q6 (True / False — Spot the Trap):** If an evaluation run completes with empty scores (no scores returned), it means the agent had zero errors and passed cleanly.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Missing required OpenTelemetry attributes does not raise a loud error—it silently removes the signal metrics need, producing an **empty score**.
**Why it matters:** Always treat empty evaluation scores as a **telemetry gap** to investigate in Cloud Trace, never as a clean bill of health.
**Ref:** `[4.2]` · [02-telemetry-contract-and-trace-debugging.md](notes/02-telemetry-contract-and-trace-debugging.md)

</details>

**Q7 (1-Line Trigger → Choice):** Your multimodal agent passes images in trace spans, risking span size limit errors. Which three environment variables offload payloads to Cloud Storage?

<details>
<summary>Show Answer</summary>

**Answer:**
- `OTEL_INSTRUMENTATION_GENAI_UPLOAD_FORMAT='jsonl'`
- `OTEL_INSTRUMENTATION_GENAI_COMPLETION_HOOK='upload'`
- `OTEL_INSTRUMENTATION_GENAI_UPLOAD_BASE_PATH='gs://STORAGE_BUCKET_NAME/PATH'`
**Why it matters:** Offloading keeps spans within size limits while still letting evaluators read full multimodal content (omit these for text-only agents).
**Ref:** `[4.1]` `[4.2]` · [02-telemetry-contract-and-trace-debugging.md](notes/02-telemetry-contract-and-trace-debugging.md)

</details>

**Q8 (Compare):** What is the difference between a **root turn** and a **symptom turn** when debugging a trace, and which OpenTelemetry attribute links single traces into a multi-turn session?

<details>
<summary>Show Answer</summary>

**Answer:** The **symptom turn** is where the bad final answer appears, while the **root turn** is the earliest upstream turn where an unconfirmed assumption or bad decision caused the downstream failure. Traces are linked into a session via `gen_ai.conversation.id` on the Invoke Agent span.
**Why it matters:** Fixing wording on the symptom turn leaves the bug intact; backtracking to the root turn reveals the real fix.
**Ref:** `[4.2]` · [02-telemetry-contract-and-trace-debugging.md](notes/02-telemetry-contract-and-trace-debugging.md)

</details>

---

## 3. Scenario Generation and the User Simulator ([note](notes/03-scenario-generation-and-user-simulator.md))

**Q9 (Recall / Cloze):** What are the **two** components of a generated eval case, and which one is hidden from the agent?

<details>
<summary>Show Answer</summary>

**Answer:** A **starting prompt** (visible initial user message) and a **conversation plan** (hidden background instruction guiding how the simulated user behaves across subsequent turns).
**Why it matters:** A generated eval case is a dynamic conversation specification for the User Simulator, not a static transcript.
**Ref:** `[4.1]` · [03-scenario-generation-and-user-simulator.md](notes/03-scenario-generation-and-user-simulator.md)

</details>

**Q10 (Recall / Cloze):** What is the default **Max turn** cap for the User Simulator to prevent infinite loops?

<details>
<summary>Show Answer</summary>

**Answer:** **5** turns (configurable—can be increased per run).
**Why it matters:** Knowing the default cap helps you avoid prematurely truncated multi-turn scenarios when longer interactions are needed.
**Ref:** `[4.1]` · [03-scenario-generation-and-user-simulator.md](notes/03-scenario-generation-and-user-simulator.md)

</details>

**Q11 (True / False — Spot the Trap):** During a **Simulate sessions** run, the Gemini platform automatically provides a built-in tool sandbox so simulated conversations cannot affect live production backends.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** In a simulated run, **only the user is simulated**—there is **no built-in tool sandbox**, and every tool the agent calls executes live against its configured backend.
**Why it matters:** Always run simulations against an agent version with read-only/safe tools or point tool configurations at non-production backends.
**Ref:** `[4.1]` · [03-scenario-generation-and-user-simulator.md](notes/03-scenario-generation-and-user-simulator.md)

</details>

**Q12 (1-Line Trigger → Choice):** You have a brand-new agent with zero eval cases and zero production traffic, and you want reproducible A/B comparisons across future versions. What two features do you use?

<details>
<summary>Show Answer</summary>

**Answer:** Register an immutable snapshot in the **Agent Registry** (freezing instructions, tools, and model), and select **Simulate sessions** with **Generation guidance** in the New experiment wizard.
**Why it matters:** Scenario generation solves the day-zero cold-start gap directly from the agent's instructions and tool definitions, while Agent Registry versions keep A/B runs comparable.
**Ref:** `[4.1]` · [03-scenario-generation-and-user-simulator.md](notes/03-scenario-generation-and-user-simulator.md)

</details>

---

## 4. Grading Metrics and the Metric Registry ([note](notes/04-grading-metrics-and-registry.md))

**Q13 (Recall / Cloze):** Which **four** predefined single-turn metrics are added by default in a New Experiment, and how do predefined multi-turn metrics behave at setup?

<details>
<summary>Show Answer</summary>

**Answer:** The four default single-turn metrics are `Agent Final Response Quality`, `Agent Hallucination`, `Agent Tool Use Quality`, and `Safety`. Multi-turn metrics (`Agent Multi-turn Task Success`, `Agent Multi-turn Tool Use Quality`, `Agent Multi-turn Trajectory Quality`) are **not** added by default and must be added explicitly.
**Why it matters:** Relying only on defaults misses multi-turn failures like compounding wrong assumptions across turns.
**Ref:** `[4.1]` · [04-grading-metrics-and-registry.md](notes/04-grading-metrics-and-registry.md)

</details>

**Q14 (Compare):** How does an **Adaptive rubric metric** (e.g., `Agent Multi-turn Task Success`) differ from a **Static rubric metric** (e.g., `Agent Hallucination`, `Safety`)?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Adaptive rubric metric:** Dynamically generates unique pass/fail rubrics per eval case; outputs passing rate, per-rubric **Pass/Fail** verdicts, and a **rationale**.
- **Static rubric metric:** Applies one fixed scoring scheme across all cases; outputs a single **0-to-1** numeric score and an **explanation**.
**Why it matters:** Expecting a 0-to-1 scale on adaptive metrics (or per-case unit-test verdicts on static metrics) misreads the evaluation report.
**Ref:** `[4.1]` · [04-grading-metrics-and-registry.md](notes/04-grading-metrics-and-registry.md)

</details>

**Q15 (1-Line Trigger → Choice):** You need to verify both (a) that a tool returned valid structured JSON and (b) that the agent's reply stayed calm and empathetic under pressure. Which custom metric type fits each?

<details>
<summary>Show Answer</summary>

**Answer:** Use a **Custom code metric** (deterministic Python) for JSON schema compliance, and a **Custom LLM metric** (judge model + rubric) for empathetic tone.
**Why it matters:** Custom code metrics are faster, cheaper, and deterministic when a criterion has a verifiable right answer in code; reserve LLM judges for subjective judgment.
**Ref:** `[4.1]` · [04-grading-metrics-and-registry.md](notes/04-grading-metrics-and-registry.md)

</details>

**Q16 (True / False — Spot the Trap):** To adjust a metric's rubric in the Metric Registry (`Evaluation > Metrics`) without disrupting existing workflows, edit the metric directly in place.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Never mutate a shared metric directly if other experiments or online monitors depend on it—use **More options > Duplicate** and edit the copy.
**Why it matters:** Because offline experiments and online monitors share the Metric Registry, in-place edits silently alter live monitoring baselines.
**Ref:** `[4.1]` · [04-grading-metrics-and-registry.md](notes/04-grading-metrics-and-registry.md)

</details>

---

## 5. Offline Evaluation and Result Triage ([note](notes/05-offline-evaluation-and-triage.md))

**Q17 (Compare):** When should you use **Bulk evaluation** vs. **Ad-hoc evaluation** in the console, and where do you launch each?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Bulk evaluation** (`Evaluation` page → **New Experiment** wizard): Grades many captured traces/sessions at once to benchmark an agent **Version** or **time window**.
- **Ad-hoc evaluation** (`Traces` tab → **Session view** → select conversation → **Evaluation** sub-tab → **Evaluate**): Grades a single suspicious trace or session on demand.
**Why it matters:** Ad-hoc evaluation lets you debug one multi-turn conversation immediately without waiting on a full experiment batch.
**Ref:** `[4.1]` · [05-offline-evaluation-and-triage.md](notes/05-offline-evaluation-and-triage.md)

</details>

**Q18 (Recall / Cloze):** What are the **four steps** of the console triage drill-down workflow from aggregate scores to the root cause?

<details>
<summary>Show Answer</summary>

**Answer:**
1. **Summary Metrics** (Evaluation Report)
2. **Per-Case Results**
3. **Verdicts and Rationale** (Pass/Fail verdicts or 0–1 score + explanation)
4. **Trace → Root Turn** (backtrack turn-by-turn to the earliest failing turn)
**Why it matters:** Top-down triage keeps hundreds of eval cases manageable and prevents fixing symptoms instead of root turns.
**Ref:** `[4.1]` `[4.2]` · [05-offline-evaluation-and-triage.md](notes/05-offline-evaluation-and-triage.md)

</details>

**Q19 (1-Line Trigger → Choice):** In the **New Experiment** wizard, you want to evaluate already-recorded multi-turn conversations (not single turns or synthetic runs). Which data source tab do you choose, and what GCS field must be configured?

<details>
<summary>Show Answer</summary>

**Answer:** Choose the **Sessions** tab (not Traces or Simulate sessions) and set **`Output private data path`** to a Cloud Storage URI (`gs://...`).
**Why it matters:** Selecting **Traces** only grades single turns, and omitting `Output private data path` causes runs to fail or return empty results.
**Ref:** `[4.1]` · [05-offline-evaluation-and-triage.md](notes/05-offline-evaluation-and-triage.md)

</details>

**Q20 (Recall / Cloze):** If an offline experiment returns **no scores at all**, what **three** troubleshooting checks should you perform?

<details>
<summary>Show Answer</summary>

**Answer:**
1. Open the trace in **Cloud Trace** and verify `gen_ai.agent.name` and `gen_ai.conversation.id` are on the invoke-agent span.
2. Confirm **`Output private data path`** (Cloud Storage URI) is set.
3. Confirm your environment is authenticated before re-running.
**Why it matters:** Empty scores are almost always missing telemetry, unset GCS output, or auth issues—not an evaluation engine bug.
**Ref:** `[4.2]` · [05-offline-evaluation-and-triage.md](notes/05-offline-evaluation-and-triage.md)

</details>

---

## 6. Online Monitoring and Quality Drift Alerting ([note](notes/06-online-monitoring-and-drift-alerting.md))

**Q21 (Recall / Cloze):** Which **two** settings control cost in an online monitor (`OnlineEvaluator`), and which filter feature lets you preview matching live traces before saving?

<details>
<summary>Show Answer</summary>

**Answer:** **Sampling percentage** and **Max samples per run** control cost; the **Initial Inspection** timeframe previews which live traces match your **Agent engine** and **Filter criteria** before saving.
**Why it matters:** Scoring 100% of production traffic is expensive, and Initial Inspection prevents deploying a monitor whose filter matches zero traces.
**Ref:** `[4.2]` · [06-online-monitoring-and-drift-alerting.md](notes/06-online-monitoring-and-drift-alerting.md)

</details>

**Q22 (True / False — Spot the Trap):** Leaving **Use notification channels** unchecked when creating a quality alerting policy disables the policy so no incidents are recorded.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Incidents are still created and logged under **Cloud Monitoring (`Monitoring > Alerting`)**—they just do not page anyone via Slack, email, or Pub/Sub.
**Why it matters:** Without notification channels, quality drift sits silently in Cloud Monitoring instead of alerting responders.
**Ref:** `[4.2]` · [06-online-monitoring-and-drift-alerting.md](notes/06-online-monitoring-and-drift-alerting.md)

</details>

**Q23 (Compare):** How do **Recommended Alerts** (`Online Monitor - Low evaluation score`) differ from a **per-monitor alerting policy** (`Online monitors > More options > Create alerting policy`)?

<details>
<summary>Show Answer</summary>

**Answer:** **Recommended Alerts** provides broad baseline coverage across **all** active monitors, whereas a **per-monitor alerting policy** creates one template per metric for tighter thresholds on a **single** monitor.
**Why it matters:** Combine both: Recommended Alerts as a safety net, plus per-monitor policies on critical metrics like task success.
**Ref:** `[4.2]` · [06-online-monitoring-and-drift-alerting.md](notes/06-online-monitoring-and-drift-alerting.md)

</details>

**Q24 (1-Line Trigger → Choice):** Why must monitor-creation IAM permissions be restricted to authorized administrators, and where do you check diagnostic errors (`error.type`, `error.message`) for an empty monitor?

<details>
<summary>Show Answer</summary>

**Answer:** An `OnlineEvaluator` runs under the **project service account**, so anyone with monitor-creation permission can attach it to *any* agent in the project. Check diagnostic errors in **Logs Explorer** (`error.type` and `error.message`).
**Why it matters:** Unrestricted monitor creation is an IAM/cost-exposure risk across all agents in the project.
**Ref:** `[4.2]` · [06-online-monitoring-and-drift-alerting.md](notes/06-online-monitoring-and-drift-alerting.md)

</details>

---

## 7. Failure Clustering, Optimization, and Closing the Flywheel ([note](notes/07-failure-clustering-and-optimization.md))

**Q25 (Recall / Cloze):** What is **Automatic Loss Analysis** called in the Python SDK, what method triggers it, and which **two** metrics support issue clustering in the experiment setup UI?

<details>
<summary>Show Answer</summary>

**Answer:** Called **loss clusters** in the Python SDK, triggered via `client.evals.generate_loss_clusters`. In the experiment setup UI, issue clustering is supported for **Multi-turn Task Success** and **Multi-turn Tool Use Quality**.
**Why it matters:** In the console, Automatic Loss Analysis runs automatically when recurring rubric failures exist; in the SDK, you invoke it explicitly.
**Ref:** `[4.2]` · [07-failure-clustering-and-optimization.md](notes/07-failure-clustering-and-optimization.md)

</details>

**Q26 (True / False — Spot the Trap):** When triaging Failure Clusters, start with the largest cluster first and click the console's Optimize button to refine the agent's instructions.

<details>
<summary>Show Answer</summary>

**Answer:** **False** on both counts: start triage at the **lowest-scoring summary metric** (not necessarily the largest cluster), and there is **no console launch button** for optimization—it runs programmatically via the **SDK and ADK tooling**.
**Why it matters:** The lowest-scoring metric usually points to the highest-impact fix, and optimization uses seeding inputs inspected from the console.
**Ref:** `[4.1]` `[4.2]` · [07-failure-clustering-and-optimization.md](notes/07-failure-clustering-and-optimization.md)

</details>

**Q27 (Recall / Cloze):** What are the **four** elements displayed on every Failure Cluster?

<details>
<summary>Show Answer</summary>

**Answer:** **Category** (e.g., `Instruction Following`, `Tool Calling`), **Loss Pattern**, **Example Scenarios**, and **Rationale**.
**Why it matters:** These four fields let you fix a systemic root cause once rather than triaging individual failing cases one by one.
**Ref:** `[4.2]` · [07-failure-clustering-and-optimization.md](notes/07-failure-clustering-and-optimization.md)

</details>

**Q28 (1-Line Trigger → Choice):** After refining your agent's system instructions via SDK/ADK to fix a failure cluster, how do you verify the fix and close the Quality Flywheel Lap?

<details>
<summary>Show Answer</summary>

**Answer:** Register the updated agent version in **Agent Registry** and re-run the **exact same offline evaluation** on the **original seeded suite** across **all metrics** to confirm the cluster shrinks/disappears without regressing other metrics.
**Why it matters:** Swapping in a new suite breaks before/after comparability, and hill-climbing can overfit or regress other metrics if you don't track the full suite.
**Ref:** `[4.1]` `[4.2]` · [07-failure-clustering-and-optimization.md](notes/07-failure-clustering-and-optimization.md)

</details>

---

## 8. ADK Build-Time Evaluation and Graduation to Managed Eval ([note](notes/08-adk-build-time-evaluation.md))

**Q29 (Compare):** How does an ADK **`*.test.json`** file differ from an **`*.evalset.json`** file?

<details>
<summary>Show Answer</summary>

**Answer:**
- **`*.test.json`:** Holds a **single session**; runs **locally** as a unit test with **no billing** (best for fast inner-loop checks).
- **`*.evalset.json`:** Holds **multiple, longer multi-turn sessions**; runs on the metered/billed **Gen AI evaluation service** (best for broader integration testing).
**Why it matters:** Both share the same Pydantic data model (`EvalCase` → `Invocation`), so local tests graduate cleanly to larger integration suites.
**Ref:** `[4.1]` · [08-adk-build-time-evaluation.md](notes/08-adk-build-time-evaluation.md)

</details>

**Q30 (Recall / Cloze):** Which **two** ADK metrics run locally for free and are the only metrics with built-in default thresholds (include their defaults)?

<details>
<summary>Show Answer</summary>

**Answer:** `tool_trajectory_avg_score` (default **`1.0`**, `EXACT` match) and `response_match_score` (default **`0.8`**).
**Why it matters:** Every LLM-judge metric (`final_response_match_v2`, `hallucinations_v1`, etc.) and Gen AI evaluation service metric (`multi_turn_*`, `safety_v1`) requires an **explicit threshold** in `test_config.json`.
**Ref:** `[4.1]` · [08-adk-build-time-evaluation.md](notes/08-adk-build-time-evaluation.md)

</details>

**Q31 (Compare):** What is the difference between **`num_runs`** (in `AgentEvaluator.evaluate()`) and **`num_samples`** (in `judge_model_options`)?

<details>
<summary>Show Answer</summary>

**Answer:** **`num_runs`** repeats the **agent** execution multiple times to smooth out agent nondeterminism in CI, whereas **`num_samples`** averages multiple **judge model** evaluations for a single response.
**Why it matters:** Confusing the two either fails to stabilize flaky agent runs or fails to stabilize noisy LLM-judge grading.
**Ref:** `[4.1]` · [08-adk-build-time-evaluation.md](notes/08-adk-build-time-evaluation.md)

</details>

**Q32 (Compare):** How do **`EXACT`**, **`IN_ORDER`**, and **`ANY_ORDER`** match types differ for `tool_trajectory_avg_score`?

<details>
<summary>Show Answer</summary>

**Answer:**
- **`EXACT`:** Requires identical tool calls in the exact sequence (default).
- **`IN_ORDER`:** Allows extra tool calls as long as all expected calls appear in order.
- **`ANY_ORDER`:** Requires the expected tool calls regardless of sequence.
**Why it matters:** Choosing `IN_ORDER` or `ANY_ORDER` prevents brittle CI failures when an agent makes valid extra diagnostic calls.
**Ref:** `[4.1]` · [08-adk-build-time-evaluation.md](notes/08-adk-build-time-evaluation.md)

</details>
