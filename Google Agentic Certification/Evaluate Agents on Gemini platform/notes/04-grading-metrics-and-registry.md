# Grading Metrics and the Metric Registry

**Module map:** Covers **[4.1]** (determining evaluation tooling/metrics, predefined vs custom autoraters and code checks, rubric-based grading).
**Prerequisites:** [03-scenario-generation-and-user-simulator.md](03-scenario-generation-and-user-simulator.md). **Next:** [05-offline-evaluation-and-triage.md](05-offline-evaluation-and-triage.md).

---

## 1. Rubrics, Metrics, and the Metric Registry `[4.1]`

**What it is:**
- A **rubric** is a single pass-or-fail criterion—like a unit test on a response.
- A **metric** is a rule that aggregates one or more rubrics to turn recorded trace data into an actionable score.
- The **Metric Registry** (located on `Evaluation > Metrics`) is the centralized repository where both predefined and custom metrics are stored and managed.

**Why it exists:** Registering a metric once in the Metric Registry lets you reuse the exact same grading criteria across **offline evaluations** and **online monitors** without redefining rules per run.

![Metric Registry feeds both offline evaluations and online monitors](diagrams/metric-registry-reuse.png)

> **Best practice for variants:** Use **More options > Duplicate** and edit the copy rather than modifying an existing metric that other evaluations or monitors depend on.

> **Takeaway:** Define metrics once in the **Metric Registry** (`Evaluation > Metrics`) so offline experiments and online monitors share identical grading standards.

---

## 2. Predefined Metrics: Single-Turn vs. Multi-Turn `[4.1]`

**What it is:** Google-managed, out-of-the-box metrics available immediately without authoring rubrics. They are divided by **scope**—matching scope to behavior is critical because multi-turn failures (such as compounding wrong assumptions) slip past single-turn metrics.

| Scope | Predefined Metric | What It Grades | Added by Default in New Experiment? |
| :--- | :--- | :--- | :--- |
| **Single-turn** | `Agent Final Response Quality` | Quality of one final response | Yes |
| **Single-turn** | `Agent Hallucination` | Whether one response is grounded | Yes |
| **Single-turn** | `Agent Tool Use Quality` | Whether a tool call in one turn was correct | Yes |
| **Single-turn** | `Safety` | Whether one response is safe | Yes |
| **Multi-turn** | `Agent Multi-turn Task Success` | Whether the conversation achieved its overall goal | No (add explicitly) |
| **Multi-turn** | `Agent Multi-turn Tool Use Quality` | Tool use across the entire conversation | No (add explicitly) |
| **Multi-turn** | `Agent Multi-turn Trajectory Quality` | The path the agent took across turns | No (add explicitly) |

> **Takeaway:** The four single-turn predefined metrics are added by default in a New Experiment, but you must explicitly add **multi-turn** metrics to catch failures that emerge across turns.

---

## 3. Adaptive vs. Static Rubric Metrics `[4.1]`

How a metric reports results in the evaluation report depends on whether its rubrics are adaptive or static:

| Type | How It Grades | Output Format | Example Metrics |
| :--- | :--- | :--- | :--- |
| **Adaptive rubric metric** | Dynamically generates a unique set of pass/fail rubrics per eval case | Passing rate + per-rubric **Pass/Fail** verdicts + natural-language **rationale** | `Agent Multi-turn Task Success` |
| **Static rubric metric** | Applies one fixed scoring scheme across every eval case | Single **0-to-1** numeric score + natural-language **explanation** | `Agent Hallucination`, `Safety` |

> **Takeaway:** **Adaptive rubric metrics** generate case-specific pass/fail unit tests with rationales, while **static rubric metrics** apply one fixed 0-to-1 scale across all cases.

---

## 4. Custom Metrics: Judge-Based vs. Code-Based `[4.1]`

**What it is:** When predefined metrics do not cover a domain-specific requirement (such as support reply tone or strict JSON schema compliance), you author a custom metric via the **New metric** dialog on the **Metrics** tab.

| Dimension | Custom LLM Metric *(Judge-based)* | Custom Code Metric *(Code-based)* |
| :--- | :--- | :--- |
| **Grades with** | An LLM judge model evaluating against a rubric | A deterministic Python check |
| **Best for** | Subjective criteria and judgment calls | Objective, repeatable, definite pass/fail rules |
| **Authoring in console** | **Sample** buttons seed a starter rubric (instruction, named criteria like `Clarity` or `Excitement`, and a rating-score ladder) to edit without coding | Deterministic Python check registered in the same dialog |
| **Example** | Scoring whether a support reply stays calm and professional under pressure | Confirming a tool returned a structured JSON result |

**Decision rule:** If a criterion has one verifiable right answer in code, use a **Custom code metric** (faster, cheaper, deterministic). Reserve **Custom LLM metrics** for criteria that genuinely require judgment.

**Worked Example (Google Cloud Support Agent):**
- You want to verify that the agent stays empathetic when a customer is frustrated *and* that `lookup_resource` returns a valid resource ID.
- *Cross-check:* Register a **Custom LLM metric** for support tone (judgment call) and a **Custom code metric** for the structured tool output (deterministic check). Both live in the same Metric Registry and can run together in one experiment.

> **Takeaway:** Use **Custom code metrics** for deterministic pass/fail checks and **Custom LLM metrics** only when grading requires subjective judgment.

---

## Common Confusions

| Term A | Term B | Key Difference |
| :--- | :--- | :--- |
| **Rubric** | **Metric** | A **rubric** is one pass/fail criterion; a **metric** aggregates one or more rubrics into a score. |
| **Adaptive rubric metric** | **Static rubric metric** | Adaptive generates per-case pass/fail rubrics + passing rate; static applies one fixed 0-to-1 scheme to every case. |
| **Custom LLM metric** | **Custom code metric** | LLM metric uses a judge model + rubric (subjective); code metric runs deterministic Python (objective). |
| **Editing a metric** | **Duplicating a metric** | Never mutate a shared metric directly; use **More options > Duplicate** to create variants safely. |
