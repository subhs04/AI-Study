# 01-improve-agent-search-findings.md

## Module Map
- **Scope:** End-to-end optimization of [Agent Search](https://partner.skills.google/paths/4531/course_templates/1323) relevance on Gemini Platform ([Gemini Enterprise](https://partner.skills.google/paths/4531) / Agent Platform) via structured web metadata ingestion, schema indexing, and dynamic serving controls.
- **Core Pipeline:** HTML `<head>` metadata injection (`<meta>`, PageMaps, `schema.org` JSON) & automatic byline date inference $\rightarrow$ Data store schema registration (`indexable` flags) $\rightarrow$ Serving control authoring (Boost/Bury, Filter, Synonyms, Promote) & trigger binding $\rightarrow$ Attachment to a `serving config`.
- **Covers:** `[1.2]`, `[3.2]` (partial)

---

## [1.2] Web Metadata Ingestion & Data Store Schema Indexing

**Takeaway:** High-precision [Agent Search](https://partner.skills.google/paths/4531/course_templates/1323) requires embedding structured metadata in HTML `<head>` tags and explicitly marking custom fields as `indexable` in the website content data store schema before queries or controls can evaluate them.

### Metadata Injection Mechanisms
Crawlers and indexing pipelines extract structured attributes from three supported HTML `<head>` mechanisms:
1. **HTML `<meta>` tags:** Key-value metadata attributes embedded directly in `<head>` (e.g., department classifications, content types, or Open Graph image properties for mobile thumbnail rendering).
2. **PageMaps:** Structured XML/JSON metadata blocks embedded in web pages for crawler extraction.
3. **`schema.org` JSON (`JSON-LD`):** Structured data objects placed inside a `<script>` tag within `<head>` to expose strongly typed entities and properties.

### Automatic Byline Dates
- **Extracted Properties:** `datePublished` (initial publication timestamp) and `dateModified` (most recent update timestamp).
- **Indexing Behavior:** Google automatically infers and indexes both fields during site crawling when pages follow Google Search byline date guidelines.
- **Schema Shortcut:** Unlike custom `<meta>` tags, inferred byline dates can be referenced directly in queries and serving controls without manually adding them to the data store schema.

### Data Store Schema Configuration
- **Registration Rule:** Custom metadata attributes (such as `department` or `severity`) are opaque to search filtering and boosting until registered in the **website content data store schema**.
- **Indexable Flag:** Each custom field must be explicitly marked **indexable** in the schema to unlock query-time filtering and serving control evaluation.

```text
[Web Page <head>]                     [Crawler & Indexer]                  [Website Data Store Schema]
 ├── <meta name="dept" ...>    ──┐
 ├── PageMaps                  ──┼──► Extract Structured Metadata ──────► Register Custom Fields
 ├── <script> schema.org JSON  ──┘                                         └── Flag as: INDEXABLE
 └── Byline Dates (Published/Mod) ──► Auto-Infer Temporal Fields ───────► Available Directly (No Schema Edit)
```

> **Clarification (not in source):** While the module refers to website content data stores and search apps/widgets on Gemini Platform, underlying filter expressions in [Agent Search](https://partner.skills.google/paths/4531/course_templates/1323) use standard Google Cloud Search filter syntax (e.g., `field: ANY("value")`) once fields are marked indexable.

---

## [1.2] Dynamic Serving Controls & Lifecycle Architecture

**Takeaway:** Serving controls override default [Agent Search](https://partner.skills.google/paths/4531/course_templates/1323) ranking and inclusion logic at query time, but remain completely inert unless explicitly bound to an active **serving config**.

### Control Types & Mechanics

| Control Type | Mechanism | Primary Use Case | Ranking / Inclusion Effect |
| :--- | :--- | :--- | :--- |
| **Boost / Bury** | Evaluates metadata filter criteria and applies a numeric magnitude via a **slider bar** | Elevate high-priority landing pages, recent docs, or user-department matches (e.g., HR/Finance); demote stale content | **Re-ranks** matching documents up (positive) or down (negative) without removing them |
| **Filter** | Enforces strict boolean metadata predicates on candidate documents | Restrict results to a specific content type (e.g., `docs`), severity (`severity = critical`), or trailing time window | **Hard inclusion/exclusion**; drops non-matching documents from the response |
| **Synonyms** | Maps equivalent query strings together | Bridge domain acronyms, internal jargon, and user phrasing | **Expands** query matching across mapped terms |
| **Promote** | Associates a specific query with a target URI | Pin canonical portals, runbooks, or forms to the top of search results | **Injects/pins** the specified link for matching queries |

### Control Lifecycle & Serving Config Scoping
1. **Creation & ID Assignment:** Define a **Control Name**, which immutably becomes the **Control ID**.
2. **Trigger Binding (Optional):**
   - **Unconditional (Default):** If no trigger is set, the control evaluates on every search request routed through its serving config.
   - **Query terms:** Fires only when specified terms appear in the search query.
   - **Time range:** Fires only when the request timestamp falls within a configured window.
   - **Query regex:** Fires when the query string matches a regular expression pattern.
3. **Action & Scope Definition:** Select target data store(s) (e.g., `advisories department`), define filter predicates (e.g., `severity = critical`, excluding `severity = unknown`), and set boost/bury slider values.
4. **Publishing & Attachment:** Enable **Publish immediately** under *Additional settings* and attach the control to one or more **serving configs** in the search app/widget **Controls** tab.

```text
[Incoming Search Request]
         │
         ▼
  ┌──────────────┐     Unattached Controls ──► [INERT / IGNORED]
  │Serving Config│
  └──────┬───────┘
         │ Evaluates Attached Controls
         ▼
  ┌─────────────────────┐   No Match
  │ Trigger Conditions  │ ───────────► [Bypass Control -> Default Ranking]
  │ (Terms/Time/Regex)  │
  └──────┬──────────────┘
         │ Match (or No Trigger Defined)
         ▼
  ┌─────────────────────┐
  │ Execute Action      │ ──► Filter (Drop) | Boost/Bury (Re-rank) | Synonyms | Promote (Pin URI)
  └──────┬──────────────┘
         │
         ▼
  [Adjusted Search Results Returned to Agent / Widget]
```

---

## [1.2] Decision Tables: Metadata & Serving Control Selection

### Metadata Injection Strategy

| Mechanism | Best When | Maintenance Overhead | Schema Edit Required | Latency / Scale Impact |
| :--- | :--- | :--- | :--- | :--- |
| **HTML `<meta>` tags** | Adding flat, page-level attributes (e.g., `department`, `severity`, `og:image`) to existing templates | **Low** — simple key-value tags in `<head>` | **Yes** — must register field & mark `indexable` | Zero query-time overhead; evaluated during crawl/index |
| **`schema.org` JSON** | Exposing rich, nested entities and typed attributes already needed for SEO/structured web standards | **Medium** — requires valid JSON-LD block in `<script>` | **Yes** — custom mapped fields require `indexable` flag | Zero query-time overhead; parsed at crawl time |
| **PageMaps** | Legacy or custom enterprise crawler setups already emitting structured PageMap blocks | **Medium** — custom XML/JSON structure | **Yes** — must register & mark `indexable` | Zero query-time overhead; parsed at crawl time |
| **Automatic Byline Dates** | Filtering or boosting by freshness (`datePublished`, `dateModified`) | **Lowest** — inferred automatically if page follows byline guidelines | **No** — natively indexed and immediately usable | Zero query-time overhead; native temporal index |

### Query-Time Filter vs. Serving Control Action

| Approach | Scope & Governance | Security / Access Control | Cost & Latency | When to Use |
| :--- | :--- | :--- | :--- | :--- |
| **Client/Agent Query Filter** (e.g., passing `department` filter in search API request) | Per-request dynamic filtering driven by caller/agent logic | Relies on caller passing accurate context; **not** a security boundary | Negligible overhead; reduces candidate set early | Dynamic per-user context (e.g., passing the logged-in user's `department` at query time) |
| **Filter Serving Control** | Centralized, admin-managed rule enforced across all traffic hitting a `serving config` | Enforces consistent tenant/app-wide policy regardless of client query parameters | Negligible overhead; enforced inside serving layer | Global exclusions (e.g., always hiding `severity = unknown` or non-`docs` pages) |
| **Boost / Bury Serving Control** | Centralized ranking adjustment via slider magnitude | Non-restrictive; demoted items remain retrievable if no better match exists | Negligible overhead; adjusts scoring during retrieval | Soft preferences (e.g., prioritizing recent updates or specific departments without zero-result risk) |

---

## [1.2] Worked Architectural Scenarios

### Scenario 1: Enterprise Security Advisory Search App (*illustrative*)
- **Context:** An internal security agent queries an `advisories department` website data store on Gemini Platform. Incident responders complain that critical advisories are buried beneath unverified (`severity = unknown`) drafts and older posts.
- **Architecture:**
  1. Embed `<meta name="severity" content="critical">` (or `unknown`) in advisory HTML `<head>` templates and ensure `datePublished` follows byline guidelines.
  2. In the website data store schema, add `severity` and mark it **indexable**.
  3. Create a **Filter Control** (`exclude-unknown-severity`) targeting `advisories department` to exclude `severity = unknown`, with **no trigger** (always active) and **Publish immediately** enabled.
  4. Create a **Boost/Bury Control** (`boost-critical-advisories`) targeting `advisories department` where `severity = critical`, setting a high positive boost slider value.
  5. Attach both controls to the production search app's **serving config**.
- **Cross-check:**
  - *Draft exclusion:* Guaranteed by the unconditional Filter Control removing `severity = unknown` before ranking.
  - *Critical prioritization:* Guaranteed by the Boost Control elevating `severity = critical` without hiding `medium`/`high` advisories.
  - *Freshness:* Supported natively via inferred `datePublished` without extra schema modifications.
  - *Activation:* Explicitly attaching both controls to the active serving config prevents silent no-op behavior.

### Scenario 2: Query-Scoped Documentation Promotion (*illustrative*)
- **Context:** Employees searching for `"expense policy"` in a Gemini Enterprise search widget frequently land on archived 2022 PDFs instead of the canonical HR portal page.
- **Architecture:**
  1. Configure a **Promote Control** (`promote-hr-expense-portal`) triggered by **Query terms** matching `"expense policy"`, pointing to the canonical HR portal URI.
  2. Configure a **Boost/Bury Control** (`bury-stale-policies`) using the natively indexed `dateModified` byline field to demote documents older than a threshold date.
  3. Bind both controls to the widget's **serving config**.
- **Cross-check:**
  - *Exact query redirection:* The Promote Control pins the canonical URI only when `"expense policy"` is queried, avoiding clutter on unrelated searches.
  - *Global staleness reduction:* The Bury Control demotes outdated pages across all queries without deleting historical records from the index.

---

## Common Confusions

| Term / Feature A | Term / Feature B | Key Architectural Difference |
| :--- | :--- | :--- |
| **Boost / Bury Control** | **Filter Control** | **Boost/Bury** alters relative ranking order using a slider magnitude (demoted docs can still appear); **Filter** strictly removes non-matching documents from results. |
| **Custom `<meta>` / `schema.org` fields** | **Byline Dates (`datePublished`, `dateModified`)** | Custom metadata fields **must** be added to the data store schema and marked `indexable`; byline dates are **automatically inferred** and usable without schema edits. |
| **Serving Control Creation** | **Serving Config Attachment** | Creating and publishing a control only defines the rule; it has **zero runtime effect** until attached to a specific **serving config**. |
| **Promote Control** | **Synonyms Control** | **Promote** pins a specific target URI/link for a matching query; **Synonyms** expands query matching by equating distinct search strings. |
| **Client Query Filter** | **Serving Control Filter** | **Client Query Filters** are passed dynamically per API/agent request; **Serving Control Filters** are centrally configured on the serving config and apply automatically based on optional triggers. |

> **Exam Gap (`[1.2]` & `[3.2]`):** For `[1.2]`, this module covers website metadata enrichment (`<meta>`, PageMaps, `schema.org` JSON, byline dates) and [Agent Search](https://partner.skills.google/paths/4531/course_templates/1323) serving controls, but does **not** cover multimodal data ingestion (video, audio, images) or broader [Gemini Enterprise](https://partner.skills.google/paths/4531) / Workflow Builder orchestration. For `[3.2]`, it touches search retrieval tuning via metadata indexing, but omits embeddings, vector similarity, reranking models, Vector Search, RAG Engine, Agent Identity, Agent Registry, and Google Cloud MCP servers.