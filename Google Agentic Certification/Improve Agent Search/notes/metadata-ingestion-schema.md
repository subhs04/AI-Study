# Agent Search: Web Metadata Ingestion and Schema Indexing

**Module map:** How a website's structured metadata gets into **Agent Search** (formerly Vertex AI Search) and becomes usable for filtering and ranking. Covers **1.2** (connect enterprise data), touches **3.2** (retrieval quality tuning only). Next note: `serving-controls.md`.

> **Gap:** 1.2 also expects multimodal ingestion (video, audio, images) and Gemini Enterprise connections; not in this module's source. 3.2 expects embeddings, similarity scoring, reranking, Vector Search/Agent Retrieval, Agent Identity, Agent Registry, MCP servers; none are covered here.

---

## 1. Why metadata matters [1.2]

**Terms first:**
- **Metadata:** data *about* a page (its department, type, severity, publication date), as opposed to the page's visible text.
- **Data store:** the indexed collection Agent Search queries; here a **website content data store** (built by crawling a site).
- **Schema (of the data store):** the list of fields the index knows about and what you can do with each.
- **Indexable field:** a field flagged so queries and serving controls can filter/boost on it.

**Why:** Plain text matching cannot express "only critical advisories" or "prefer HR pages". If the page publishes structured metadata, search can filter and rank on it. The metadata must be (1) present on the page, then (2) registered in the schema.

**Takeaway:** Metadata on the page + `indexable` in the schema = usable at query time.

---

## 2. Three ways to put metadata in the page `<head>` [1.2]

| Mechanism | What it is | Example use from source |
|---|---|---|
| **HTML `<meta>` tags** | Key-value tags in `<head>` | Department classification, content type, Open Graph image (thumbnail on mobile) |
| **PageMaps** | Structured metadata blocks embedded in the page for the crawler | Structured attributes for crawler extraction |
| **`schema.org` JSON (JSON-LD)** | Typed structured data in a `<script>` tag within `<head>` | Typed entities and properties |

```
Web page <head>                    Crawler / indexer              Data store schema
 ├ <meta name="department"...>  ┐
 ├ PageMaps                     ├─► extract structured metadata ─► register custom field
 └ <script> schema.org JSON     ┘                                   └─ mark INDEXABLE
 byline dates (published/modified) ─► inferred automatically ─────► usable directly
```

---

## 3. Automatic byline dates [1.2]

- Two fields: `datePublished` (first published) and `dateModified` (last updated).
- If pages follow **Google Search byline date guidelines**, Google **infers and indexes** them during crawling.
- Unlike custom fields, they can be used in queries and serving controls **without being added to the schema**.

**Takeaway:** Freshness filtering/boosting is the cheapest metadata use: no tags and no schema edit, just compliant bylines.

---

## 4. Schema registration [1.2]

- Custom attributes (e.g., `department`, `severity`) are **opaque** to filtering and boosting until registered in the website content data store schema.
- Each must be explicitly marked **indexable**.

> **Clarification (not in source):** The Gemini-made capture states that filter expressions use Google Cloud Search filter syntax (e.g., `field: ANY("value")`). The course material does not show this; verify in the official Agent Search docs before relying on it.

---

## 5. Which metadata source to use

| Need | Use | Schema edit? |
|---|---|---|
| Flat page-level attribute (department, severity) | `<meta>` tag | **Yes** |
| Rich typed entities | `schema.org` JSON-LD | **Yes** (custom fields) |
| Existing crawler setup already emitting structured blocks | PageMaps | **Yes** |
| Freshness | Byline dates | **No** |

> The "maintenance overhead" and "latency" columns in the capture's version of this table are not in the course material and are omitted here.

---

## 6. Worked example (*illustrative*)

**Goal:** Search over advisory pages must be able to filter on severity and prefer recent pages.
1. Add `<meta name="severity" content="critical">` to the advisory page template.
2. Make sure bylines follow Search guidelines.
3. Register `severity` in the schema and mark it indexable.

**Cross-check:** `severity` is custom → needs schema + indexable (step 3) ✔. Dates are byline → no schema step needed ✔. Skipping step 3 would leave `severity` unusable for filters and boosts ✔.

---

## Common confusions

| Looks like | Difference |
|---|---|
| Custom `<meta>`/schema.org field vs byline date | Custom fields need schema registration and `indexable`; byline dates are inferred |
| Metadata on the page vs registered in the schema | Both are required; page tag alone is not queryable |
| PageMaps vs schema.org JSON | Both structured-data mechanisms; JSON-LD is in a `<script>` tag |
| Agent Search vs Vertex AI Search | Same product, renamed |

Sources: `Improve Agent Search/sources/raw.md` (an Ask Gemini capture; re-capture the raw course pages if you want a faithful source).
