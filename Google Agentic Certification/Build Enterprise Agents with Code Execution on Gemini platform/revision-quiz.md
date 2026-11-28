# Revision Quiz: Build Enterprise Agents with Code Execution on Gemini platform

**Questions:** 12 · **Tip:** Tap **Show Answer** on GitHub Mobile to check your recall.

---

## 1. Gemini Platform and the Agent Sandbox ([note](notes/01-gemini-platform-and-agent-sandbox.md))

**1. [Compare]** Contrast **Agent Runtime** vs. **Agent Sandbox** across **Trust Domain**, **Network Access**, and **Lifecycle/Persistence**.

<details>
<summary>Show Answer</summary>

**Answer:**
- **Agent Runtime:** Trusted (vetted developer code); appropriate enterprise infrastructure access; long-running with sub-second cold starts.
- **Agent Sandbox:** Untrusted/Isolated (dynamically synthesized model code); **zero network access** and limited filesystem; short-lived/ephemeral (retains state up to **14 days**, cleaned up after use).
**Why it matters:** Never run model-generated code in your trusted agent process—isolate it in Agent Sandbox to prevent host compromise and data exfiltration.
**Ref:** `[4.2]` `[5.2]` · [01-gemini-platform-and-agent-sandbox.md](notes/01-gemini-platform-and-agent-sandbox.md)

</details>

**2. [Recall]** What are the **three security & isolation properties** of Agent Sandbox, and what happens if model-generated code tries to run `pip install`?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Containment** (no host/network access), **Tenant Isolation** (strictly isolated from other sandboxes in the same project and other customers), and **Predictability** (fixed set of **125+ pre-installed packages** like NumPy, Pandas, Matplotlib, scikit-learn).
- Runtime `pip install` fails at the boundary because there is no network access and the package set is fixed.
**Why it matters:** Regulated multi-tenant deployments rely on a bounded, auditable attack surface with zero arbitrary package installation.
**Ref:** `[5.2]` · [01-gemini-platform-and-agent-sandbox.md](notes/01-gemini-platform-and-agent-sandbox.md)

</details>

**3. [Trigger → Choice]** Your agent needs to automate web UI tasks (navigating sites, filling forms) inside an isolated environment rather than running a Python/JS interpreter on files. Which **Agent Sandbox execution mode** should you choose?

<details>
<summary>Show Answer</summary>

**Answer:** **Computer Use** (sandboxed browser environment), rather than **Code Execution** (sandboxed Python/JavaScript interpreter).
**Why it matters:** Both are Agent Sandbox modes, so match browser UI automation to Computer Use and file/data/chart computation to Code Execution.
**Ref:** `[4.2]` · [01-gemini-platform-and-agent-sandbox.md](notes/01-gemini-platform-and-agent-sandbox.md)

</details>

**4. [True / False]** *Spot the Trap:* **Agent Engine** and **Agent Runtime** are two names for the same trusted execution environment in the Gemini platform's Scale pillar.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** **Agent Engine** is the top-level parent container resource in the API/SDK (`client.agent_engines`) that owns sandboxes and deployments (and must be created before provisioning a sandbox); **Agent Runtime** is the trusted execution environment in the **Scale** pillar.
**Why it matters:** Confusing the parent API container with the trusted runtime environment is a common terminology trap.
**Ref:** `[4.2]` · [01-gemini-platform-and-agent-sandbox.md](notes/01-gemini-platform-and-agent-sandbox.md)

</details>

---

## 2. Sandbox Lifecycle, State, and the Agent Platform SDK ([note](notes/02-sandbox-lifecycle-state-and-sdk.md))

**5. [Recall]** What is the **only supported region** for Agent Sandbox Code Execution (as of June 3, 2026), and how does the **14-day TTL** behave when `execute_code` is called again?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Region:** **`us-central1`** only (explicitly set `location="us-central1"` on the `vertexai` client if your project defaults elsewhere).
- **TTL:** It is a **sliding TTL** (configurable up to 14 days)—every `execute_code` call **resets the TTL timer**.
**Why it matters:** Provisioning in another region fails, and the TTL is an idle timer rather than a hard 14-day cutoff from creation.
**Ref:** `[4.2]` · [02-sandbox-lifecycle-state-and-sdk.md](notes/02-sandbox-lifecycle-state-and-sdk.md)

</details>

**6. [Trigger → Choice]** Your agent trains `scikit-learn` models on large datasets that exceed the default **2 vCPU / 1.5 GB RAM** tier. Which `machine_config` key do you specify, and what specs does it provide?

<details>
<summary>Show Answer</summary>

**Answer:** Pass `"MACHINE_CONFIG_VCPU4_RAM4GIB"` (Large tier), which provides **4 vCPU and 4 GB RAM**.
**Why it matters:** Default compute is 2 vCPU / 1.5 GB RAM when `machine_config` is omitted; memory-heavy ML workloads need the Large tier.
**Ref:** `[4.2]` · [02-sandbox-lifecycle-state-and-sdk.md](notes/02-sandbox-lifecycle-state-and-sdk.md)

</details>

**7. [Compare]** When parsing `response.outputs` (`Chunk` objects) from `execute_code`, how do you distinguish **stdout/stderr** from **generated file artifacts** (e.g., PNG charts)?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Stdout / Stderr:** `output.mime_type == "application/json"` and `output.metadata is None` → decode UTF-8 JSON and read keys `"msg_out"` (stdout) and `"msg_err"` (stderr).
- **File Artifacts:** Check `output.metadata.attributes["file_name"]` (decode `bytes` to `utf-8` if needed); `output.data` holds the raw file bytes.
**Why it matters:** `stdout` is wrapped in a JSON chunk under `"msg_out"`, not returned as plain text.
**Ref:** `[3.1]` · [02-sandbox-lifecycle-state-and-sdk.md](notes/02-sandbox-lifecycle-state-and-sdk.md)

</details>

**8. [True / False]** *Spot the Trap:* In-memory state (variables, imported libraries, loaded DataFrames) persists across sequential `execute_code` calls within the same sandbox **and** across different sandboxes under the same parent Agent Engine.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** In-memory state persists across sequential `execute_code` calls **within one sandbox** only; state **never** persists across different sandboxes (a new sandbox starts clean).
**Why it matters:** A single sandbox behaves like a stateful notebook kernel, but cross-sandbox isolation guarantees a clean slate per sandbox.
**Ref:** `[3.1]` `[4.2]` · [02-sandbox-lifecycle-state-and-sdk.md](notes/02-sandbox-lifecycle-state-and-sdk.md)

</details>

---

## 3. Integrating Code Execution into ADK Workflows and Cleanup ([note](notes/03-adk-code-execution-and-cleanup.md))

**9. [Compare]** Contrast **`AgentEngineSandboxCodeExecutor`** vs. **`BuiltInCodeExecutor`** in ADK across isolation, state persistence, region constraint, and intended environment.

<details>
<summary>Show Answer</summary>

**Answer:**
- **`AgentEngineSandboxCodeExecutor`:** Managed, **isolated**, **stateful across turns** (125+ libraries); requires a sandbox in **`us-central1`**; use for **production enterprise deployments**.
- **`BuiltInCodeExecutor`:** Runs **in-process** with the agent (**no isolation**, **stateless** across turns); no region restrictions; use only for **local dev/rapid prototyping**.
**Why it matters:** Using `BuiltInCodeExecutor` in production exposes the host agent process to untrusted model code.
**Ref:** `[3.1]` `[5.2]` · [03-adk-code-execution-and-cleanup.md](notes/03-adk-code-execution-and-cleanup.md)

</details>

**10. [Recall]** In ADK, what is the resource path format for `sandbox_resource_name`, and which service replaces `InMemoryMemoryService` for persistent, vector-backed semantic search in production?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Path format:** `projects/{project}/locations/{location}/reasoningEngines/{engine_id}/codeSessions/{session_id}`
- **Production memory service:** **`VertexAiMemoryBankService`**
**Why it matters:** `InMemoryMemoryService` is lost on restart; `VertexAiMemoryBankService` provides cloud persistence and semantic search.
**Ref:** `[3.1]` · [03-adk-code-execution-and-cleanup.md](notes/03-adk-code-execution-and-cleanup.md)

</details>

**11. [Trigger → Choice]** Your ADK `Runner` loads a CSV in Turn 1 and computes metrics in Turn 2. Where does **conversational context** live vs. where does **Python execution state** live, and how do you detect the final synthesized answer in `runner.run_async(...)`?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Conversational context:** `session_service` (e.g., `InMemorySessionService`).
- **Python execution state (loaded DataFrames/variables):** The **sandbox** (persists without reloading).
- **Final answer check:** `event.is_final_response()` while iterating `runner.run_async(...)` (and note `user_id` must match `create_session`).
**Why it matters:** ADK cleanly separates conversation history from sandbox memory, and streams intermediate events before the final response.
**Ref:** `[3.1]` · [03-adk-code-execution-and-cleanup.md](notes/03-adk-code-execution-and-cleanup.md)

</details>

**12. [True / False]** *Spot the Trap:* Calling `agent_engine.delete()` without arguments automatically cascade-deletes all child sandboxes, and relying on the 14-day TTL is sufficient for production cost control.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** You must pass **`force=True`** (`agent_engine.delete(force=True)`) to cascade-delete child sandboxes, and both Agent Engines and sandboxes are **billable resources**—you should delete sandboxes in `try/finally` blocks and run periodic idle-cleanup sweeps, using TTL only as a crash fallback.
**Why it matters:** Leaving sandboxes alive up to the 14-day sliding TTL incurs unnecessary billing.
**Ref:** `[4.2]` · [03-adk-code-execution-and-cleanup.md](notes/03-adk-code-execution-and-cleanup.md)

</details>
