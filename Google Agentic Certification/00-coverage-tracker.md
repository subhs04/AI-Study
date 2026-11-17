# Coverage Tracker

Status: Course status is from `roadmap.md`. **Notes** shows whether certification notes exist (✅ reviewed, 🟡 exists but made by Ask Gemini, not yet reprocessed to the AGENTS.md standard, ⬜ none). **Mapped** objectives come from the roadmap and are **unverified** until confirmed during processing.

## Courses

### Must Do
| Course | Course status | Mapped (unverified) | Module folder | Notes |
|---|---|---|---|---|
| Understand Google Cloud Agents | In progress | 1, 3, 5 | Understand Google Cloud Agents | ⬜ |
| Agentic Strategy: Discover, Design, Prototype | Not started | 1.1 | Agentic Strategy | ⬜ |
| Optimize Agent Behavior | Done | 1.1 | Optimize Agent Behavior | ⬜ |
| Build Agent Skills with Google | Done | 2.2, 3.1 | Build Agent Skills | ⬜ |
| AgentOps: Operationalize AI Agents | Not started | 4.2 | AgentOps | ⬜ |
| Deploy Multi-Agent Systems (Gemini Enterprise Agent Platform) | Not started | 3.3 | Deploy Multi-Agent Systems | ⬜ |
| Improve Agent Search Findings | Not started per roadmap; Gemini capture processed | 3.2 per roadmap; capture and notes map to 1.2 (3.2 partial only) | Improve Agent Search | ✅ metadata-ingestion-schema, serving-controls (source is a Gemini-processed capture, not raw) |
| Model Armor: Securing AI Deployments | Done (challenge lab deferred) | 5.1, 5.2 | Model Armor | ✅ overview-threats-filters, modes-logging-floor-settings, coverage-limits-data-handling |
| Govern agents with Agent Gateway, Agent Registry, Policies | Not started | 5.1 | Govern Agents | ⬜ |
| Build Agents with the Agent Development Kit | Done | 3.1 | Build Agents with ADK | ⬜ |
| Build Enterprise Agents with Code Execution | In progress | 2 | Code Execution Agents | ⬜ |
| Evaluate Agents on Gemini platform | Not started | 4.1 | Evaluate Agents | ⬜ |

### Good to Have
| Course | Mapped (unverified) | Module folder | Notes |
|---|---|---|---|
| Build AI Agents with Enterprise Databases | 1.2 | Enterprise Databases | ⬜ |
| Deploy Gemini to Transform Enterprises | n/a (business) | | ⬜ |
| Secure Your Agents With Gemini Platform | 5 | | ⬜ |
| Craft ADK Agents with Persistent Memories | 3.1 | | ⬜ |
| Hill Climb Generative and Agentic Systems | 4.2 | | ⬜ |

### Can Skip (labs/badges): not tracked
Engineer AI Agents with ADK · Deploy Gemini with Workspace Data Sources and Model Armor · Secure Agent Tools with Agent Identity and Auth Manager · Govern Agent Access with Gemini platform · Build No-Code Agents with Agent Designer · Add Agents to Gemini · Accelerate Development with Antigravity · Deploy an Agent with ADK · Evaluate and Improve ADK Agents

> Several skipped labs cover exam topics (Agent Identity/Auth Manager, Antigravity, Agent Designer, ADK deployment). Revisit once the Must Do courses are processed, to see which objectives remain gaps.

## Objective matrix (exam-guide.md)

| ID | Objective | Weight | Covered by notes | Gaps |
|---|---|---|---|---|
| 1.1 | Low-code workflows and behavior | ~13% (S1) | | |
| 1.2 | Connect enterprise data to Gemini Enterprise | | Improve Agent Search ✅ | Multimodal ingestion, Gemini Enterprise connections |
| 2.1 | Using coding agents effectively | ~17% (S2) | | |
| 2.2 | Customizing coding agents | | | |
| 3.1 | Workflows in code | ~33% (S3) | | |
| 3.2 | Enterprise domain knowledge | | Improve Agent Search ✅ (minimal) | Embeddings, reranking, Vector Search, Agent Identity/Registry, MCP |
| 3.3 | Orchestrating agentic workflows | | | |
| 4.1 | Evaluating agents | ~22% (S4) | | |
| 4.2 | Deploying and scaling | | | |
| 5.1 | Security and governance configuration | ~15% (S5) | Model Armor ✅ (partial) | Agent Gateway, Agent Registry, PAB, OAuth 2.0 not in this module |
| 5.2 | Secure agent behavior and execution | | Model Armor ✅ (partial) | HITL, Agent Gateway, identity propagation not in this module |
