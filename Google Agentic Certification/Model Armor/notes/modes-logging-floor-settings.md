# Model Armor: Operating Modes, Logging and Floor Settings

**Module map:** How to roll Model Armor out safely (Inspect only vs Inspect and block), how to verify detections in Cloud Logging, and how floor settings set a baseline across projects. Covers **5.1**, **5.2**. Prerequisite: `overview-threats-filters.md`.

---

## 1. Operating modes [5.2]

**What/why:** Blocking real users with an untuned filter hurts legitimate traffic. Model Armor has two modes so you can observe before you enforce.

| | `Inspect only` | `Inspect and block` |
|---|---|---|
| Detection | Yes, logged to Cloud Logging | Yes, logged |
| Prompt to LLM | **Still sent** | **Not sent** if the prompt violates policy |
| Response to user | **Still returned** | **Not returned** if the response violates policy |
| User experience | No visible effect from Model Armor | Request denied; the application shows a message (wording is up to the client app) |
| Use cases | Policy testing/tuning, monitoring emerging threats, compliance auditing | Preventing harmful content, protecting sensitive data, stopping injection/jailbreak, blocking unsafe URLs, enforcing custom topics |

**Who actually blocks?** In `Inspect and block`, Model Armor **returns a block verdict**. The **calling service or integration point, the Policy Enforcement Point (PEP)**, is responsible for stopping further processing. (A PEP = the component that enforces a policy decision, e.g., the application or a proxy.)

```
Prompt ─► [PEP] ─► Model Armor ─► verdict
            │   Inspect only:       log, PEP lets prompt through
            └─► Inspect and block:  log, PEP stops the request (LLM never called)
```

**Takeaway:** Start in `Inspect only`, analyze logs, adjust, then switch to `Inspect and block`.

---

## 2. Verifying detections: Cloud Logging [5.2]

- **Cloud Logging must be enabled.** Without it, `Inspect only` yields no useful information.
- Find logs in Cloud Logging, filtered by service name `modelarmor.googleapis.com`; look for entries for the operations enabled in your template.
- The record to check is **`SanitizeOperationLogEntry`**.

> **Exam trap: model refusal vs Model Armor block.** In `Inspect only`, Model Armor does not block. If the response says "I can't fulfill this request", that is the **model's own built-in safety** refusing, a separate mechanism. To confirm Model Armor detected something, check `SanitizeOperationLogEntry` in Cloud Logging, not the response body.

Related: Model Armor does not store content by default. Cloud Logging is the only place content-related data persists, and only if you enable it (see `model-armor-coverage-limits.md`).

---

## 3. Floor settings vs templates [5.1]

**What/why:** Templates give each application flexibility, but an organization needs a minimum level of protection everywhere. **Floor settings** define **minimum requirements** for **all templates created at the project level** in the resource hierarchy.

| | Floor settings | Template |
|---|---|---|
| Purpose | Baseline protection across all AI apps | Per-application tuning |
| Scope | Project level (minimum requirements for templates in it) | One app/agent |
| Question to ask | "What must every app in this project have?" | "What does this chatbot need?" |

```
Project: floor settings (minimum)
   ├─ Template A (customer bot)   must meet floor
   ├─ Template B (HR assistant)   must meet floor
   └─ Template C (code agent)     must meet floor
```

> **Clarification (not in source):** Source says floor settings "define minimum requirements" for templates but does not state what happens when a template is weaker than the floor. Verify in the official Model Armor floor settings docs before relying on specifics such as inheritance from folder/organization.

**Takeaway:** Floor settings = project-wide minimum baseline; templates = per-app configuration above that baseline.

---

## 4. Worked example: safe rollout

**Scenario (*illustrative*):** A team deploys an agent and worries that legitimate customers will be blocked by RAI or injection filters.

1. Create a template with the needed filters. Mode = `Inspect only`. Enable Cloud Logging.
2. Run traffic; query logs for `SanitizeOperationLogEntry` under `modelarmor.googleapis.com`.
3. Too many legitimate prompts flagged → **raise** confidence level (more lenient). Misses → **lower** it (stricter).
4. When results are acceptable, switch to `Inspect and block`.

**Cross-check:** Step 1 requires logging (otherwise step 2 has nothing to analyze) ✔. Step 3 direction matches "lower = stricter" ✔. Step 4 follows the source's best practice (observe first) ✔. Blocking in step 4 is performed by the PEP acting on the verdict ✔.

---

## Common confusions

| Looks like | Difference |
|---|---|
| Inspect only vs Inspect and block | Only the second returns a block verdict; the first logs and lets traffic through |
| Model refusal vs Model Armor block | Refusal is the model's own safety; verify Model Armor via `SanitizeOperationLogEntry` |
| Model Armor vs PEP | Model Armor detects and returns a verdict; the PEP enforces it |
| Floor settings vs template | Floor = project-wide minimum; template = per-app configuration |

Sources: Model Armor overview doc, course transcript (see `sources/`).
