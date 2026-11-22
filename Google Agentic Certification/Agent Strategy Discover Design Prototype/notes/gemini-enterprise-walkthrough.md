# Gemini Enterprise: Setup, Data Stores, Pre-built Agents, NotebookLM

**Module map:** The hands-on demo reconstructed as concepts: create a Gemini Enterprise app, configure workforce identity, connect data stores, use search/chat controls, pre-built agents (Idea Generation, Deep Research) and NotebookLM. Covers **1.2** (connecting enterprise data; multimodal prompts), partial **1.1**. Prerequisite: `prototyping-tools-and-ucp.md`. Related: `Improve Agent Search/notes/` (Agent Search ingestion and serving).

---

## 1. What Gemini Enterprise is and how an app is set up [1.2]

**What/why:** Gemini Enterprise is the organization-wide AI workspace. An **app** is the instance your workforce uses; it needs (a) an **identity provider** so only your workforce signs in, and (b) **data stores** so answers are grounded in company data (internal) and optionally external sources.

```
Cloud Console ─► Gemini Enterprise ─► Create app (name)
                                        ├─► Set up identity  (Google Identity → confirm Workforce Identity)
                                        ├─► Connected data stores (Drive, Calendar, Cloud Storage ...)
                                        └─► Share app via unique URL with workforce
```

| Step | Demo detail |
|---|---|
| Create | Console → search "Gemini Enterprise" → **Create your first app** → name (e.g., "Cymbal Foods Marketing Team") |
| Identity | **Setup identity** → choose **Google Identity** as provider → **Confirm Workforce Identity** (required *before* sharing with workforce) |
| Share | Unique URL on the Overview page |

**Takeaway:** App creation → identity setup → data-store connections → share by URL.

---

## 2. Connecting data stores [1.2]

| Data store | Key choices in demo | Why |
|---|---|---|
| **Google Drive** | Sync **all of the organization's shared drives** | Documents, collaboration content |
| **Google Calendar** | Connector with a name | Scheduling context |
| **Cloud Storage** | **Data type = Documents** (unstructured: PDFs, HTML, TXT); **sync frequency = One time** (files ingested once); browse to choose the **bucket** | Static document corpus |

Rules of thumb drawn from the demo (*cross-check*):
- Unstructured files (PDF/HTML/TXT) → data type **Documents**.
- Files that do not change → **one-time** sync (a changing source would need a recurring sync; *clarification, not in source: the demo only shows one-time; verify other frequency options in docs*).
- Result: three data stores connected; the app now answers over internal data.

> **Gap:** Exam 1.2 also expects ingesting **unstructured multimodal data (video, audio, images)** and securely connecting proprietary sources via **Agent Search**. This demo only evidences image (chart) understanding and Cloud Storage documents. See `Improve Agent Search` notes for ingestion/schema.

---

## 3. Using the workspace: search scope and multimodal prompts [1.2]

- Default prompt ("Give me an update on our latest marketing data") searches **internal and external** sources and returns a **context-aware** answer.
- **Enhance your conversation** button changes search parameters: restrict to **internal sources only**; generate **videos with Veo**, **images with Nano Banana**, and more.
- **Multimodal prompt:** "Summarize the customer sentiment pie chart" → searches internal sources and answers from the **image** of the chart.

**Takeaway:** Search scope is controllable per conversation (internal-only vs internal+external); multimodal inputs are supported.

---

## 4. Pre-built agents (low-code, no build required) [1.1]

| Agent | Use when | Flow in demo |
|---|---|---|
| **Idea Generation** | A *team of agents* should **plan, generate and evaluate** ideas on a topic | Prompt → agent drafts a **plan** → you review → **Start session** → multiple ideas generated |
| **Deep Research** | In-depth analysis, comprehensive reports on complex topics | Prompt → agent drafts a **research plan** → you review/modify → **Start research** → full **report + audio summary** |

Common pattern: **plan → human review/modify → execute** (a light HITL step; ties to `design-best-practices-cuj.md` rule 3).

---

## 5. NotebookLM

**What/why:** Source-grounded notebook: you upload content and it answers *from those sources* and transforms them into other formats.

| Feature | Detail |
|---|---|
| Sources | Google Docs, website URLs, YouTube videos, copied text, uploaded local files |
| Chat | Context-aware answers from your sources (demo: "Women aged 25-55 interested in baking and desserts in Oakland, California") |
| Notes | Save responses as notes for later |
| **Studio tab** | Convert sources to **audio, video overviews, mind maps, reports** (demo: 5-minute video overview "Marketing by the Numbers") |

---

## Common confusions

| Looks like | Difference |
|---|---|
| Gemini Enterprise app vs data store | App = the workspace; data store = a connected source feeding it |
| Workforce identity vs data-store access | Identity = who may sign in to the app; data stores = what content it can search |
| Deep Research vs Idea Generation | Research = evidence report; Idea Generation = brainstorm, then generate/evaluate ideas |
| NotebookLM vs Gemini Enterprise chat | NotebookLM answers strictly from the sources you upload; chat searches connected data stores (and optionally external sources) |
| Agent Designer / Workflow Builder vs pre-built agents | Pre-built agents are ready-made; Workflow Builder (formerly Agent Designer) is for configuring your own (not in this course) |

Sources: Gemini Enterprise walkthrough in `sources/ProtototypeModule.md`. (Demo company name appears as "Symbol/Cymbal" in capture; Cymbal assumed.)
