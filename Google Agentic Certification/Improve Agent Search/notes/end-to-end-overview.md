# Agent Search: End-to-End Picture (Who Does What, and When)

**Module map:** The "big picture" note tying together website metadata, schema, data store, serving controls, serving config and the query path. Covers **1.2** (partial **3.2**). Read this first, then use the detail notes: `metadata-ingestion-schema.md` (build/index side) and `serving-controls.md` (query side).

> **Gap:** Same as the detail notes: no multimodal ingestion, embeddings, reranking or Vector Search in this module's source.

---

## 1. The one-sentence model [1.2]

**Agent Search** (formerly Vertex AI Search) = a **data store** (the index, built by crawling your site) + a **search app/widget** (the thing users or agents call) + a **serving config** (the runtime settings of that app, where **serving controls** are attached).

Two separate timelines:

| Timeline | When it happens | Who acts | What happens |
|---|---|---|---|
| **A. Content/index time** | Before any query; repeats on re-crawl | Site owner / content team + search admin | Metadata is put on pages, crawled, and registered in the schema |
| **B. Query time** | Every user/agent search | End user or calling agent; Agent Search runs the pipeline | Request goes through the serving config; attached controls reshape results |

Admin configuration of controls (create, trigger, publish, attach) is done once, **between** A and B, but it only has effect at B.

**Takeaway:** Metadata + schema decide *what can be filtered or boosted* (A). Serving controls + serving config decide *whether and how it is filtered or boosted* (B).

---

## 2. Actors [1.2]

| Actor | Role | Touches |
|---|---|---|
| **Site/content owner** | Adds `<meta>` tags, PageMaps or schema.org JSON in `<head>`; keeps byline dates compliant | Web pages |
| **Crawler/indexer** (Google-managed) | Fetches pages, extracts metadata, infers byline dates, builds the index | Data store |
| **Search admin** | Marks custom fields `indexable` in the schema; creates controls; attaches them to the serving config | Schema, controls, serving config |
| **End user or calling agent** | Sends the query (optionally with a per-request client filter) | Search app/widget |
| **Serving config** | Runtime entry point; applies attached controls | Every request |

---

## 3. End-to-end flow [1.2]

```
TIMELINE A: BUILD & INDEX (before queries)
 Site owner                 Crawler/indexer              Search admin
 ┌──────────────┐  crawl   ┌───────────────────┐        ┌────────────────────────┐
 │ page <head>: │────────► │ extract metadata  │        │ Data store SCHEMA:     │
 │  <meta>      │          │ infer byline dates│───────►│ register custom fields │
 │  PageMaps    │          │ (published/modif.)│        │ mark them INDEXABLE    │
 │  schema.org  │          └─────────┬─────────┘        └───────────┬────────────┘
 └──────────────┘                    ▼                              │
                          ┌─────────────────────┐                   │
                          │ DATA STORE (index)  │◄──────────────────┘
                          └─────────────────────┘
                 byline dates: usable directly, no schema edit

CONFIGURE (admin, once; takes effect only at query time)
 Create control ─► optional trigger ─► action+scope ─► publish ─► ATTACH to serving config

TIMELINE B: QUERY
 User/agent ─► search app/widget ─► SERVING CONFIG ─► attached controls?
                                                         │ none: default ranking
                                                         ▼
                                   trigger matches (or no trigger)?
                                                         │ no: skip control
                                                         ▼
                              Filter (drop) | Boost/Bury (re-rank) | Synonyms (expand) | Promote (pin)
                                                         ▼
                                        adjusted results ─► user/agent
```

**Where each piece fits:**

| Piece | Timeline | Question it answers |
|---|---|---|
| `<meta>` / PageMaps / schema.org | A (on page) | What facts does the page expose? |
| Byline dates | A (automatic) | When was it published/updated? |
| Schema + `indexable` | A (admin) | Which facts may queries and controls use? |
| Serving control | Configure → B | What rule reshapes results? |
| Trigger | B | When does the rule fire? |
| Serving config | B | Which app's requests does the rule apply to? |

---

## 4. Dependency chain: why things silently fail [1.2]

| Break point | Symptom | Fix |
|---|---|---|
| Tag missing on page (or not re-crawled) | Control matches nothing | Add the tag; wait for/trigger re-crawl |
| Field not registered **or** not `indexable` | Filter/boost has no effect on that field | Register it and mark indexable |
| Control created/published but **not attached** | No change at all | Attach in the app's Controls tab |
| Trigger too narrow | Control fires only for some queries | Loosen/remove trigger |
| Filter too strict | Zero or too few results | Use Boost/Bury instead |

> **Clarification (not in source):** Whether a new schema field takes effect immediately or needs re-indexing is not stated in the course; verify in the official docs.

**Takeaway:** Four things must all be true for a control to work on a custom field: tag on page → crawled → field indexable → control attached.

---

## 5. Client filter vs serving control [1.2]

Both narrow results, but they live in different places.

| | **Client query filter** | **Filter serving control** |
|---|---|---|
| Set by | The caller (user/agent) in the request | Admin on the serving config |
| Scope | That request only | Every request through the config (optionally trigger-gated) |
| Use for | Per-user context (e.g., caller's department) | Global policy (e.g., always hide `severity = unknown`) |

Both can only use fields that are indexable (or byline dates).

---

## 6. Sample use case walkthrough (*illustrative*)

**Scenario:** *Contoso* runs an internal **Security Advisory** site. A **security agent** answers incident-response questions by calling an Agent Search app over the website data store. Problems: (1) unverified drafts appear first, (2) critical advisories are buried, (3) "VPN outage" lands on a 2021 post, (4) responders type "IR runbook" but pages say "incident response playbook".

### Step 1: Site owner (timeline A)
Adds to each advisory template:
```html
<head>
  <meta name="severity" content="critical">   <!-- critical | high | medium | unknown -->
  <meta name="department" content="security">
</head>
```
Bylines follow Google Search guidelines, so `datePublished` and `dateModified` are inferred.

### Step 2: Crawler indexes the site (timeline A)
Extracts `severity`, `department`; infers dates. At this point they are in the index but **not yet usable** for custom fields.

### Step 3: Admin edits the schema (timeline A)
Registers `severity` (and `department`), marks both **indexable**. Dates need nothing.

### Step 4: Admin builds controls (configure)

| # | Control (Control ID = name, immutable) | Type | Rule | Trigger |
|---|---|---|---|---|
| 1 | `exclude-unknown-severity` | Filter | exclude `severity = unknown` | none (always) |
| 2 | `boost-critical-advisories` | Boost | `severity = critical`, high positive slider | none |
| 3 | `bury-stale-advisories` | Bury | `dateModified` older than a chosen threshold | none |
| 4 | `synonym-ir-runbook` | Synonyms | "IR runbook" ≡ "incident response playbook" | n/a |
| 5 | `promote-vpn-status` | Promote | query terms "VPN outage" → status page URI | Query terms: "VPN outage" |

Each: **Publish immediately** on, then **attached to the production serving config** (Controls tab).

### Step 5: A query at runtime (timeline B)
Responder asks the agent: *"VPN outage"*.

```
agent ─► Agent Search app ─► serving config
   1 Filter  : drafts (unknown) removed            (no trigger: runs)
   2 Promote : trigger "VPN outage" matches → status page pinned on top
   3 Boost   : critical advisories move up
   4 Bury    : pre-threshold dateModified pages move down
   5 Synonyms: not relevant to this query, no effect
   ─► ranked results back to the agent
```
Second query *"IR runbook"*: Promote trigger does not match (skipped); Synonyms widens matching to "incident response playbook"; Filter, Boost, Bury still run.

### Cross-check

| Requirement | Satisfied by | Verified |
|---|---|---|
| Drafts never shown | Filter, no trigger, attached | ✔ hard removal, all queries |
| Critical first but medium/high still reachable | Boost (not Filter) | ✔ re-rank only |
| Old posts demoted, not deleted | Bury on `dateModified` (byline, no schema edit) | ✔ |
| "VPN outage" → status page only for that query | Promote with Query-terms trigger | ✔ |
| Jargon mismatch | Synonyms | ✔ |
| Custom fields usable | `severity` registered + indexable (Step 3) | ✔ |
| Anything takes effect | All attached to serving config | ✔ |

> **Clarification (not in source):** The order in which controls are applied in the diagram is a simplification for reading. The course does not specify evaluation order; do not rely on it.

---

## 7. Quick decision guide [1.2]

| Situation | Answer |
|---|---|
| Need freshness only | Byline dates; no tags, no schema edit |
| Need to filter/boost on department or severity | `<meta>` tag + schema `indexable` + control attached |
| Must never appear | Filter |
| Prefer but keep available | Boost/Bury |
| One query must hit one page | Promote with query-terms trigger |
| Users' words differ from content's | Synonyms |
| Rule depends on who is asking | Client query filter (caller passes it) |
| Rule applies to everyone | Filter serving control |

---

## Common confusions

| Looks like | Difference |
|---|---|
| Data store vs serving config | Data store = the index (content + schema); serving config = runtime settings of the app where controls attach |
| Schema vs serving control | Schema declares which fields are usable; controls use those fields to change results |
| Index time vs query time | Metadata/schema work happens on crawl/admin side; controls act per request |
| Who searches | End user or an agent calls the search app; the admin never searches, they configure |
| Created vs attached control | Only attached controls run |
| Trigger vs filter predicate | Trigger = *when* the control fires (query terms/time/regex); predicate = *which documents* it affects |

Sources: `Improve Agent Search/notes/metadata-ingestion-schema.md`, `serving-controls.md`, `sources/raw.md` (Ask Gemini capture). The Contoso scenario and control names are *illustrative*.
