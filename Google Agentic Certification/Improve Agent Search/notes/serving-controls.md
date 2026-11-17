# Agent Search: Serving Controls

**Module map:** How to change Agent Search results at query time with serving controls (Boost/Bury, Filter, Synonyms, Promote), triggers, and attachment to a serving config. Covers **1.2**; partial relevance to **3.2** (retrieval tuning). Prerequisite: `metadata-ingestion-schema.md`.

> **Gap:** No rerankers, embeddings or vector search here (3.2). See the gap box in the prerequisite note.

---

## 1. What serving controls are [1.2]

**What/why:** After content is indexed, you often need to steer results without re-crawling: hide drafts, favor recent pages, pin a canonical page. A **serving control** is an admin-defined rule applied at **query time**. A **serving config** is the runtime configuration of a search app/widget that a request goes through; controls only take effect when **attached** to it.

**Takeaway:** A control that is created but not attached to a serving config does nothing.

---

## 2. The four control types [1.2]

| Control | Mechanism | Effect | Use case |
|---|---|---|---|
| **Boost / Bury** | Metadata filter criteria + numeric magnitude via a **slider** | **Re-ranks** matches up (boost) or down (bury); docs stay in results | Prioritize HR/Finance pages, recent docs; demote stale content |
| **Filter** | Strict metadata predicate | **Hard include/exclude**; non-matching docs are removed | Only `docs` type, `severity = critical`, trailing time window |
| **Synonyms** | Maps equivalent query strings | **Expands** matching across terms | Acronyms, internal jargon |
| **Promote** | Associates a query with a target URI | **Pins** that link in results for matching queries | Canonical portals, runbooks, forms |

---

## 3. Control lifecycle [1.2]

1. **Create:** the **Control Name** you enter becomes the **Control ID** and cannot change later.
2. **Trigger (optional):**
   - none (default): runs on every request through the serving config
   - **Query terms:** only when the query contains those terms
   - **Time range:** only when the request time falls in a window
   - **Query regex:** when the query matches a pattern
3. **Action and scope:** pick target data store(s), set filter predicates (e.g., `severity = critical`) and boost/bury slider value.
4. **Publish and attach:** enable **Publish immediately** (Additional settings) and attach to one or more serving configs in the app/widget **Controls** tab.

```
Search request ─► Serving config ─► attached controls? ─ no ─► default ranking
                                         │ yes
                                  trigger matches (or none)? ─ no ─► skip control
                                         │ yes
                          Filter (drop) | Boost/Bury (re-rank) | Synonyms (expand) | Promote (pin)
                                         ▼
                              adjusted results returned
```

---

## 4. Filter vs Boost: when to use which [1.2]

| Need | Choose | Why |
|---|---|---|
| Result must never appear (e.g., `severity = unknown`) | **Filter** | Hard removal |
| Prefer some results but keep others available | **Boost/Bury** | Soft re-rank, no zero-result risk |
| User phrase differs from content wording | **Synonyms** | Expands query matching |
| Specific query must land on one canonical page | **Promote** | Pins the URI |

> **Clarification (not in source):** The capture claims a per-request client query filter "is not a security boundary" because the caller supplies it. That reasoning is plausible (it is caller-controlled versus admin-controlled serving controls) but not stated in the course. Verify before using it as an exam rule.

---

## 5. Worked examples (*illustrative*)

**A. Security advisories:** Responders see unverified drafts first.
- Filter control `exclude-unknown-severity`: exclude `severity = unknown`, no trigger, publish immediately.
- Boost control `boost-critical-advisories`: boost `severity = critical`.
- Attach both to the serving config.

Cross-check: filter removes drafts for all queries (no trigger) ✔; boost raises critical without hiding medium/high ✔; `severity` must be indexable (prior note) ✔; unattached controls would be inert ✔.

**B. "expense policy" lands on 2022 PDFs:**
- Promote control triggered by query terms "expense policy" → canonical HR page URI.
- Bury control on `dateModified` older than a threshold.
- Attach both.

Cross-check: Promote fires only for that query, no clutter elsewhere ✔; bury demotes stale pages without deleting them ✔; `dateModified` is a byline date, no schema edit ✔.

---

## Common confusions

| Looks like | Difference |
|---|---|
| Boost/Bury vs Filter | Re-rank (still retrievable) vs hard removal |
| Promote vs Synonyms | Pin a URI for a query vs expand which terms match |
| Creating/publishing a control vs attaching it | Only attachment to a serving config makes it run |
| Control Name vs Control ID | The name becomes the ID and is immutable |
| Trigger vs no trigger | No trigger = every request; query terms/time range/regex narrow it |
| Client query filter vs Filter control | Per-request from the caller vs admin-configured on the serving config |

Sources: `Improve Agent Search/sources/raw.md` (an Ask Gemini capture).
