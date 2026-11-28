# Model Armor — Revision Quiz

**15 active-recall questions** covering all 3 notes in study order.
Tap **Show Answer** on GitHub Mobile to check your recall.

---

## 1. Overview, Threats, and Filters ([note](notes/overview-threats-filters.md))

**Q1. Compare:** What is the core difference between **prompt injection** and **jailbreaking**, and how many Model Armor filter families cover them?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Prompt injection:** abuses what the system can *do* (makes the model perform unauthorized actions or extract confidential data).
- **Jailbreaking:** tries to override/bypass the **safety restrictions** built into the model.
- Model Armor covers both with a **single** filter family: **Prompt injection and jailbreak detection**.

**Why it matters:** Both are distinct threat intents, yet configured under one filter family in a Model Armor template.
**Ref:** `[5.2]` · [overview-threats-filters.md](notes/overview-threats-filters.md)

</details>

**Q2. Trigger -> Choice:** You need to add Model Armor to an **existing AI workload** without modifying its application code. Which deployment approach fits?

<details>
<summary>Show Answer</summary>

**Answer:** **Network proxy enforcement** (the other two approaches are calling the Model Armor API from app code, and Agent Platform / Vertex AI integration).

**Why it matters:** Exam scenarios that say "retrofit existing workloads without code changes" map to network proxy enforcement.
**Ref:** `[5.1][5.2]` · [overview-threats-filters.md](notes/overview-threats-filters.md)

</details>

**Q3. Recall:** Name the **five** filter families you can enable in a Model Armor template.

<details>
<summary>Show Answer</summary>

**Answer:**
1. **Prompt injection and jailbreak detection**
2. **Sensitive Data Protection (SDP)** (using selected infoTypes)
3. **Responsible AI (RAI) safety categories** (e.g., hate speech, harassment, dangerous content)
4. **Malicious URL detection**
5. **Custom topics**

**Why it matters:** Data in any disabled filter is not sent to its backing service.
**Ref:** `[5.2]` · [overview-threats-filters.md](notes/overview-threats-filters.md)

</details>

**Q4. Spot the Trap (True / False):** Raising the RAI **confidence level** threshold makes Model Armor stricter and catches more borderline harmful content.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Raising the confidence threshold makes Model Armor **more lenient** (more risk of misses). **Lowering** the threshold makes it **stricter** (more false positives).

**Why it matters:** Directionality of confidence thresholds is a classic exam trap—lower = stricter, higher = more lenient.
**Ref:** `[5.1]` · [overview-threats-filters.md](notes/overview-threats-filters.md)

</details>

**Q5. Trigger -> Choice:** A user sends an SSN in a prompt, or a model tries to return 100 valid credit card numbers. Which filter catches both **input and output**, and what external service's categories power it?

<details>
<summary>Show Answer</summary>

**Answer:** The **Sensitive Data Protection (SDP)** filter, powered by **Sensitive Data Protection infoTypes** that you select.

**Why it matters:** Model Armor relies on SDP infoTypes for sensitive-data screening on both prompts and responses, and language support depends on the infoTypes chosen.
**Ref:** `[5.2]` · [overview-threats-filters.md](notes/overview-threats-filters.md)

</details>

---

## 2. Operating Modes, Logging, and Floor Settings ([note](notes/modes-logging-floor-settings.md))

**Q6. Compare:** In `Inspect only` vs. `Inspect and block`, when is the prompt still sent to the LLM, and which component actually stops the request on a block verdict?

<details>
<summary>Show Answer</summary>

**Answer:**
- **`Inspect only`:** detections are logged, but the prompt is **still sent** to the LLM and the response is **still returned**.
- **`Inspect and block`:** Model Armor returns a block verdict, and the **Policy Enforcement Point (PEP)** (the calling app/service or proxy) stops further processing so the LLM is not called.

**Why it matters:** Model Armor returns the verdict; the PEP enforces the block.
**Ref:** `[5.2]` · [modes-logging-floor-settings.md](notes/modes-logging-floor-settings.md)

</details>

**Q7. Recall:** To verify Model Armor detections in Cloud Logging, which **service name** do you filter by, and what is the exact **log entry** type?

<details>
<summary>Show Answer</summary>

**Answer:** Service name **`modelarmor.googleapis.com`**; log entry **`SanitizeOperationLogEntry`**.

**Why it matters:** Without Cloud Logging enabled, `Inspect only` yields no actionable signal for tuning.
**Ref:** `[5.2]` · [modes-logging-floor-settings.md](notes/modes-logging-floor-settings.md)

</details>

**Q8. Spot the Trap (True / False):** While testing in `Inspect only` mode, seeing the response `"I can't fulfill this request"` proves that Model Armor's RAI filter blocked the prompt.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** `Inspect only` never blocks traffic. That response comes from the **model's own built-in safety** refusing; confirm Model Armor detections via `SanitizeOperationLogEntry` in Cloud Logging.

**Why it matters:** Distinguishing a model refusal from a Model Armor block prevents misdiagnosing filter behavior during rollout.
**Ref:** `[5.2]` · [modes-logging-floor-settings.md](notes/modes-logging-floor-settings.md)

</details>

**Q9. Compare:** What is the difference in scope and purpose between **floor settings** and **templates**?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Floor settings:** **project-level** minimum baseline requirements that all templates created in that project must meet.
- **Templates:** **per-application/agent** security settings (filters, thresholds, mode) tuned above that baseline.

**Why it matters:** Use floor settings for organization-wide minimum guardrails and templates for per-app customization.
**Ref:** `[5.1]` · [modes-logging-floor-settings.md](notes/modes-logging-floor-settings.md)

</details>

**Q10. Trigger -> Choice:** During an `Inspect only` rollout, `SanitizeOperationLogEntry` shows too many legitimate user prompts flagged by RAI filters. What should you do before switching to `Inspect and block`?

<details>
<summary>Show Answer</summary>

**Answer:** **Raise** the RAI confidence level threshold (making it more lenient), verify the updated logs in `Inspect only`, and then switch to `Inspect and block`.

**Why it matters:** Observing first in `Inspect only` and tuning thresholds avoids breaking legitimate production traffic.
**Ref:** `[5.1][5.2]` · [modes-logging-floor-settings.md](notes/modes-logging-floor-settings.md)

</details>

---

## 3. Coverage, Limits, Data Handling, and Pricing ([note](notes/coverage-limits-data-handling.md))

**Q11. Recall:** How does Model Armor handle a document that is **> 4 MB** versus one that is **< 69 bytes**?

<details>
<summary>Show Answer</summary>

**Answer:**
- **> 4 MB:** **skipped** (not scanned and not charged; no verdict produced).
- **< 69 bytes:** rejected with **`InvalidDocumentInputException`**.

**Why it matters:** Treating a "no block" on a > 4 MB file as "scanned and clean" is a design bug—oversized files are silently skipped.
**Ref:** `[5.2]` · [coverage-limits-data-handling.md](notes/coverage-limits-data-handling.md)

</details>

**Q12. Recall:** State the key constraints for Model Armor **image screening (Preview)**: supported formats, max size/count, regional availability, and combined text+image support.

<details>
<summary>Show Answer</summary>

**Answer:**
- **Formats & size:** JPEG, PNG, BMP, **≤ 4 MB** each (images embedded in files are not screened).
- **Count & text:** **One image per request**; images alongside text are not screened via `SanitizeUserPrompt` / `SanitizeModelResponse`.
- **Regions:** **`us` and `eu` multi-regions only** (elsewhere `invocation_result` = `FAILURE`).

**Why it matters:** Multi-modal or non-US/EU deployments hit hard Preview limits if you assume image screening works everywhere.
**Ref:** `[5.2]` · [coverage-limits-data-handling.md](notes/coverage-limits-data-handling.md)

</details>

**Q13. Spot the Trap (True / False):** Because Model Armor catches malicious prompts disguised inside a code snippet variable, it also decodes Base64 and URL-encoded payloads before scanning.

<details>
<summary>Show Answer</summary>

**Answer:** **False.** Model Armor detects plain code-disguised text, but it **does not decode** Base64, hexadecimal, URL encoding, or ciphertext—encoded payloads are opaque to it.

**Why it matters:** Upstream normalization/decoding is needed if attackers can submit encoded inputs.
**Ref:** `[5.2]` · [coverage-limits-data-handling.md](notes/coverage-limits-data-handling.md)

</details>

**Q14. Trigger -> Choice:** An attacker splits a malicious instruction across three consecutive chat turns. Why does Model Armor fail to see the combined attack?

<details>
<summary>Show Answer</summary>

**Answer:** Model Armor is **single-turn only**—it inspects each prompt or response independently with no conversation history or multi-turn context.

**Why it matters:** Multi-turn attacks require stateful checks at the agent/application layer, not just single-turn filtering.
**Ref:** `[5.2]` · [coverage-limits-data-handling.md](notes/coverage-limits-data-handling.md)

</details>

**Q15. Compare:** Where does prompt/response content persist during Model Armor processing, and what data do **disabled** filters receive?

<details>
<summary>Show Answer</summary>

**Answer:**
- **Model Armor:** **stateless** (in-memory processing only, TLS 1.2+ in transit; no durable retention by default).
- **Cloud Logging:** the **only** storage path for content-related data, and only if **you enable** it.
- **Disabled filters:** receive **no data** from Model Armor.

**Why it matters:** Addresses compliance/data-minimization concerns—Model Armor does not become a hidden prompt data store unless you opt into Cloud Logging.
**Ref:** `[5.2]` · [coverage-limits-data-handling.md](notes/coverage-limits-data-handling.md)

</details>
