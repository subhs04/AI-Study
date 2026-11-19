# Agent Evaluation as Unit Tests: Nondeterminism and the Golden Dataset

**Module map:** Why classic unit tests fail for agents, how a **golden dataset** replaces exact-match assertions, and the two lenses CI uses to grade an agent (trajectory/tool use and final response). Covers **4.1** (test sets, golden data, evaluation against a golden dataset), **4.2** (regression prevention). Prerequisites: `01-agentops-lifecycle.md` (test stage, golden dataset introduced), `03-govern-and-optimize-platform.md` (Agent Evaluation service). Next: `05-cicd-pipeline-quality-gates.md`.

---

## 1. Why this exists: CI for code vs CI for agents [4.1][4.2]

**What/why:** In traditional software, **continuous integration (CI)** runs automatic unit and integration tests on every code change before merge. This catches bugs early. Agents need the same safety net, but two problems appear:

1. **Nondeterminism:** generative models are random, so reproducible results are hard to get.
2. **Open-ended outputs:** there are endlessly many valid inputs and phrasings, so comprehensive test cases are hard to write.

Terms defined:
- **Unit test:** a binary pass/fail check of one function, typically an exact assertion.
- **Regression:** a change that breaks behavior that previously worked.
- **Nondeterminism:** same input, different (possibly equally correct) outputs.

| | Traditional unit test | Agent evaluation |
|---|---|---|
| Assertion style | Exact match (`get_price("camping_tent") == 199.99`) | Score against a rubric |
| Result | Binary pass/fail | A **score** compared with a **threshold** |
| Handles wording variation? | No: "Yes, the tent is \$199.99" vs "Barely! The tent costs \$199.99" fails string match | Yes: both answers are judged correct |
| What is checked | Output value | Reasoning path **and** final response quality |

**Takeaway:** Replace rigid unit tests with **evaluations as unit tests**: automated, scored, threshold-based.

---

## 2. The golden dataset [4.1]

**What it is:** A curated dataset that mirrors the natural-language inputs of your app. Also called **ground truth**. It becomes the rubric the agent is continuously graded against.

Per the source (CymbalMart example), each record holds:

| Field | Meaning |
|---|---|
| **User prompt** | A real or historical user question (hundreds of them) |
| **Ideal response** | The human-written best answer |
| **Expected tool sequence** | The exact sequence of tools the agent *should* call |

```
 golden dataset: [prompt] + [ideal response] + [expected tool calls]
        │
        ▼ run agent on each prompt
 agent output: [actual response] + [actual tool calls]
        │
        ▼ compare to golden record
 score ──► threshold ──► pass / fail
```

**Stabilize early:** fix your evaluation approach, metrics and ground-truth data **as early as possible** in development. Reason: if you keep changing golden questions you cannot tell whether a score drop came from **bad code or a harder test**. Stable data = comparable experiments and test runs.

**Takeaway:** Golden dataset = prompts + ideal answers + expected tool sequence; freeze it early so scores stay comparable.

---

## 3. Two evaluation lenses [4.1]

CI evaluates the agent's **decision-making** through two lenses:

| Lens | Question it answers | Compared against (golden data) |
|---|---|---|
| **Trajectory and tool use** | Did the agent take the right steps and call the right tools in the right sequence? | The expected tool sequence |
| **Final response** | Was the answer itself good? | The ideal response |

Why both: an agent can give a plausible answer while skipping a required tool (wrong trajectory), or call the right tools and still phrase a poor answer (bad final response). Only checking one hides the other failure.

> **Gap:** The source capture lists the two lenses as headings only; the expanded detail (metric names, how each is scored) was **not captured**. Re-capture those two panels. The exam guide (4.1) also expects: ADK evalset, Agent Platform Gen AI evaluation service, custom autoraters, continuous evaluation pipelines. A later course ("Evaluate Agents") is the intended source.
>
> **Clarification (not in source):** Scoring free-text answers against an ideal response generally relies on an LLM acting as judge (autorater). Metric names and the exact tools are not in this capture. Verify in the official docs. Note: the lab lesson that teaches pointwise vs pairwise judging is parked for Phase 2 (see tracker); for the MCQ, read a theory source for those terms.

---

## 4. Worked example [4.1]

CymbalMart agent, prompt: "Can I afford the tent with a \$200 gift card?"

| Golden field (*illustrative values*) | Agent run today | Check |
|---|---|---|
| Expected tools: `get_price("camping_tent")` | Agent calls `get_price` ✔ | Trajectory lens passes |
| Ideal response: states price 199.99 and that it fits within \$200 | "Barely! The tent costs \$199.99" ✔ | Final response lens passes despite different wording |
| Exact-match unit test | Would **fail** the build | Shows why exact match is the wrong tool for agents ✔ |

Cross-check: if the agent answered correctly *without* calling `get_price` (guessing from memory), the final-response lens passes but the trajectory lens fails: that is exactly why both lenses are required.

---

## Common confusions

| Looks like | Difference |
|---|---|
| **Golden dataset vs ground truth** | Used interchangeably in this source for the curated rubric data |
| **Unit test vs evaluation** | Unit test = exact binary assertion on code; evaluation = scored comparison against golden data with a threshold |
| **Trajectory vs final response** | Trajectory = the steps/tool calls; final response = the answer text |
| **Evaluating a changed dataset** | Changing the golden set mid-project makes score changes uninterpretable |
| **Offline vs online evaluation** | This note and `05` are *offline* (golden dataset, pre-release); `06` covers *online* (live traffic) |

Sources: AgentOps course, "Agent evaluation" (`sources/ci-cd-and-quality-assurance-raw.md`).
