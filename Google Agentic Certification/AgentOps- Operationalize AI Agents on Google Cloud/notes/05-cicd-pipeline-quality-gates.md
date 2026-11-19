# CI/CD Pipeline with Automated Quality Gates (Cloud Build)

**Module map:** The five-step CI/CD pipeline for agents, and how a **quality gate** in staging uses golden-dataset evaluation to block bad releases. Covers **4.1** (continuous evaluation pipelines, evaluating against golden data), **4.2** (deployment, regression prevention). Cloud Build itself is not on the exam guide's in-scope tool list; know the *pattern*. Prerequisites: `01-agentops-lifecycle.md`, `04-evaluation-as-unit-tests.md`. Next: `06-canary-deployments-and-rollback.md`.

---

## 1. What it is and why [4.1][4.2]

**What/why:** Manual agent updates are risky: a prompt tweak can silently break behavior. A **CI/CD pipeline** (continuous integration / continuous delivery) automates the path from commit to production, and a **quality gate** is a pipeline step that **must pass** or the whole pipeline stops. Here the gate is an **evaluation** run, so only agents that pass rigorous AI evaluation reach production.

Terms defined:
- **Cloud Build:** Google Cloud's CI/CD build service; pipelines are defined as **configuration as code** (typically `cloudbuild.yaml`).
- **Blocking quality gate:** a failing step that halts deployment.
- **Smoke test:** a quick check that the basics work after deploying.
- **Staging environment:** an environment that **mirrors production** used for final verification.

**Takeaway:** Quality gate = automated evaluation in staging that fails the build below a threshold.

---

## 2. The five-step pipeline [4.2]

```
 commit ─► 1 Validate & test ─► 2 Build & deploy to DEV ─► 3 Manual approval ─►
           4 Deploy to STAGING + run evaluation  ◄── QUALITY GATE ──►
           5 Production deploy (metadata auto-registered)
```

| # | Step | What happens | Gate type |
|---|---|---|---|
| 1 | **Validate and test** | Check repository structure meets enterprise standards; run **static code analysis** and **traditional unit tests** on Python functions and tools, before they are registered | Automated (code-level) |
| 2 | **Build and deploy to dev** | Package agent code, dependencies and **prompt configurations** into an artifact (e.g., **container image**); automatically **provision infrastructure** and deploy to **development** for smoke tests and developer verification | Automated |
| 3 | **Manual approval gate** | Engineers run manual tests and verify basic functionality before advancing | **Human** |
| 4 | **Deploy to staging and run evaluation** | Staging mirrors production. Runs **evaluation-at-scale scripts, stress tests, integration tests**. Results stored centrally in an **AI governance environment** for analysis | **Automated quality gate** |
| 5 | **Production deployment** | If metrics meet thresholds, a **final manual approval gate** is cleared and the agent deploys. **Metadata from the repository is automatically registered** in the enterprise **Tool Registry** | Human + automated |

Note: prompt configurations are packaged as part of the artifact, so prompt changes go through the same pipeline as code.

> **Clarification (not in source):** The source says "Tool Registry" here, while the previous lessons call the catalog **Agent Registry** (see `03-govern-and-optimize-platform.md`). They most likely refer to the same registry; verify in the official docs.

**Takeaway:** Validate → build/dev → human approval → staging eval gate → prod with auto-registration. Two human approvals (step 3, and the final one in step 5), one automated quality gate (step 4).

---

## 3. Mechanics of the quality gate (step 4) [4.1]

How Cloud Build enforces it: a pipeline step runs your **Python evaluation script** (the one prototyped in the lab) instead of only building a Docker container.

| # | Action | Detail |
|---|---|---|
| 1 | **Trigger the eval** | Cloud Build temporarily deploys the new agent code to **staging** |
| 2 | **Run the golden dataset** | Script feeds hundreds of golden prompts to the staging agent and collects responses |
| 3 | **Calculate the score** | Calls **Gemini Enterprise Agent Platform Evals** to grade the results |
| 4 | **Pass/fail logic** | Simple check, e.g. `if groundedness_score < 4.0: exit(1)`. Non-zero exit → script error → **Cloud Build fails the whole pipeline** → deployment halted |

```
 staging agent ◄── golden prompts ──┐
      │ responses                    │
      ▼                              │
 evaluation service ──► score ──► threshold check ──► exit(1)? ──► pipeline red, deploy halted
                                                         └─ ok ──► continue to production step
```

**Takeaway:** The gate is just code: score below threshold → non-zero exit → pipeline fails. Threshold values are *your* design choice (4.0 and 4.5 in the source examples).

> **Clarification (not in source):** The source uses "Gemini Enterprise Agent Platform Evals" here and "Agent Platform Generative AI Evaluation Service" in the lab title. They very likely refer to the same evaluation capability (the exam guide's "Gen AI evaluation service"). Verify the naming in the official docs.

---

## 4. Worked example: how the gate would have prevented an outage [4.1][4.2]

Source scenario (scores are the source's values):

| Step | What happened | Cross-check |
|---|---|---|
| Mistake | AI engineer edits the Digital Concierge system prompt to be "more conversational", pushes code | A prompt change is a code change in the repo ✔ |
| Pipeline | Cloud Build triggers; **step 1 passes** (no syntax errors); deploys to dev and staging | Unit tests only check code, so they cannot see behavioral breakage ✔ |
| Gate | Step 4 evaluation: new prompt made the LLM **skip `check_inventory`**. Tool-call accuracy **1.2 / 5** vs required threshold **4.5** | Trajectory/tool-use lens (`04`) caught it ✔ |
| Block | Cloud Build fails, deployment halted, engineers alerted, they see exactly which evaluation failed | Gate behavior from §3 ✔ |

Result: broken prompt never reaches production.

---

## Common confusions

| Looks like | Difference |
|---|---|
| **Step 1 unit tests vs step 4 evaluation** | Step 1 = code correctness (syntax, functions); step 4 = agent behavior quality vs golden dataset. The outage example passed step 1 and failed step 4 |
| **Manual approval gate (step 3) vs quality gate (step 4)** | Step 3 is human, basic functionality in dev; step 4 is automated evaluation in staging with numeric thresholds |
| **Dev vs staging** | Dev = initial smoke tests/developer checks; staging = mirrors production, runs the evaluation gate. (Matches the dev → staging → prod idea; the earlier note's unverified environment clarification is now supported by this pipeline) |
| **Quality gate vs canary** | Quality gate = *offline* evaluation before release (known regressions); canary = *online* test on live traffic (see `06`) |
| **Cloud Build config vs agent code** | `cloudbuild.yaml` defines pipeline steps; the evaluation script is a separate Python script it executes |

Sources: AgentOps course, "Automated Quality Gates with Cloud Build" (`sources/ci-cd-and-quality-assurance-raw.md`).
