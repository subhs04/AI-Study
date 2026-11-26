# 05 — Agent Runtime Deployment & ADK Evaluation

**Module map:** Covers deploying ADK (and third-party) agents to **Agent Runtime** (managed scaling, security, session/memory, Example Store, 3-step deploy flow) and **ADK Evaluation** (Final Response vs. Trajectory & Tool Use, `.test.json` vs. `.evalset.json`, Web UI / CLI / `pytest` `AgentEvaluator.evaluate`, and Cloud Trace).
**Exam objectives:** `4.2` (Deploying and scaling production workloads), `4.1` (Evaluating agents in development and in production).
**Prerequisites:** [04 — ADK Callbacks & Lifecycle Hooks](04-adk-callbacks-and-lifecycle-hooks.md). **Next:** None (end of module).

---

## 1. Deploying to Agent Runtime `[4.2]`

**What it is:** **Agent Runtime** (formerly *Agent Engine*) is a fully managed Google Cloud service on **Agent Platform** (*Vertex AI*) for deploying, querying, managing, and autoscaling Gen AI agents in production.

**Why it exists:** Moving an agent from prototype to production introduces operational friction—request timeouts, model selection, VPC Service Controls, authentication, CI/CD, session/memory persistence, and analytics. Agent Runtime eliminates this infrastructure boilerplate.

### Core Capabilities of Agent Runtime
* **Scalable Deployment & Security:** Managed autoscaling, API to manage and query agents, and built-in security including **VPC Service Controls (VPC-SC)** and **authentication**.
* **Comprehensive Agent Management:** Managed **session and memory** services, a UI to list/view/converse with agents, plus monitoring and analytics.
* **Quality & Example Store:** Built-in evaluation integration and an **Example Store** to add and manage examples that improve agent performance.
* **Framework-Agnostic:** Supports both first-party frameworks (**ADK**) and third-party open-source frameworks (**LangGraph**, **LangChain**, **CrewAI**).

### 3-Step Deployment Flow
```
1. Define & Test Locally          2. Deploy to Remote App          3. Query
┌─────────────────────────┐      ┌─────────────────────────┐      ┌──────────────────┐
│ Define model, tools,    │─────▶│ Package & deploy agent  │─────▶│ Query the agent  │
│ & agent; test locally   │      │ to Agent Runtime app    │      │ via remote app   │
└─────────────────────────┘      └─────────────────────────┘      └──────────────────┘
```

**Takeaway:** **Agent Runtime** is a framework-agnostic, fully managed production runtime handling autoscaling, VPC-SC/auth, session/memory management, Example Store, and Cloud Trace.

---

## 2. ADK Evaluation Dimensions & Metrics `[4.1]`

**What it is:** An automated framework built into ADK to assess both **what** an agent answers and **how** it reasoned and called tools to get there.

**Why it exists:** Evaluating standard generative models only checks final text output. Agents take multi-step actions (disambiguating intent, looking up policies, calling APIs), so production readiness requires verifying both the **Final Response** and the **Trajectory**.

### Dimension 1: Final Response (`response_match_score`)
* **What it measures:** Quality, relevance, and correctness of the agent's final natural-language answer compared against the `reference` field in the eval dataset.
* **Metric:** Uses **ROUGE** similarity.
* **Default threshold:** **`0.8`** (allows slight wording variation while requiring high semantic/lexical overlap).

### Dimension 2: Trajectory & Tool Use (`tool_trajectory_avg_score`)
* **What it measures:** Compares the agent's actual sequence of steps (determine intent → use tool → review results → report generation) against `expected_tool_use`.
* **Scoring:** Each matching tool usage step scores **`1`**, mismatch scores **`0`**; the final score is the average across steps.
* **Default threshold:** **`1.0`** (requires a 100% match by default).

| Trajectory Match Type | How It Evaluates | When to Use |
| :--- | :--- | :--- |
| **Exact match** | Requires a **perfect match** to the ideal trajectory (no extra/missing steps, exact order) | High-stakes, strict compliance or deterministic workflows |
| **In-order match** | Requires the expected actions in the **correct order**, **allows extra actions** | Workflows where order matters but extra intermediate lookups are acceptable |
| **Any-order match** | Requires the expected actions in **any order**, **allows extra actions** | Flexible tasks where independent tool calls can happen in any sequence |
| **Precision** | Measures relevance/correctness of predicted actions | Checking that the agent isn't making unnecessary/wrong tool calls |
| **Recall** | Measures how many essential expected actions were captured | Checking that critical steps weren't skipped |
| **Single-tool use** | Checks for the inclusion of one specific action | Verifying a mandatory tool (e.g., audit/logging call) was invoked |

**Takeaway:** ADK grades **`response_match_score`** (ROUGE, default `0.8`) for final answers and **`tool_trajectory_avg_score`** (default `1.0`) for tool steps, with six trajectory match modes from strict `Exact` to flexible `Any-order`.

---

## 3. Test Files vs. Evalsets & Execution Methods `[4.1]`, `[4.2]`

| Feature | Test File (`*.test.json`) | Evalset (`*.evalset.json`) |
| :--- | :--- | :--- |
| **Structure** | **Single session** (may have multiple turns); any prefix allowed as long as it ends in `.test.json` | **Multiple `evals`** (each a distinct named session with `initial_session_state` and 1+ turns) |
| **Session contents** | Queries, `expected_tool_use`, `reference` responses | User query, `expected_tool_use`, `expected_intermediate_responses`, `reference` response |
| **Testing role** | **Unit testing** during active development (fast, simple complexity) | **Integration testing** for complex, lengthy multi-turn conversations (run less frequently) |

### Three Ways to Run ADK Evaluations
1. **Web UI (`Eval` tab):** Interactive evaluation; also lets you **capture live or past saved sessions** and convert them into eval cases inside an evalset (avoiding manual JSON authoring), then re-run against new agent versions.
2. **CLI:** Run evaluations directly against an existing `.evalset.json` file from the terminal.
3. **Programmatic (`pytest`):** Call `AgentEvaluator.evaluate(...)` in `pytest` to integrate `.test.json` checks (including custom initial session state files) into **CI/CD pipelines**.

### Enabling Cloud Trace
* Add `AF_TRACE_TO_CLOUD=1` to your `.env` file (transcript states *"AF Trace to Cloud equals 1"*) so **Cloud Trace** captures latency across LLM and tool calls when deployed on Agent Runtime.
* **Clarification (not in source):** Verify the exact environment variable name in current ADK docs (recent ADK versions use CLI flags like `--trace_to_cloud` or `OTEL` configuration depending on SDK version).

### Worked Example: Development-to-CI Evaluation Workflow
*Scenario (illustrative):* You build a multi-agent support bot and want to prevent regressions before deploying to Agent Runtime.
1. In **Web UI**, you test a multi-turn refund conversation, open the **Eval** tab, and save the session to a `.test.json` file.
2. Because the agent may optionally call a policy lookup before issuing a refund, you configure `tool_trajectory_avg_score` with **In-order match** (instead of Exact match) and keep `response_match_score` at `0.8`.
3. You wire `AgentEvaluator.evaluate()` into **`pytest`** in CI/CD for fast unit checks on every commit, and run a broader `.evalset.json` suite before promoting to **Agent Runtime** with Cloud Trace enabled.
*Cross-check:* `.test.json` handles the fast single-session CI unit check; `.evalset.json` handles multi-session integration testing; In-order match prevents false failures from benign extra lookups.

**Takeaway:** Author evals from live sessions in **Web UI**, run fast single-session **`.test.json`** unit tests in **`pytest`/CI**, and run multi-session **`.evalset.json`** integration suites before deploying to **Agent Runtime**.

---

## Common Confusions

| Term A | Term B | Key Distinction |
| :--- | :--- | :--- |
| **`*.test.json`** | **`*.evalset.json`** | **`.test.json`** holds a *single* session for fast unit testing (`pytest`); **`.evalset.json`** holds *multiple* named sessions (with initial state and intermediate responses) for integration testing. |
| **`response_match_score`** | **`tool_trajectory_avg_score`** | **`response_match_score`** grades the *final natural-language answer* via ROUGE (default `0.8`); **`tool_trajectory_avg_score`** grades the *sequence of tool calls* (default `1.0`). |
| **In-order match** | **Any-order match** | Both allow extra actions, but **In-order** requires expected actions in sequence while **Any-order** ignores sequence. |

*Sources: `Deploy Multi-Agent Systems with Gemini Enterprise Agent Platform/Sources/Raw.md` (Lessons 6–7).*
