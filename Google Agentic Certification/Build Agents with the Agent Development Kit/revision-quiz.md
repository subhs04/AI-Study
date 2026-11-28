# Revision Quiz: Build Agents with the Agent Development Kit

**Questions:** 20 (across 5 notes)  
Tap **Show Answer** on GitHub Mobile to check your recall.

---

## 1. ADK Core Model and Local Setup ([note](notes/01-adk-core-model-and-setup.md))

**Q1 (Recall):** Which module-level variable name in `agent.py` do `adk run`, `adk web`, `adk api_server`, and cloud deployment targets use to discover your entry agent?

<details>
<summary>Show Answer</summary>

**Answer:** `root_agent`
**Why it matters:** Without exposing `root_agent` in `agent.py`, neither local CLI/UI tools nor Agent Runtime deployment can locate the entry point.
**Ref:** `[3.1]` · [01-adk-core-model-and-setup.md](notes/01-adk-core-model-and-setup.md)

</details>

**Q2 (Compare):** How do `instruction` and `description` differ on an ADK `Agent`?

<details>
<summary>Show Answer</summary>

**Answer:**
- `instruction` (required): System prompt guiding *this* agent's internal persona, scope, constraints, and tool-use rules.
- `description` (optional): Single-sentence scope summary read by a parent coordinator agent (or Gemini) to decide *when* to route tasks to this agent.

**Why it matters:** Putting routing summaries only in `instruction` leaves coordinators blind; `description` is what drives delegation.
**Ref:** `[3.1]` · [01-adk-core-model-and-setup.md](notes/01-adk-core-model-and-setup.md)

</details>

**Q3 (Compare):** What is the difference between `output_key` and `output_schema`, and what constraint applies to `output_schema`?

<details>
<summary>Show Answer</summary>

**Answer:**
- `output_key` writes the agent's final text response into session state under that key.
- `output_schema` enforces structured JSON output via a Pydantic/type schema, and is **incompatible with `tools` on some models**.

**Why it matters:** Combining `tools` and `output_schema` on an incompatible model breaks execution; use `output_key` to pass results forward in state.
**Ref:** `[3.1]` · [01-adk-core-model-and-setup.md](notes/01-adk-core-model-and-setup.md)

</details>

**Q4 (True / False):** `adk web` starts a programmatic HTTP streaming server for production clients, whereas `adk api_server` launches the browser dev UI at `http://localhost:8000`.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** It's the reverse: `adk web` launches the browser dev UI at `http://localhost:8000` (chat panel + execution trace), while `adk api_server` starts an HTTP server streaming responses using the same `Runner`.
**Why it matters:** Both wrap the same `Runner` engine, but `adk web` is for interactive local debugging and `adk api_server` is for programmatic HTTP clients.
**Ref:** `[3.1]` · [01-adk-core-model-and-setup.md](notes/01-adk-core-model-and-setup.md)

</details>

---

## 2. ADK Tools, Context, State, and Lifecycle Hooks ([note](notes/02-tools-context-state-and-hooks.md))

**Q5 (True / False):** You should document `tool_context: ToolContext` in a Python tool's docstring so the LLM knows how to pass session state and auth credentials.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Never document `tool_context: ToolContext` in the docstring—ADK injects it automatically at runtime and hides it from the model-facing JSON schema.
**Why it matters:** Hiding `ToolContext` from the LLM prevents parameter hallucination and prompt injection against server-controlled state (like `session_user_id`).
**Ref:** `[3.1]` `[5.2]` · [02-tools-context-state-and-hooks.md](notes/02-tools-context-state-and-hooks.md)

</details>

**Q6 (Recall):** What are the four ADK session state key scopes (by prefix), and what are the three safe channels for writing state?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Prefixes:** *(none)* = session-scoped; `user:` = user-scoped across sessions; `app:` = app-scoped across all users; `temp:` = invocation-scoped (cleared when invocation ends).
- **Safe write channels:** `output_key` on an `Agent`, `tool_context.state`, or `callback_context.state`.

**Why it matters:** Never mutate `session.state` directly outside managed contexts—persistence services (`InMemorySessionService`, `DatabaseSessionService`, `VertexAiSessionService`) track deltas only through these channels.
**Ref:** `[3.1]` · [02-tools-context-state-and-hooks.md](notes/02-tools-context-state-and-hooks.md)

</details>

**Q7 (Trigger -> Choice):** You need to block any prompt containing `"RESTRICTED"` before the LLM is called and immediately return a canned message without invoking the model. Which callback and return behavior should you use?

<details>
<summary>Show Answer</summary>

**Answer:** Attach a `before_model_callback` on the `Agent` and return a non-`None` `LlmResponse` object when `"RESTRICTED"` is detected (return `None` otherwise).
**Why it matters:** Returning `None` lets execution continue; returning a non-`None` response object **short-circuits** and skips the upcoming LLM or tool call.
**Ref:** `[3.1]` `[5.2]` · [02-tools-context-state-and-hooks.md](notes/02-tools-context-state-and-hooks.md)

</details>

**Q8 (Compare):** Runner `BasePlugin` vs. `Agent` callbacks — which runs first, and when should you use each?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Execution order:** Runner `BasePlugin` hooks always run **before** the corresponding `Agent` callback.
- **When to use:** Use `BasePlugin` on the `Runner` for cross-cutting observability/policy across all invocations (plus user-message, run-start, and error hooks); use `Agent` callbacks (`before/after_agent`, `before/after_model`, `before/after_tool`) for agent-specific guardrails.

**Why it matters:** Registering a `BasePlugin` once on `Runner` avoids duplicating logging or policy hooks on every sub-agent.
**Ref:** `[3.1]` `[5.2]` · [02-tools-context-state-and-hooks.md](notes/02-tools-context-state-and-hooks.md)

</details>

---

## 3. Multi-Agent Orchestration: Template Workflows and ADK 2.0 ([note](notes/03-multi-agent-orchestration-and-adk2.md))

**Q9 (Trigger -> Choice):** You have a deterministic workflow where sub-agents must repeat iteratively until a condition is met, without spending LLM tokens on routing. Which workflow agent should you use, and what parameter must you set defensively?

<details>
<summary>Show Answer</summary>

**Answer:** `LoopAgent`, with `max_iterations` always set defensively.
**Why it matters:** Omitting `max_iterations` risks infinite loops and runaway token/compute cost if the exit condition is never met.
**Ref:** `[3.3]` · [03-multi-agent-orchestration-and-adk2.md](notes/03-multi-agent-orchestration-and-adk2.md)

</details>

**Q10 (Compare):** `SequentialAgent` vs. ADK 2.0 `Workflow` — when do you upgrade from `SequentialAgent` to `Workflow`?

<details>
<summary>Show Answer</summary>

**Answer:**
- `SequentialAgent`: Fixed linear pipeline running `sub_agents` one after another.
- ADK 2.0 `Workflow`: Explicit directed graph (starting at `"START"`) needed for conditional branching via `Event(route=...)`, mixed Python/agent/tool/nested-`Workflow` nodes, or parallel fan-out/fan-in via `JoinNode`.

**Why it matters:** Choose the simplest deterministic wrapper that fits—`SequentialAgent` for linear chains, `Workflow` for branching or fan-out/fan-in graphs.
**Ref:** `[3.3]` · [03-multi-agent-orchestration-and-adk2.md](notes/03-multi-agent-orchestration-and-adk2.md)

</details>

**Q11 (Compare):** How do `mode="task"` and `mode="single_turn"` differ on coordinator `sub_agents`?

<details>
<summary>Show Answer</summary>

**Answer:**
- `mode="task"`: Scoped subtask where the specialist may interact with the user before returning control; runs **sequentially**.
- `mode="single_turn"`: One-shot delegation returning a structured result directly to the coordinator without user interaction; runs in **parallel** when multiple are called in one turn.

**Why it matters:** Both provide bidirectional context isolation and audit-trail branching, but only `single_turn` parallelizes fan-out lookups in a single turn.
**Ref:** `[3.3]` · [03-multi-agent-orchestration-and-adk2.md](notes/03-multi-agent-orchestration-and-adk2.md)

</details>

**Q12 (True / False):** A sub-agent configured with `mode="task"` or `mode="single_turn"` can define its own nested `sub_agents` as long as it uses `JoinNode`.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Both `task` and `single_turn` sub-agents must be **leaf nodes** and cannot define their own `sub_agents`.
**Why it matters:** Nesting sub-agents under `task` or `single_turn` violates ADK's delegation architecture; use `Workflow` or default `chat` mode if deeper hierarchies are required.
**Ref:** `[3.3]` · [03-multi-agent-orchestration-and-adk2.md](notes/03-multi-agent-orchestration-and-adk2.md)

</details>

---

## 4. Grounding, Enterprise Data Integration, and MCP ([note](notes/04-grounding-enterprise-data-and-mcp.md))

**Q13 (True / False):** `VertexAiSearchTool` works with both Google AI Studio API keys and Vertex AI.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** `VertexAiSearchTool` queries an Agent Search datastore and requires `GOOGLE_GENAI_USE_VERTEXAI=TRUE`—Google AI Studio is not supported.
**Why it matters:** Using an AI Studio key with `VertexAiSearchTool` fails; enterprise datastore grounding requires Vertex AI.
**Ref:** `[3.2]` `[1.2]` · [04-grounding-enterprise-data-and-mcp.md](notes/04-grounding-enterprise-data-and-mcp.md)

</details>

**Q14 (Compare):** **Schema hallucination** vs. **Semantic drift** — which data type does each affect, and how do you mitigate each?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Schema hallucination** (structured data like SQL/CRM): Inventing table or column names. Mitigate with custom Python tools + `ToolContext` auth checks, or two-step retrieval injecting approved schemas before SQL generation.
- **Semantic drift** (unstructured data like PDFs/wikis): Retrieving topically similar but wrong chunks. Mitigate with `VertexAiSearchTool` or hybrid **Vector Search 2.0** (`vectorsearch` API) with metadata filtering/custom ranking.

**Why it matters:** Pairing dedicated tools per knowledge base with the right retrieval guardrail prevents both fabricated queries and off-target citations.
**Ref:** `[3.2]` · [04-grounding-enterprise-data-and-mcp.md](notes/04-grounding-enterprise-data-and-mcp.md)

</details>

**Q15 (Compare):** When do you use `StdioConnectionParams` vs. `StreamableHTTPConnectionParams` with `McpToolset`?

<details>
<summary>Show Answer</summary>

**Answer:**
- `StdioConnectionParams` (`StdioServerParameters`): Local processes or sidecar containers communicating over stdin/stdout.
- `StreamableHTTPConnectionParams`: Remote production HTTP MCP servers.

**Why it matters:** Local stdio MCP servers don't span separate network hosts—remote production MCP integrations require `StreamableHTTPConnectionParams`.
**Ref:** `[3.2]` · [04-grounding-enterprise-data-and-mcp.md](notes/04-grounding-enterprise-data-and-mcp.md)

</details>

**Q16 (Trigger -> Choice):** Your `McpToolset` agent works locally in `adk web` using an async factory function, but fails when deployed to Cloud Run / Agent Runtime. What is the fix, and which parameter enforces least privilege?

<details>
<summary>Show Answer</summary>

**Answer:** Define the agent and `McpToolset` **synchronously in `agent.py`** (async factories fail on Cloud Run, GKE, and Agent Runtime), and pass `tool_filter=[...]` to restrict exposed MCP tools.
**Why it matters:** Sync module-level `McpToolset` avoids cloud runtime startup failures, and `tool_filter` prevents context bloat and over-privileged tool exposure.
**Ref:** `[3.2]` · [04-grounding-enterprise-data-and-mcp.md](notes/04-grounding-enterprise-data-and-mcp.md)

</details>

---

## 5. Deploying and Sharing ADK Agents in Gemini ([note](notes/05-deploying-and-sharing-in-gemini.md))

**Q17 (Trigger -> Choice):** Which deployment path should you choose when you need **CI/CD pipelines** and **Terraform (IaC)** scaffolded automatically, vs. fine-grained scaling (`min_instances`, `max_instances`, `resource_limits`)?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Agents CLI**: Scaffolds a production-ready environment with CI/CD pipelines and Terraform IaC automatically.
- **Gemini Platform SDK** (`client.agent_engines.create()`): Provides custom deployment sources and runtime tuning (`min_instances`, `max_instances`, `resource_limits`).
- *(Use `adk deploy agent_engine` for standard direct deploys.)*

**Why it matters:** Both CLIs wrap `client.agent_engines.create()`, so choose Agents CLI for IaC/CI-CD scaffolding and the SDK for explicit container/scaling control.
**Ref:** `[4.2]` `[3.1]` · [05-deploying-and-sharing-in-gemini.md](notes/05-deploying-and-sharing-in-gemini.md)

</details>

**Q18 (True / False):** Deploying a multi-agent ADK project to Agent Runtime creates a separate `reasoningEngine` endpoint for each sub-agent and automatically exposes `root_agent` in the Gemini web app.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Two errors: (1) deploying `root_agent` packages the coordinator and all sub-agents behind **one** `reasoningEngine` resource; (2) deploying to Agent Runtime does **not** expose the agent in Gemini (or Agent Registry)—registration in Gemini is an explicit separate step.
**Why it matters:** Keep the three lifecycle steps distinct: Build (ADK) -> Scale (Agent Runtime `reasoningEngine`) -> Register (Gemini for users / Agent Registry for governance).
**Ref:** `[4.2]` · [05-deploying-and-sharing-in-gemini.md](notes/05-deploying-and-sharing-in-gemini.md)

</details>

**Q19 (Recall):** What is the regional compatibility rule when registering an Agent Runtime `reasoningEngine` in a Gemini app?

<details>
<summary>Show Answer</summary>

**Answer:** A **`us`** Gemini app requires an agent deployed in a **`us-*`** region; a **`global`** Gemini app accepts **any** region.
**Why it matters:** Deploying a `reasoningEngine` outside `us-*` blocks registration into a `us` Gemini app.
**Ref:** `[4.2]` · [05-deploying-and-sharing-in-gemini.md](notes/05-deploying-and-sharing-in-gemini.md)

</details>

**Q20 (Trigger -> Choice):** Your `reasoningEngine` lives in Project A, while the Gemini app lives in Project B. Which IAM role must you grant, to whom, and in which project?

<details>
<summary>Show Answer</summary>

**Answer:** Grant the **Discovery Engine Service Agent** IAM role to the **Gemini service agent** in the agent's hosting project (**Project A**).
**Why it matters:** Cross-project Gemini invocation fails unless the Gemini service agent has Discovery Engine Service Agent permissions on the project hosting the `reasoningEngine`.
**Ref:** `[5.1]` · [05-deploying-and-sharing-in-gemini.md](notes/05-deploying-and-sharing-in-gemini.md)

</details>
