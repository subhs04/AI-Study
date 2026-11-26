# 02 — ADK Setup & Execution Modes

**Module map:** Covers ADK project setup (`venv`, directory layout, `.env` auth routing), `root_agent` anatomy in `agent.py`, and the four ways to run and interact with ADK agents (**Web UI**, **CLI**, **API Server**, and **Programmatic Interface** using `Runner`, `SessionService`, `ArtifactService`, and `Content`).
**Exam objectives:** `3.1` (Designing and building agentic workflows in code).
**Prerequisites:** [01 — ADK Architecture & Google Cloud Agent Paths](01-adk-architecture-and-agent-paths.md). **Next:** [03 — Multi-Agent Tree & Workflow Agents](03-multi-agent-tree-and-workflows.md)

---

## 1. Project Structure & Configuration `[3.1]`

**What it is:** A standardized Python virtual environment and folder layout that ADK's CLI, Web UI, and API Server expect when discovering and running agents.

**Why it exists:** Isolating dependencies avoids Python package conflicts, and a predictable directory contract lets ADK tooling load agents and their credentials without custom wiring.

### Environment & Directory Layout
1. **Virtual Environment:** Create and activate an isolated Python virtual environment (e.g., `.adk`), then install ADK via `pip`.
2. **Agent Directory Contract** (required for **Web UI**, **CLI**, and **API Server**):
   ```
   my_agent_folder/
   ├── __init__.py    # Marks directory as a Python package
   ├── agent.py       # Defines the agent (must expose root_agent)
   └── .env           # Auth & config (Gemini API vs. Vertex AI)
   ```
3. **`.env` Authentication Routing:** Tells ADK agents whether to authenticate through the **Gemini API** (API key) or **Vertex AI / Agent Platform** (Google Cloud project/location).

### Defining `root_agent` in `agent.py`
* **`root_agent` (required):** Every `agent.py` must assign its entry-point agent to a variable named `root_agent`.
* **`description`:** Tells a **parent agent or peer agent** what this agent does so it can decide whether to transfer control of the conversation to it.
* **`instruction`:** The system prompt governing how the agent behaves and acts.
* **Optional parameters:**
  * `generate_content_config`: Model parameters such as `temperature`.
  * `tools`: Functions, built-in tools, or agent tools the agent can invoke.
  * `input_schema` / `output_schema`: Structured input and output contracts.
  * **Callbacks**: Custom functions hooked into agent, model, or tool lifecycle events.

**Takeaway:** Standard ADK tooling discovers agents via `__init__.py`, `.env` (Gemini API vs. Vertex AI auth), and `agent.py` exposing `root_agent` with clear `description` and `instruction` fields.

---

## 2. Four Ways to Interact with ADK Agents `[3.1]`

**What it is:** Four execution interfaces that run the **exact same `agent.py` definition**, differing only in how interactions are initiated and managed.

**Why it exists:** Developers need visual debugging locally, fast terminal scripting, REST endpoints for external apps, and fine-grained Python embedding for custom services or notebooks.

| Mode | How It Runs | Production-Ready? | Requires Folder Layout? | Best-Fit Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **1. Web UI** | Local web server opened in browser | **No** (local testing only) | Yes | Visual interaction, monitoring behavior, debugging, and saving eval sessions |
| **2. CLI** | Interactive terminal session | **No** (local testing only) | Yes | Quick tests, scripting, and terminal-first development |
| **3. API Server** | Flask REST API server (default port `8000`) | **Yes** | Yes | Integrating agents with external apps/services over REST |
| **4. Programmatic Interface** | Python code or notebooks (Jupyter/Colab) using `Session` + `Runner` | **Yes** | **No** (single file/cell OK) | Deep embedding in Python apps, custom workflows, and fine-grained execution control |

**Clarification (not in source):** The standard ADK CLI commands for these modes are `adk web`, `adk run`, and `adk api_server` (the video transcript shows them on screen without reading them aloud).

**Takeaway:** Use **Web UI** and **CLI** only for local testing; use **API Server** (REST) or the **Programmatic Interface** (`Runner` + `Session`) for production integrations.

---

## 3. Programmatic Execution Flow (`Runner` & `Session`) `[3.1]`

When embedding ADK directly in Python (without `adk web`/`cli`/`api_server`), you explicitly wire the services and process the event stream:

```
1. Create Services          2. Create Session         3. Prepare Content
┌────────────────────┐     ┌──────────────────┐     ┌─────────────────────────┐
│ InMemorySession-   │────▶│ Track single     │────▶│ google.genai.types:     │
│ Service (ephemeral)│     │ conversation     │     │ query → Parts → Content │
│ InMemoryArtifact-  │     │ + internal state │     │ + Role ("user")         │
│ Service (ephemeral)│     └──────────────────┘     └────────────┬────────────┘
└────────────────────┘                                           │
                                                                 ▼
5. Consume Event Stream     4. Execute via Runner
┌────────────────────┐     ┌──────────────────────────────────────────────────┐
│ Loop over events:  │◀────│ Runner(agent, session_service, artifact_service) │
│ • LLM response     │     │ • Sends query + context to LLM                   │
│ • Tool called      │     │ • Interprets LLM output & invokes tools          │
│ • Tool result      │     │ • Updates session state & manages artifacts      │
│ • Final response   │     └──────────────────────────────────────────────────┘
└────────────────────┘
```

* **Ephemeral In-Memory Services:** `InMemorySessionService` (conversation history and state variables) and `InMemoryArtifactService` (files/data generated or used by the agent) are **ephemeral**—all data is lost when the Python process exits.
* **Worked Example (Programmatic vs. Web UI):**
  *Scenario (illustrative):* You want to run your agent inside a Jupyter notebook for a quick experiment without creating `__init__.py` or `agent.py`.
  *Cross-check:* Because the **Programmatic Interface** does not require the folder structure, you can define the agent, instantiate `InMemorySessionService` and `InMemoryArtifactService`, wrap the prompt in `Content`/`Parts` with a `Role`, and iterate over the `Runner` event stream in a single notebook cell—knowing state disappears once the kernel restarts.

**Takeaway:** The **Programmatic Interface** bypasses the directory layout by wiring `SessionService`, `ArtifactService`, `Content`/`Role`, and `Runner` directly in Python and iterating over the resulting event stream.

---

## Common Confusions

| Term A | Term B | Key Distinction |
| :--- | :--- | :--- |
| **`description`** | **`instruction`** | **`description`** helps *parent or peer agents* decide whether to route/transfer to this agent; **`instruction`** is the *system prompt* telling the agent itself how to behave. |
| **Web UI / CLI** | **API Server / Programmatic** | **Web UI** and **CLI** are for *local testing only*; **API Server** (REST) and **Programmatic Interface** (`Runner`) can be used in *production*. |
| **`InMemorySessionService`** | **`InMemoryArtifactService`** | **SessionService** stores *conversation history and state variables*; **ArtifactService** stores *files and binary data* used or generated by the agent (both are ephemeral in-memory). |

*Sources: `Deploy Multi-Agent Systems with Gemini Enterprise Agent Platform/Sources/Raw.md` (Lesson 3).*
