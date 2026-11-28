# Next-Steps Roadmap (Phase 1 MCQ -> Phase 2 Labs)

Action plan from current state (10 modules with notes + `revision-quiz.md` committed; `content.json` 234-question scenario bank available) through passing the **Professional Agentic Architect** exam.

---

## Stage 1: Close Remaining Must-Do Content Gaps

1. **Process the 2 pending Must-Do courses** (sources already captured):
   - [ ] **`Build Agent Skills`** (`2.2`, `3.1`) — Critical: **Section 2 (Coding agents, ~17%)** currently has 0% note coverage in `00-coverage-tracker.md`.
   - [ ] **`Optimize Agent Behavior`** (`1.1`) — Closes low-code prompt-template coverage.
   - [ ] Generate `revision-quiz.md` for both modules after notes are written.

2. **Plug the "parked-lab / missing-detail" MCQ gaps** using theory/official docs (do **not** open parked labs in Phase 1):
   - [ ] **Section 2 (`2.1`, `2.2`):** Antigravity workflows, sandboxes (GKE Sandbox, Cloud Workstations), and Agents CLI.
   - [ ] **Section 3 (`3.1`):** Memory Bank vs. Sessions internals, LLM vs. SLM, OSS vs. proprietary selection criteria *(optionally skim only the Memory Bank section of `Craft ADK Agents with Persistent Memories`)*.
   - [ ] **Section 4 (`4.1`):** Pointwise vs. pairwise judging and Gen AI Evaluation Service internals.
   - [ ] **Section 5 (`5.1`, `5.2`):** Agent Identity / Auth Manager, Principal Access Boundary (PAB) policies, full OAuth 2.0 flow, mTLS personas, SCC Agent Threat Detection, HITL, and VPC-SC configuration details.

3. **Skip "Good to Have" courses end-to-end:**
   - Skip `Deploy Gemini to Transform Enterprises`, `Hill Climb Generative and Agentic Systems`, and `Build AI Agents with Enterprise Databases` unless all Must-Do drills and gaps are already at 100%.

---

## Stage 2: Active-Recall Retention Drills (GitHub Mobile)

Drill the module `revision-quiz.md` files (228 questions currently) in **exam-weight order** until names, prefixes, limits, and "X vs. Y" distinctions are automatic:

- [ ] **S3 — Developing Custom Agents (~33%)**
  - `Deploy Multi-Agent Systems with Gemini Enterprise Agent Platform/revision-quiz.md` (20 Qs)
  - `Build Agents with the Agent Development Kit/revision-quiz.md` (20 Qs)
  - `Build Enterprise Agents with Code Execution on Gemini platform/revision-quiz.md` (12 Qs)
- [ ] **S4 — Evaluating & Deploying (~22%)**
  - `Evaluate Agents on Gemini platform/revision-quiz.md` (32 Qs)
  - `AgentOps- Operationalize AI Agents on Google Cloud/revision-quiz.md` (44 Qs)
- [ ] **S5 — Securing & Governing (~15%)**
  - `Govern agents with Agent Gateway, Agent Registry, and Policies/revision-quiz.md` (16 Qs)
  - `Model Armor/revision-quiz.md` (15 Qs)
- [ ] **S2 — Coding Agents (~17%)**
  - `Build Agent Skills/revision-quiz.md` (once Stage 1 is processed)
- [ ] **S1 / Foundational (~13%)**
  - `Understand Google Cloud Agents/revision-quiz.md` (28 Qs)
  - `Improve Agent Search/revision-quiz.md` (12 Qs)
  - `Optimize Agent Behavior/revision-quiz.md` (once Stage 1 is processed)
  - `Agent Strategy Discover Design Prototype/revision-quiz.md` (29 Qs)

---

## Stage 3: Scenario-Based Practice (`content.json`)

- [ ] **Shuffle answer options** before drilling `content.json` (the raw file has a ~68% `B`-answer bias across its 228 single-select questions).
- [ ] Drill all **234 scenario questions** by domain priority:
  1. Domain 2 — Coding agents (41 Qs)
  2. Domain 3 — Developing custom agents (73 Qs)
  3. Domain 4 — Evaluating and deploying agents (53 Qs)
  4. Domain 5 — Security and governance (36 Qs)
  5. Domain 1 — Low-code tools (31 Qs)
- [ ] Cross-check any missed or ambiguous questions against your module notes and the cited official doc URLs.

---

## Stage 4: Final Mock & Phase 1 MCQ Exam

- [ ] Complete 1–2 timed, mixed-domain passes (50–60 shuffled questions) targeting **80%+** with clear reasoning for why distractors are wrong.
- [ ] Sit and pass the **Phase 1 proctored MCQ exam**.

---

## Stage 5: Phase 2 (Hands-On Labs — After Passing MCQ)

- [ ] Unpark the 11 labs and challenge labs tracked in `00-coverage-tracker.md` and complete hands-on lab practice on Google Skills.
