# Coverage Tracker

Phase 1 (MCQ only). Status: Course status is from `roadmap.md`. **Notes** shows whether certification notes exist (✅ reviewed, 🟡 exists but made by Ask Gemini, not yet reprocessed to the AGENTS.md standard, ⬜ none). **Mapped** objectives come from the roadmap and are **unverified** until confirmed during processing.

## Courses

### Must Do (ordered by priority; exam weight × coverage gap)
| # | Course | Course status | Targets (unverified) | Weight | Module folder | Notes |
|---|---|---|---|---|---|---|
| 1 | Deploy Multi-Agent Systems (Gemini Enterprise Agent Platform) | Not started | 3.3 | S3 ~33% | Deploy Multi-Agent Systems | ⬜ |
| 2 | Evaluate Agents on Gemini platform | Not started | 4.1 | S4 ~22% | Evaluate Agents | ⬜ |
| 3 | AgentOps: Operationalize AI Agents | Done: course complete, all 5 captures processed (missing captures: intro page, environments detail, lens detail panels, quizzes) | 4.2 per roadmap; content also maps to 4.1, 5.1, 5.2, 3.1 (partial) | S4 ~22% | AgentOps- Operationalize AI Agents on Google Cloud | ✅ 01-agentops-lifecycle, 02-build-and-scale-platform, 03-govern-and-optimize-platform, 04-evaluation-as-unit-tests, 05-cicd-pipeline-quality-gates, 06-canary-deployments-and-rollback, 07-iam-service-accounts-authn-authz, 08-state-memory-and-data-perimeters, 09-gateway-credential-isolation-guardrails, 10-agent-tracing-opentelemetry, 11-structured-logging-cloud-logging |
| 4 | Govern agents with Agent Gateway, Agent Registry, Policies | Captured (diagrams included; 5 lesson texts unretained in raw capture) | 5.1 per roadmap; content maps to 5.1, 5.2, 4.2, 3.2 (partial) | S5 ~15% | Govern agents with Agent Gateway, Agent Registry, and Policies | ✅ 01-centralized-gateway-and-dual-paths, 02-enterprise-networking-and-tool-ingress, 03-identity-access-control-and-guardrails, 04-observability-tracing-and-governance |
| 5 | Understand Google Cloud Agents | In progress | 1, 3, 5 | foundational | Understand Google Cloud Agents | ⬜ |
| 6 | Build Enterprise Agents with Code Execution | In progress | 2 | S2 ~17% | Code Execution Agents | ⬜ |
| 7 | Build Agents with the Agent Development Kit | Done (watched) | 3.1 | S3 ~33% | Build Agents with ADK | ⬜ notes pending |
| 8 | Build Agent Skills with Google | Done (watched) | 2.2, 3.1 | S2/S3 | Build Agent Skills | ⬜ notes pending |
| 9 | Optimize Agent Behavior | Done (watched) | 1.1 | S1 ~13% | Optimize Agent Behavior | ⬜ notes pending |
| 10 | Improve Agent Search Findings | Done (Gemini capture processed) | 3.2 per roadmap; capture maps to 1.2 (3.2 partial only) | S1/S3 | Improve Agent Search | ✅ end-to-end-overview, metadata-ingestion-schema, serving-controls (source is a Gemini-processed capture, not raw) |
| 11 | Model Armor: Securing AI Deployments | Done (challenge lab parked) | 5.1, 5.2 | S5 ~15% | Model Armor | ✅ overview-threats-filters, modes-logging-floor-settings, coverage-limits-data-handling |

> Items 7-9 are watched but unprocessed: processing them is cheap (sources already captured) and covers S3/S2/S1, so do it right after the gap courses (1-4).

### Good to Have (only if time remains; first in line is highest value)
| Course | Mapped (unverified) | Module folder | Notes |
|---|---|---|---|
| Craft ADK Agents with Persistent Memories | 3.1 (Memory Bank, sessions are named in the exam guide; 7+ hours, skim concepts only) | | ⬜ |
| Secure Your Agents With Gemini Platform | 5 | | ⬜ |
| Hill Climb Generative and Agentic Systems | 4.2 | | ⬜ |
| Build AI Agents with Enterprise Databases | 1.2 | Enterprise Databases | ⬜ |
| Agentic Strategy: Discover, Design, Prototype (moved from Must Do: strategy-heavy, low direct exam overlap; notes already exist) | 1.1 per roadmap; content maps to 1.1, 1.2 (partial), 3.x/4.x/5.x as scenario context only | Agent Strategy Discover Design Prototype | ✅ course-mindmap, framework-and-lifecycle, discover-design-value-prioritization, design-best-practices-cuj, prototyping-tools-and-ucp, gemini-enterprise-walkthrough, prompt-engineering-tuning-grounding |
| Deploy Gemini to Transform Enterprises | n/a (business) | | ⬜ |

### Phase 2: Labs and badges (parked until the MCQ is passed; not tracked, docs not processed)
Per `AGENTS.md`, lab documentation is ignored in Phase 1. Objectives listed are from titles only.

| Lab | Likely objectives | Status |
|---|---|---|
| Govern Agent Access with Gemini Enterprise Agent Platform: Challenge Lab | 5.1, 3.2, 4.2 (title: Agent Identity, Agent Runtime, BigQuery toolset) | Completed; resources saved in its folder for second pass |
| Engineer AI Agents with ADK | 3.1 | Parked |
| CI/CD and Quality Assurance: Evaluate ADK agent performance using the Agent Platform Generative AI Evaluation Service (lab inside the AgentOps course) | 4.1 (title: Gen AI evaluation service, ADK agents) | Parked; its intro was in the capture but not used for notes |
| Deploy Gemini with Workspace Data Sources and Model Armor | 1.2, 5.x | Parked |
| Secure Agent Tools with Agent Identity and Auth Manager | 5.1 | Parked |
| Build No-Code Agents with Agent Designer | 1.1 | Parked |
| Add Agents to Gemini | 1.x | Parked |
| Accelerate Development with Antigravity | 2.1, 2.2 | Parked |
| Deploy an Agent with ADK | 4.2 | Parked |
| Evaluate and Improve ADK Agents | 4.1 | Parked |

> MCQ gap warning: Agent Identity/Auth Manager, Antigravity and Workflow Builder content lives mainly in parked labs. For the MCQ, cover them with theory sources (course readings, official docs), not the labs.

## Objective matrix (exam-guide.md)

| ID | Objective | Weight | Covered by notes | Gaps |
|---|---|---|---|---|
| 1.1 | Low-code workflows and behavior | ~13% (S1) | Agent Strategy ✅ (partial: prompt anatomy, system instructions, few-shot, CoT mention, pre-built agents) | Workflow Builder / CX Agent Studio state-based workflows (pages, routes, event handlers) not covered |
| 1.2 | Connect enterprise data to Gemini Enterprise | | Improve Agent Search ✅; Agent Strategy ✅ (gemini-enterprise-walkthrough: identity, Drive/Calendar/GCS data stores) | Video/audio ingestion, Gemini Enterprise connections beyond demo |
| 2.1 | Using coding agents effectively | ~17% (S2) | | |
| 2.2 | Customizing coding agents | | | |
| 3.1 | Workflows in code | ~33% (S3) | Agent Strategy ✅ (concept only: model selection, tuning ladder, short/long-term memory, ADK mention); AgentOps ✅ (concept: ADK vs Agent Studio, Sessions vs Memory Bank, externalizing state to Firestore/Cloud SQL) | ADK build, Memory Bank, sessions, Agents CLI, LLM vs SLM, OSS vs proprietary |
| 3.2 | Enterprise domain knowledge | | AgentOps ✅ (Agent Registry, Agent Identity concept; dedicated service accounts, least privilege, authN vs authZ); Govern Agents ✅ (Agent Registry egress allowlist concept); Improve Agent Search ✅ (minimal); Agent Strategy ✅ (grounding vs RAG concept only) | Embeddings, reranking, Vector Search, Agent Identity/Registry runtime discovery, MCP |
| 3.3 | Orchestrating agentic workflows | | AgentOps ✅ (Registry discovery only); Agent Strategy ✅ (context only: hierarchical pattern, single vs multi-agent, UCP) | A2A, MCP, parallel/sequential/graph, Agent Runtime/Registry |
| 4.1 | Evaluating agents | ~22% (S4) | AgentOps ✅ (golden dataset, trajectory vs final-response evaluation, CI quality gate with threshold, offline vs online evaluation, Agent Evaluation/Simulation concept); Agent Strategy ✅ (concept only: golden dataset, ground truth, supervisor model) | ADK evalset, Gen AI evaluation service internals, autoraters, pointwise vs pairwise judging (only appears in a parked lab intro; use a theory source), metric names |
| 4.2 | Deploying and scaling | | AgentOps ✅ (lifecycle, five-step CI/CD pipeline, canary traffic splitting and instant rollback, canary metrics, agent KPIs, Agent Runtime/Sandbox/Sessions/Memory Bank, Observability/Optimizer, OpenTelemetry spans and Cloud Trace waterfall patterns, structured logging and Cloud Logging queries for hallucination/config/instruction diagnosis); Govern Agents ✅ (Cloud Run public vs private ingress, OpenTelemetry/Cloud Trace 6-span hierarchy, Authorization Debugging Dashboard); Agent Strategy ✅ (concept only: AgentOps, CI/CD, monitoring and alerting) | Cloud Run/GKE vs Agent Runtime selection, alert/dashboard configuration; environments detail not captured |
| 5.1 | Security and governance configuration | ~15% (S5) | Model Armor ✅ (partial); AgentOps ✅ (Agent Registry, Gateway, Identity; IAM least privilege, service accounts, authN/authZ, auth methods incl. OAuth client ID, gateway benefits, Secret Manager); Govern Agents ✅ (Agent Gateway Ingress/Egress, IAP/OIDC, Network Attachment/PSC/DNS peering, REQUEST_AUTHZ, CONTENT_AUTHZ + Model Armor/DLP, Access Transparency, DRY_RUN -> null rollout); Agent Strategy ✅ (principle only: agent as digital employee, least privilege) | PAB policies, full OAuth 2.0 flow, mTLS personas, SCC Agent Threat Detection, and Agent Registry runtime discovery not in captured text |
| 5.2 | Secure agent behavior and execution | | Model Armor ✅ (partial); AgentOps ✅ (Gateway + Model Armor in transit, VPC Service Controls, credential isolation, input/output guardrails, confused deputy); Govern Agents ✅ (Egress isolation, CONTENT_AUTHZ Model Armor request/response templates, Cloud DLP inspect/de-identify templates, Access Transparency); Agent Strategy ✅ (HITL and kill switch principles, guardrails on inputs/outputs/actions) | HITL (not in course), identity propagation, VPC SC configuration details not covered |
