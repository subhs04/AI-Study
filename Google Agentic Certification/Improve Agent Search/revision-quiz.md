# Improve Agent Search — Revision Quiz

**12 questions** across 3 notes · Tap **Show Answer** on GitHub Mobile to check your recall.

---

## 1. End-to-End Overview ([note](notes/end-to-end-overview.md))

### Q1 · Recall
What are the three core components of **Agent Search** (formerly Vertex AI Search), and what two timelines do they span?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Components:** A **data store** (index built by crawling your site), a **search app/widget** (called by users or agents), and a **serving config** (runtime settings where serving controls attach).
- **Timeline A (Content/index time):** Metadata is placed on pages, crawled, and registered in the schema before any query.
- **Timeline B (Query time):** Each request passes through the serving config, and attached controls reshape results.
**Why it matters:** Metadata + schema decide *what can be filtered or boosted* (A), while serving controls + serving config decide *whether and how* it happens at runtime (B).
**Ref:** `[1.2]` · [end-to-end-overview.md](notes/end-to-end-overview.md)

</details>

### Q2 · Compare
**Client query filter** vs. **Filter serving control** — how do they differ in who sets them, scope, and typical use?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Client query filter:** Set by the **caller** (user/agent) in the request; applies to **that request only**; used for per-user context (e.g., caller's department).
- **Filter serving control:** Set by the **admin** on the serving config; applies to **every request** through the config (optionally trigger-gated); used for global policy (e.g., always hide `severity = unknown`).
**Why it matters:** Both can only use fields that are `indexable` (or byline dates), so neither works without proper schema setup.
**Ref:** `[1.2]` · [end-to-end-overview.md](notes/end-to-end-overview.md)

</details>

### Q3 · Recall
What four links in the dependency chain must **all** be true for a serving control to work on a custom field?

<details>
<summary>Show Answer</summary>

**Answer:** Tag on page → crawled into the data store → field registered in schema and marked **`indexable`** → control **attached** to the serving config.
**Why it matters:** Breaking any single link causes the control to silently have no effect.
**Ref:** `[1.2]` · [end-to-end-overview.md](notes/end-to-end-overview.md)

</details>

### Q4 · Spot the Trap (True / False)
**True or False:** A **trigger** on a serving control defines *which documents* the control affects, while a **filter predicate** defines *when* the control fires.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** It is the reverse: a **trigger** defines *when* the control fires (query terms, time range, or query regex), while a **filter predicate** defines *which documents* it affects.
**Why it matters:** Confusing triggers and predicates leads to controls firing on the wrong queries or matching the wrong pages.
**Ref:** `[1.2]` · [end-to-end-overview.md](notes/end-to-end-overview.md)

</details>

---

## 2. Web Metadata Ingestion and Schema Indexing ([note](notes/metadata-ingestion-schema.md))

### Q5 · Recall
What are the three mechanisms for putting structured metadata into a web page's `<head>`, and where does `schema.org` JSON-LD live?

<details>
<summary>Show Answer</summary>

**Answer:**
- **HTML `<meta>` tags** (key-value tags in `<head>`).
- **PageMaps** (structured metadata blocks embedded for the crawler).
- **`schema.org` JSON (JSON-LD)** (typed structured data placed inside a `<script>` tag within `<head>`).
**Why it matters:** Plain text matching cannot express structured rules like "only critical advisories"—structured metadata in `<head>` makes them possible.
**Ref:** `[1.2]` · [metadata-ingestion-schema.md](notes/metadata-ingestion-schema.md)

</details>

### Q6 · Compare
**Custom `<meta>` / `schema.org` fields** vs. **automatic byline dates (`datePublished`, `dateModified`)** — how do they differ in schema requirements?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Custom fields:** Must be explicitly registered in the website content data store schema and marked **`indexable`**.
- **Byline dates:** Inferred and indexed automatically during crawling (if pages follow Google Search byline date guidelines) and usable in queries and controls **without** editing the schema.
**Why it matters:** Byline dates are the lowest-effort way to support freshness filtering or boosting.
**Ref:** `[1.2]` · [metadata-ingestion-schema.md](notes/metadata-ingestion-schema.md)

</details>

### Q7 · Spot the Trap (True / False)
**True or False:** Once you add `<meta name="department" content="security">` to your pages and the crawler indexes the site, queries and serving controls can immediately filter on `department`.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Custom attributes remain **opaque** to filtering and boosting until an admin registers them in the data store schema and marks them **`indexable`**.
**Why it matters:** Page tags alone are not queryable; both page metadata and schema registration are required.
**Ref:** `[1.2]` · [metadata-ingestion-schema.md](notes/metadata-ingestion-schema.md)

</details>

### Q8 · 1-Line Trigger → Choice
You need freshness filtering/boosting on web pages with minimal setup and **no schema changes**. Which metadata source should you use?

<details>
<summary>Show Answer</summary>

**Answer:** Compliant **byline dates** (`datePublished` and `dateModified`), which Google infers automatically during crawling.
**Why it matters:** Avoids unnecessary `<meta>` tag maintenance and schema edits when freshness is the only requirement.
**Ref:** `[1.2]` · [metadata-ingestion-schema.md](notes/metadata-ingestion-schema.md)

</details>

---

## 3. Serving Controls ([note](notes/serving-controls.md))

### Q9 · Compare
**Filter** vs. **Boost / Bury** serving controls — how do they differ in how they treat results?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Filter:** Uses a strict metadata predicate for **hard include/exclude**—non-matching documents are removed from results.
- **Boost / Bury:** Uses metadata filter criteria plus a numeric magnitude via a **slider** to **re-rank** matches up or down—documents remain in the results.
**Why it matters:** Use **Boost/Bury** when you want to prefer or demote pages without risking zero results; use **Filter** only when results must never appear.
**Ref:** `[1.2]` · [serving-controls.md](notes/serving-controls.md)

</details>

### Q10 · 1-Line Trigger → Choice
Which serving control type fits each need?
1. Responders search `"IR runbook"` while pages say `"incident response playbook"`.
2. Searching `"expense policy"` must pin a canonical HR portal URI at the top.

<details>
<summary>Show Answer</summary>

**Answer:**
1. **Synonyms** — maps equivalent query strings to expand matching across terms.
2. **Promote** — associates a query with a target URI to pin that link in results for matching queries.
**Why it matters:** **Synonyms** broadens which terms match; **Promote** pins one canonical destination for a specific query.
**Ref:** `[1.2]` · [serving-controls.md](notes/serving-controls.md)

</details>

### Q11 · Recall
What are the **four trigger options** when configuring a serving control, and what is the default behavior?

<details>
<summary>Show Answer</summary>

**Answer:**
- **None (default):** Runs on **every request** through the serving config.
- **Query terms:** Fires only when the query contains those terms.
- **Time range:** Fires only when the request time falls within a window.
- **Query regex:** Fires when the query matches a pattern.
**Why it matters:** Omitting a trigger makes the control global to the serving config, while triggers scope it to specific queries or times.
**Ref:** `[1.2]` · [serving-controls.md](notes/serving-controls.md)

</details>

### Q12 · Spot the Trap (True / False)
**True or False:** After creating a serving control, you can rename its Control ID later, and enabling **Publish immediately** is enough for it to start affecting search results.

<details>
<summary>Show Answer</summary>

**Answer:** **False** on both counts:
- The **Control Name** you enter becomes the **Control ID** and **cannot change later**.
- A published control still does **nothing** until it is **attached** to one or more serving configs in the app/widget **Controls** tab.
**Why it matters:** Forgetting to attach a published control is a classic reason a configured rule produces zero change at query time.
**Ref:** `[1.2]` · [serving-controls.md](notes/serving-controls.md)

</details>
