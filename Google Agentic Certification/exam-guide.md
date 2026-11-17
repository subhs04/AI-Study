# Professional Agentic Architect: Exam Guide (extracted)

Source: official exam guide PDF (extracted verbatim, reformatted). Use the IDs below (1.1, 3.2 ...) for all tags.

**Candidate profile:** Technical practitioner who designs and manages autonomous, AI-driven agentic workflows in Google Cloud. Experienced developer/architect who builds agentic solutions considering reliability, performance, cost, security and scalability. Deep experience with LLMs, agent design patterns, code, and integrating data sources.

## Section 1: Building agents using low-code tools (~13%)

**1.1 Configuring agentic workflows and behavior using low-code tools**
- Configure state-based workflows (pages, transition routes, event handlers) using Gemini Enterprise tools (e.g., Gemini Enterprise Workflow Builder and Customer Experience Agent Studio [CX Agent Studio])
- Create system instructions and in-console prompt templates (e.g., few-shot and chain-of-thought) to guide agent behavior (e.g., Workflow Builder and CX Agent Studio)

**1.2 Connecting enterprise data to Gemini Enterprise**
- Configure agents to securely connect and query enterprise proprietary data sources (e.g., Gemini Enterprise and Agent Search)
- Ingest and process unstructured multimodal data (e.g., videos, audio, images) into the agentic workflow

## Section 2: Using coding agents for application development (~17%)

**2.1 Using coding agents effectively**
- Configure coding agents with Model Context Protocol (MCP) servers, custom skills, and access to tools (e.g., Antigravity and Claude Code on Google Cloud)
- Use coding agents in secure sandboxes (e.g., Google Kubernetes Engine [GKE], Cloud Workstations, and Antigravity)
- Use coding agents to refactor source code, optimize execution runtimes, and patch application-layer vulnerabilities

**2.2 Customizing coding agents for enterprise workflows**
- Create skills, plugins, extensions, hooks, rules, and subagents using Antigravity
- Augment Antigravity with Agents CLI to build, scale, govern, and optimize deployed agents

## Section 3: Developing custom agents (~33%)

**3.1 Designing and building agentic workflows in code**
- Select and configure the appropriate language model (LLM vs. small language model [SLM], self-hosted vs. SaaS, open-source [OSS] vs. proprietary LLM) considering cost, security, and agent architecture
- Build custom agents using open-source libraries (e.g., Agent Development Kit [ADK])
- Configure sessions and memory (e.g., Agent Platform Memory Bank and managed sessions)
- Configure skills using Agents CLI (e.g., plugins and agent vs. human mode)

**3.2 Integrating enterprise domain knowledge**
- Design, configure, and manage retrieval-augmented generation (RAG) pipelines and vector retrieval systems (e.g., embedding models, similarity scoring, reranking) using appropriate services such as vector databases (e.g., Vector Search and Agent Retrieval)
- Configure agent permissions (e.g., Agent Identity)
- Use Google Cloud tools (e.g., Agent Registry, Google Cloud MCP servers) to configure prebuilt and custom capabilities (e.g., custom integration layers for managed databases, API integrations, and MCP servers that connect agents to third-party SaaS tools and remote servers)

**3.3 Orchestrating and coordinating agentic workflows**
- Orchestrate agents using agentic protocols (e.g., MCP and Agent2Agent [A2A])
- Select and coordinate multi-agent handoffs and workflows (e.g., parallel agents, sequential agents, graph workflow) using Google Cloud tools (e.g., Agent Identity, Agent Registry, Agent Runtime, and agent policies)

## Section 4: Evaluating and deploying agentic workflows (~22%)

**4.1 Evaluating agents in development and in production**
- Create test sets for agent evaluation (e.g., golden data, prompts, edge cases)
- Create continuous evaluation pipelines to assess an agent's tool execution based on established success criteria
- Determine the appropriate evaluation framework and tooling (e.g., ADK evaluation tooling (evalset), Agent Platform Gen AI evaluation service, custom autoraters)
- Evaluate an agentic system against a golden dataset to assess agent response and retrieval quality (e.g., using ADK)

**4.2 Deploying and scaling production workloads**
- Select optimal deployment runtime based on use case, requirements, and cost (e.g., Agent Runtime, Cloud Run, GKE)
- Troubleshoot agent issues (e.g., drift, tool invocation latency, agent reasoning loops, system failures)
- Monitor and optimize agents for performance, reliability, and cost (e.g., identify logic errors, latency bottlenecks, hallucinations)

## Section 5: Securing and governing agentic workflows (~15%)

**5.1 Configuring agent security and governance**
- Implement authentication and secure tool execution (e.g., agent-to-tool API calls using OAuth 2.0)
- Configure Principal Access Boundary (PAB) policies using Agent Identity
- Configure Agent Gateway to monitor traffic and track agents
- Design and configure agentic governance and policy enforcement (e.g., Agent Registry and Model Armor)

**5.2 Implementing secure agent behavior and execution**
- Design appropriate safety frameworks and guardrails (e.g., Agent Gateway, Model Armor, human-in-the-loop [HITL])
- Configure secure access to data and identity propagation (e.g., Agent Gateway and Agent Registry)

## In-scope tools

Agent Development Kit (ADK) · Agent Gateway · Agent Identity · Agent Registry · Agent Retrieval and Vector Search · Agent Runtime (formerly Agent Engine) · Agent Search (formerly Vertex AI Search) · Agentic protocols (A2A, MCP) · Agents CLI in Agent Platform · Antigravity (CLI, SDK, App) · BigQuery · Cloud Run · Cloud SQL · Cloud Storage · Firestore · Gemini Enterprise · Gemini LLMs · Gen AI evaluation service · Google Cloud Observability (Cloud Logging, Cloud Trace) · GKE · Memorystore for Redis · Model Armor · MCP servers · Model Garden · RAG Engine · Sensitive Data Protection · Skill Registry · Workflow Builder (formerly Agent Designer)
