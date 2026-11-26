# 04 — Gemini Platform & Enterprise Connectivity

**Module map:** Covers the developer-focused **Gemini Platform** lifecycle (**Agent garden**, **ADK**, **Agent runtime**, **Gemini API**), enterprise connectivity (90+ connectors, >800K Apigee APIs, BigQuery Data Agents), **Model Garden**, and open ecosystem standards.
**Exam objectives:** `3.1` (partial — model ecosystem & ADK/runtime roles), `3.2` (partial — RAG, enterprise connectors, public grounding), `4.1` (partial — Example Store & Agentic Autorater mention).
**Prerequisites:** [01 — Agent Foundations, Architecture & Platform Selection](01-agent-foundations-and-architecture.md). **Next:** [05 — ADK Multi-Agent Architecture & Orchestration](05-adk-multi-agent-and-orchestration.md)

---

## 1. Gemini Platform Developer Lifecycle `[3.1]`, `[4.2]`

For engineers building custom, code-first agents, the **Gemini Platform** organizes the lifecycle around four components:

| Phase | Component | What It Is & Why It Exists |
| :--- | :--- | :--- |
| **Design** | **Agent garden** | Centralized library of pre-built agent samples, blueprints (e.g., *data science agent blueprint*), and reusable tools with **GitHub** source code access to accelerate prototyping. |
| **Development** | **Agent Development Kit (ADK)** | Open-source framework defining agent reasoning, **outer-loop orchestration**, tool selection, and dynamic model selection. |
| **Deployment** | **Agent runtime** *(formerly Agent Engine)* | Fully managed, serverless production runtime providing auto-scaling, sessions & short-term memory, **long-term memory bank**, **Example Store** (few-shot examples), **Agentic Autorater** (automated QA), code execution/simulation, and trace logging. |
| **Intelligence** | **Gemini API** | Exposes multimodal models optimized for agentic reasoning plus **inner-loop tools** (e.g., code interpreters, search) and real-time audio/video streaming. |

*(Note: **Agent Registry** is also referenced alongside Agent garden and ADK as a component for discovering and reusing pre-built agent tools.)*

**Takeaway:** Prototype from **Agent garden** blueprints, implement logic and orchestration in **ADK**, and deploy to **Agent runtime** for managed scaling, memory, and security.

---

## 2. Enterprise Connectivity & Grounding Sources `[3.2]`

An agent is only as capable as its access to authoritative data and systems:

* **Public Grounding:** **Google Search** (real-time web facts) and **Google Maps** (real-time business locations, hours, status).
* **Enterprise Data Grounding:** Internal documents, databases, **knowledge graph data** (for complex entity reasoning), and **BigQuery Data Agents** (dedicated data-for-agents tools for internal tables, history, and usage logs).
* **Application & API Connectivity:**
  * **90+ pre-built connectors** to major enterprise SaaS (e.g., **Salesforce**, **SAP**).
  * **>800K custom APIs** (commonly managed via **Apigee**) for proprietary systems.

**Takeaway:** Combine RAG over internal stores (BigQuery, CRM/ERP) with Google Search/Maps for public facts and Apigee/pre-built connectors for enterprise actions.

---

## 3. Native Tool Use, Model Garden & Open Ecosystem `[3.1]`, `[3.2]`, `[3.3]`

### 4-Step Native Tool Use Workflow
1. **User input:** User asks a question or issues a command.
2. **Model reasoning:** Model analyzes intent and selects the needed external action (e.g., *"Call the inventory API"*).
3. **Tool execution:** Executes the tool/API call to fetch live data or perform an action.
4. **Informed response:** Synthesizes tool output into an accurate answer or next action.

### Model & Framework Flexibility
* **Model Garden:** Catalog of **hundreds (100s) of curated third-party and open-source LLMs**, letting teams pick specialized models per task.
* **Open-Source Frameworks:** Interoperates with **LangChain**, **LangGraph**, **LlamaIndex**, and **CrewAI**.
* **Open Protocols:** Supports **MCP** (agent-to-tool/context) and **A2A** (agent-to-agent communication).

**Takeaway:** The Gemini Platform avoids lock-in by pairing Gemini models with **Model Garden** (third-party/OSS LLMs), open-source frameworks, and **MCP/A2A** protocols.

---

## 4. Worked Example: Cymbal Labs Custom ROI Agent `[3.1]`, `[3.2]`

* **Problem:** Cymbal Labs' sales reps sell multi-million-dollar custom hardware/cartridge/service packages and need verifiable 3-year ROI models—generic quotes are insufficient.
* **Architecture:**
  1. **Design (`Agent garden`):** Start from the pre-built **data science agent blueprint**.
  2. **Develop (`ADK`):** Connect to **BigQuery** (device usage logs) and wrap Cymbal Labs' proprietary **Python optimization algorithms** as a custom tool.
  3. **Ground (`RAG`):** Retrieve internal pricing rules, CRM/ERP history, and device logs to ground the prompt (*"Calculate 3-year ROI for customer X"*).
  4. **Orchestrate (`ADK`):** Ground inputs → run **Optimization Agent** (minimizes TCO, maximizes ROI) → synthesize client-ready financial brief.
  5. **Deploy (`Agent runtime`):** Serverless scaling, session/context management, **VPC Service Controls**, **CMEK**, and **data residency** for sensitive clinical/genomic data.
* **Quiz Cross-Checks:**
  * *Managed infrastructure for production scaling, security, and runtime:* **Agent runtime**.
  * *Finding and reusing a pre-built CRM tool (select two):* **Agent garden** and **Agent Registry** *(or ADK depending on exact quiz key; both Agent garden and Agent Registry are catalog/discovery components—verify in official docs if needed)*.

---

## Common Confusions

| Term A | Term B | Key Distinction |
| :--- | :--- | :--- |
| **Agent garden** | **Model garden** | **Agent garden** is a library of pre-built *agent* blueprints/samples and reusable tools; **Model garden** is a catalog of *LLMs* (first-party, third-party, OSS). |
| **Agent runtime** | **ADK** | **ADK** is the open-source *development framework* you write code in; **Agent runtime** is the managed *hosting/execution infrastructure* where it runs in production. |
| **Inner-loop tools** | **Outer-loop orchestration** | **Inner-loop tools** (code interpreter, search) are exposed inside the **Gemini API**; **outer-loop orchestration** (multi-step, multi-agent coordination) is managed by **ADK**. |

> [!NOTE]
> **Gap (`3.1`, `3.2`):** The exam guide expects criteria for **LLM vs. SLM**, **self-hosted vs. SaaS**, **embedding models, similarity scoring, reranking, and Vector Search**. Those deeper topics are not covered in this overview course.

*Sources: Building Agents with Gemini (`building-agents-with-gemini-raw.md`), Developing agents with Google Cloud (`gemini-code-1795710966079.md`).*
