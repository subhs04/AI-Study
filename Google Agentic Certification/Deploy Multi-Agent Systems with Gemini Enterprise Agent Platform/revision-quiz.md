# Revision Quiz: Deploy Multi-Agent Systems with Gemini Enterprise Agent Platform

**Questions:** 20 (5 notes) · Tap **Show Answer** on GitHub Mobile to check your recall.

---

## 1. ADK Architecture & Google Cloud Agent Paths ([note](notes/01-adk-architecture-and-agent-paths.md))

**Q1 (1-Line Trigger → Choice):** Which Google Cloud agent-building path should you choose for external-facing customer support that integrates with telephony and human support teams?

<details>
<summary>Show Answer</summary>

**Answer:** **Customer Engagement Suite (Conversational Agents)**.
**Why it matters:** Conversational Agents targets *external* customer support with telephony/human handoff, whereas a **Gemini Enterprise app** targets *internal* enterprise search across Drive, Chat, Mail, and ticketing systems.
**Ref:** `[3.1]` · [01-adk-architecture-and-agent-paths.md](notes/01-adk-architecture-and-agent-paths.md)

</details>

**Q2 (Compare X vs. Y):** How do **Session & State**, **Memory**, and **Artifact Management** differ in ADK?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Session & State:** Short-term working memory and event history for a *single* conversation.
- **Memory:** Long-term user context recalled *across multiple sessions*.
- **Artifact Management:** Stores *versioned* files or binary data (images, documents, reports) tied to a session or user.

**Why it matters:** Don't put cross-session user preferences in Session State or binary files in key-value state—match the storage primitive to scope and data type.
**Ref:** `[3.1]` · [01-adk-architecture-and-agent-paths.md](notes/01-adk-architecture-and-agent-paths.md)

</details>

**Q3 (Recall / Cloze):** Every ADK agent relies on four main components: **Models** (reasoning), **Tools** (action), **________** (control flow & context), and **________** (execution).

<details>
<summary>Show Answer</summary>

**Answer:** **Orchestration** and **Runtime**.
**Why it matters:** ADK cleanly separates reasoning (Models), external actions (Tools), routing/state (Orchestration), and execution environment (local or Agent Runtime).
**Ref:** `[3.1]` · [01-adk-architecture-and-agent-paths.md](notes/01-adk-architecture-and-agent-paths.md)

</details>

**Q4 (True / False — Spot the Trap):** *True or False:* ADK only works with Gemini models and custom Python functions written specifically for ADK.

<details>
<summary>Show Answer</summary>

**Answer:** **False.**
**Why it matters:** ADK integrates third-party/OSS models (e.g., Claude or custom models via its base LLM interface) and reuses tools from frameworks like **LangChain** and **CrewAI**.
**Ref:** `[3.1]` · [01-adk-architecture-and-agent-paths.md](notes/01-adk-architecture-and-agent-paths.md)

</details>

---

## 2. ADK Setup & Execution Modes ([note](notes/02-adk-setup-and-execution-modes.md))

**Q5 (Recall / Cloze):** What three files must exist in an ADK agent folder for the Web UI, CLI, and API Server, and what variable name must `agent.py` expose?

<details>
<summary>Show Answer</summary>

**Answer:** `__init__.py`, `agent.py`, and `.env`; `agent.py` must assign its entry-point agent to **`root_agent`**.
**Why it matters:** ADK's local tooling and API Server discover agents via this exact folder contract and `.env` auth routing (Gemini API key vs. Vertex AI project/location).
**Ref:** `[3.1]` · [02-adk-setup-and-execution-modes.md](notes/02-adk-setup-and-execution-modes.md)

</details>

**Q6 (Compare X vs. Y):** What is the difference between an agent's **`description`** and its **`instruction`** in `agent.py`?

<details>
<summary>Show Answer</summary>

**Answer:**
- **`description`:** Tells a *parent or peer agent* what this agent does so it can decide whether to transfer control to it.
- **`instruction`:** The *system prompt* governing how the agent itself behaves and acts.

**Why it matters:** A vague `description` breaks multi-agent routing even when the agent's internal `instruction` is well-written.
**Ref:** `[3.1]` · [02-adk-setup-and-execution-modes.md](notes/02-adk-setup-and-execution-modes.md)

</details>

**Q7 (1-Line Trigger → Choice):** You need to run an ADK agent inside a single Jupyter/Colab notebook cell without creating the `__init__.py` / `agent.py` / `.env` folder layout. Which execution mode should you use?

<details>
<summary>Show Answer</summary>

**Answer:** **Programmatic Interface** (wiring `Runner`, `SessionService`, `ArtifactService`, and `Content`/`Role` directly in Python).
**Why it matters:** **Web UI**, **CLI**, and **API Server** (Flask REST on default port `8000`) require the folder layout; only the **Programmatic Interface** works in a single file or notebook cell.
**Ref:** `[3.1]` · [02-adk-setup-and-execution-modes.md](notes/02-adk-setup-and-execution-modes.md)

</details>

**Q8 (True / False — Spot the Trap):** *True or False:* `InMemorySessionService` and `InMemoryArtifactService` persist conversation state and generated files across Python process restarts.

<details>
<summary>Show Answer</summary>

**Answer:** **False.**
**Why it matters:** Both are **ephemeral** in-memory services—all conversation history, state variables, and artifacts are lost when the Python process or notebook kernel exits.
**Ref:** `[3.1]` · [02-adk-setup-and-execution-modes.md](notes/02-adk-setup-and-execution-modes.md)

</details>

---

## 3. Multi-Agent Tree & Workflow Agents ([note](notes/03-multi-agent-tree-and-workflows.md))

**Q9 (Recall / Cloze):** In ADK's multi-agent tree, an agent can only transfer conversation control to which three relative positions?

<details>
<summary>Show Answer</summary>

**Answer:** Its **sub-agents** (down), its **parent** (up), and its **peers** sharing the **same parent** (sideways—which can be disabled per agent).
**Why it matters:** Restricting transfers to the tree prevents an agent in one branch from accidentally invoking a similarly named specialist in another branch.
**Ref:** `[3.3]` · [03-multi-agent-tree-and-workflows.md](notes/03-multi-agent-tree-and-workflows.md)

</details>

**Q10 (Compare X vs. Y):** How do **LLM-based agents** differ from **Workflow agents** (`SequentialAgent`, `LoopAgent`, `ParallelAgent`)?

<details>
<summary>Show Answer</summary>

**Answer:**
- **LLM-based Agent:** Powered by an LLM; reasons dynamically and typically alternates turns with a user.
- **Workflow Agent:** Has **no LLM**; acts as a deterministic director orchestrating sub-agents without waiting on intermediate user turns.

**Why it matters:** Use workflow agents whenever business logic requires guaranteed execution order or agent-to-agent collaboration without user intervention.
**Ref:** `[3.1], [3.3]` · [03-multi-agent-tree-and-workflows.md](notes/03-multi-agent-tree-and-workflows.md)

</details>

**Q11 (1-Line Trigger → Choice):** You need to generate independent Q3 financial summaries for North America, EMEA, and APAC simultaneously to minimize latency. Which workflow agent fits best?

<details>
<summary>Show Answer</summary>

**Answer:** **`ParallelAgent`**.
**Why it matters:** `ParallelAgent` runs independent sub-agents concurrently, whereas **`SequentialAgent`** runs strict step-by-step pipelines and **`LoopAgent`** repeats until a condition is met.
**Ref:** `[3.3]` · [03-multi-agent-tree-and-workflows.md](notes/03-multi-agent-tree-and-workflows.md)

</details>

**Q12 (True / False — Spot the Trap):** *True or False:* `ParallelAgent` is designed for concurrent sub-agents that need to continuously exchange shared state with one another while running.

<details>
<summary>Show Answer</summary>

**Answer:** **False.**
**Why it matters:** `ParallelAgent` assumes **no inherent need for shared state or direct information exchange** between its concurrently executing sub-agents.
**Ref:** `[3.3]` · [03-multi-agent-tree-and-workflows.md](notes/03-multi-agent-tree-and-workflows.md)

</details>

---

## 4. ADK Callbacks & Lifecycle Hooks ([note](notes/04-adk-callbacks-and-lifecycle-hooks.md))

**Q13 (Recall / Cloze):** What return type short-circuits execution in **`before_model`** vs. **`before_tool`**?

<details>
<summary>Show Answer</summary>

**Answer:** Returning an **`LLM Response`** object in `before_model` skips the LLM call; returning a **`dict`** in `before_tool` skips `tool.run_async()` and serves as the tool result.
**Why it matters:** Returning the wrong type won't short-circuit as intended—know both exact return contracts for deterministic caching and guardrails.
**Ref:** `[3.1]` · [04-adk-callbacks-and-lifecycle-hooks.md](notes/04-adk-callbacks-and-lifecycle-hooks.md)

</details>

**Q14 (Compare X vs. Y):** Which ADK agent classes support **Agent lifecycle callbacks** (`before_agent`, `after_agent`) vs. **Model/Tool callbacks** (`before_model`, `after_model`, `before_tool`, `after_tool`)?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Agent lifecycle callbacks:** Available on **all `BaseAgent` subclasses** (including `LLMAgent`, `SequentialAgent`, `ParallelAgent`, and `LoopAgent`).
- **Model & Tool callbacks:** Specific to **`LLMAgent`** only (workflow agents do not call LLMs or tools directly).

**Why it matters:** Don't attach `before_model` or `before_tool` to a `SequentialAgent` or `ParallelAgent`—only `LLMAgent` exposes those hooks.
**Ref:** `[3.1]` · [04-adk-callbacks-and-lifecycle-hooks.md](notes/04-adk-callbacks-and-lifecycle-hooks.md)

</details>

**Q15 (True / False — Spot the Trap):** *True or False:* If `before_agent` returns cached content to skip the agent's run, `after_agent` still runs immediately afterward for cleanup.

<details>
<summary>Show Answer</summary>

**Answer:** **False.**
**Why it matters:** **`after_agent` does not run** if the agent run was skipped by `before_agent` or if `end_invocation` was set during execution.
**Ref:** `[3.1]` · [04-adk-callbacks-and-lifecycle-hooks.md](notes/04-adk-callbacks-and-lifecycle-hooks.md)

</details>

**Q16 (1-Line Trigger → Choice):** You want to enforce a deterministic Python authorization check before a `delete_record` tool runs, blocking unauthorized callers without relying on LLM prompt compliance. Which hook do you use?

<details>
<summary>Show Answer</summary>

**Answer:** **`before_tool`** callback (returning an error `dict` such as `{"error": "Permission denied"}` to skip `tool.run_async()`).
**Why it matters:** `before_tool` intercepts after the LLM emits a function call but before execution, enabling hard authorization gates and tool-level caching.
**Ref:** `[3.1]` · [04-adk-callbacks-and-lifecycle-hooks.md](notes/04-adk-callbacks-and-lifecycle-hooks.md)

</details>

---

## 5. Agent Runtime Deployment & ADK Evaluation ([note](notes/05-agent-runtime-deployment-and-adk-eval.md))

**Q17 (Recall / Cloze):** Name ADK's two evaluation score metrics, what each measures, and their default pass thresholds.

<details>
<summary>Show Answer</summary>

**Answer:**
- **`response_match_score`:** Grades the final natural-language answer against `reference` via **ROUGE** (default threshold **`0.8`**).
- **`tool_trajectory_avg_score`:** Grades tool call steps against `expected_tool_use` (default threshold **`1.0`**).

**Why it matters:** Agents can produce a plausible final answer while calling the wrong tools—ADK evaluates both *what* was answered and *how* tools were used.
**Ref:** `[4.1]` · [05-agent-runtime-deployment-and-adk-eval.md](notes/05-agent-runtime-deployment-and-adk-eval.md)

</details>

**Q18 (Compare X vs. Y):** When should you use **`*.test.json`** vs. **`*.evalset.json`** in ADK Evaluation?

<details>
<summary>Show Answer</summary>

**Answer:**
- **`*.test.json`:** **Single session** for fast **unit testing** during active development and CI/CD (via `pytest` `AgentEvaluator.evaluate`).
- **`*.evalset.json`:** **Multiple named sessions** (with `initial_session_state` and `expected_intermediate_responses`) for broader **integration testing**.

**Why it matters:** Use `.test.json` for fast per-commit CI checks and `.evalset.json` for comprehensive multi-turn integration suites.
**Ref:** `[4.1]` · [05-agent-runtime-deployment-and-adk-eval.md](notes/05-agent-runtime-deployment-and-adk-eval.md)

</details>

**Q19 (1-Line Trigger → Choice):** Your agent must call expected tools in a specific sequence, but benign extra intermediate lookups should not fail the evaluation. Which trajectory match type should you choose?

<details>
<summary>Show Answer</summary>

**Answer:** **In-order match**.
**Why it matters:** **Exact match** forbids any extra steps, **Any-order match** ignores sequence, and **In-order match** enforces sequence while allowing extra actions.
**Ref:** `[4.1]` · [05-agent-runtime-deployment-and-adk-eval.md](notes/05-agent-runtime-deployment-and-adk-eval.md)

</details>

**Q20 (True / False — Spot the Trap):** *True or False:* Agent Runtime (formerly Agent Engine) only supports deploying agents built with Google's ADK.

<details>
<summary>Show Answer</summary>

**Answer:** **False.**
**Why it matters:** **Agent Runtime** is framework-agnostic—it hosts ADK agents as well as third-party frameworks like **LangGraph**, **LangChain**, and **CrewAI** with managed autoscaling, VPC-SC, Example Store, and Cloud Trace.
**Ref:** `[4.2]` · [05-agent-runtime-deployment-and-adk-eval.md](notes/05-agent-runtime-deployment-and-adk-eval.md)

</details>
