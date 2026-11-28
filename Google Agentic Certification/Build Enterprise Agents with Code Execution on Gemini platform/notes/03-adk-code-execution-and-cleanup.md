# Integrating Code Execution into ADK Workflows and Cleanup

**Module map:** Covers `[3.1]` (ADK code executors, `Runner`, `VertexAiMemoryBankService`), `[4.2]` (cost/resource cleanup), and `[5.2]` (production isolation).  
**Prerequisites:** [01-gemini-platform-and-agent-sandbox.md](01-gemini-platform-and-agent-sandbox.md), [02-sandbox-lifecycle-state-and-sdk.md](02-sandbox-lifecycle-state-and-sdk.md).

---

## Direct SDK vs. ADK Orchestration `[3.1]`

| Path | How It Works | Best When |
|---|---|---|
| **Direct SDK (`vertexai`)** | Your code decides when to call `execute_code`, parses `Chunk` outputs, and feeds results back to the model. | Custom orchestration loops or non-ADK agent frameworks. |
| **ADK Orchestration** | An `LlmAgent` with a `code_executor` autonomously decides when to write Python, executes it, captures outputs as context, and synthesizes the answer. | Standard Gemini/ADK agent workflows needing autonomous multi-turn analysis. |

**Takeaway:** Use ADK's `code_executor` when the agent should autonomously decide when to write and run Python; use the direct SDK when integrating into a custom/non-ADK loop.

---

## Choosing a Code Executor in ADK `[3.1]` `[5.2]`

| Dimension | `AgentEngineSandboxCodeExecutor` | `BuiltInCodeExecutor` |
|---|---|---|
| **Environment** | Managed, pre-provisioned **Agent Sandbox**. | Same process as the agent (**in-process**, no sandbox). |
| **Isolation & State** | **Isolated** and **stateful across turns**; includes 125+ pre-installed libraries. | **No isolation**; **does not persist state** across turns. |
| **Region Constraint** | Requires a sandbox provisioned in **`us-central1`** (other regions raise a runtime config error). | No region restrictions. |
| **Best For** | **Production enterprise deployments** running model-generated code on real data. | Local dev, rapid prototyping, or low-risk scenarios. |

- **Resource path format (`sandbox_resource_name`):** `projects/{project}/locations/{location}/reasoningEngines/{engine_id}/codeSessions/{session_id}`

**Takeaway:** Always use `AgentEngineSandboxCodeExecutor` (backed by a `us-central1` sandbox) in production; reserve `BuiltInCodeExecutor` for local prototyping.

---

## Runner Setup and Multi-Turn State Separation `[3.1]`

ADK's `Runner` wires an `LlmAgent` to separate services for **sessions**, **artifacts**, and **memory**:

- **Dev/testing:** `InMemorySessionService`, `InMemoryArtifactService`, `InMemoryMemoryService` (lost on restart).
- **Production memory:** Swap `InMemoryMemoryService` for **`VertexAiMemoryBankService`** for persistent, vector-backed semantic search and cloud storage.
- **Multi-turn state separation:** `session_service` tracks **conversational context** across turns, while the **sandbox** tracks **execution state** (e.g., a CSV loaded in Turn 1 stays in sandbox memory for Turn 2 metric computation without reloading).
- **Streaming execution:** `runner.run_async(...)` streams intermediate events (text, tool calls, code execution) and marks the final synthesized response with `event.is_final_response()`.

### Worked Example: ADK `LlmAgent` + `Runner` with Sandbox

```python
from google.adk.agents import LlmAgent
from google.adk.code_executors.agent_engine_sandbox_code_executor import (
    AgentEngineSandboxCodeExecutor,
)
from google.adk.runners import Runner
from google.adk.sessions import InMemorySessionService
from google.adk.artifacts import InMemoryArtifactService
from google.adk.memory import InMemoryMemoryService

agent = LlmAgent(
    model="gemini-2.5-flash",
    name="financial_analyst",
    description="Analyzes portfolio data using code execution",
    instruction="You are a financial analyst. Use code to compute metrics.",
    code_executor=AgentEngineSandboxCodeExecutor(
        sandbox_resource_name=sandbox_resource_name
    ),
)

session_service = InMemorySessionService()
await session_service.create_session(
    app_name="enterprise_app", user_id="user_1", session_id="session_001"
)

runner = Runner(
    agent=agent,
    app_name="enterprise_app",
    session_service=session_service,
    artifact_service=InMemoryArtifactService(),
    memory_service=InMemoryMemoryService(),
)

async for event in runner.run_async(
    user_id="user_1",  # Must match create_session's user_id
    session_id="session_001",
    new_message=genai_types.Content(
        role="user",
        parts=[genai_types.Part(text="Analyze this dataset and compute the mean values.")],
    ),
):
    if event.is_final_response():
        print(event.content.parts[0].text)
```

> **Clarification (not in source):** The raw course capture used `user_id="user_1"` in `create_session` and `user_id="analyst_1"` in `runner.run_async`. In ADK, `runner.run_async` must pass the **same `user_id`** used to create the session (`"user_1"`); otherwise the session lookup fails.

**Takeaway:** `Runner` separates conversation state (`session_service`) from Python execution state (the sandbox) and uses `event.is_final_response()` to detect the final answer.

---

## Resource Cleanup and Cost Management `[4.2]`

Both **Agent Engines** and **Agent Engine sandboxes** are **billable resources** that require explicit lifecycle hygiene:

- **Delete a single sandbox:** `client.agent_engines.sandboxes.delete(name=sandbox_resource_name)`
- **Cascade-delete Agent Engine + all child sandboxes:** `agent_engine.delete(force=True)`
- **Production cleanup pattern:**
  1. Provision a sandbox at user session start and delete it on session close.
  2. Wrap execution in `try` / `finally` so cleanup runs even on exceptions.
  3. Run a periodic cleanup job listing sandboxes and deleting idle ones beyond your timeout (relying on the sandbox **TTL** only as a fallback if the app crashes).

**Takeaway:** Wrap sandbox sessions in `try/finally` deletion and backstop with a periodic idle-cleanup job—don't rely solely on the 14-day max TTL.

---

## Common Confusions

| Confusion | Reality |
|---|---|
| `BuiltInCodeExecutor` vs `AgentEngineSandboxCodeExecutor` | `BuiltInCodeExecutor` is in-process, stateless, and unisolated; `AgentEngineSandboxCodeExecutor` is isolated, stateful, and requires a `us-central1` sandbox. |
| Where execution state lives across turns | Conversation history lives in `session_service`; loaded DataFrames/variables live in the **sandbox**. |
| Relying on TTL for cost control | TTL can keep idle sandboxes alive up to 14 days; explicitly call `delete` in `try/finally` + periodic sweeps. |
| `agent_engine.delete()` without `force=True` | `force=True` is required to cascade-delete child sandboxes along with the parent Agent Engine. |
