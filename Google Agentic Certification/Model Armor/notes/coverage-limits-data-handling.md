# Model Armor: Coverage, Limits, Data Handling and Pricing

**Module map:** What content Model Armor can and cannot screen (languages, documents, images), its blind spots, how it handles data, and pricing/token basics. Covers **5.2** (guardrail design needs knowing the gaps). Prerequisites: `overview-threats-filters.md`, `modes-logging-floor-settings.md`.

---

## 1. Coverage matrix [5.2]

| Content | Supported | Key constraints |
|---|---|---|
| **Text** prompts/responses | Yes, multi-language | RAI and injection/jailbreak filters **tested** in: Chinese (Mandarin), English, French, German, Italian, Japanese, Korean, Portuguese, Spanish. May work in other languages with varying quality. SDP language support depends on selected infoTypes |
| **Documents** | PDF, CSV, TXT, DOCX/DOCM/DOTX/DOTM, PPTX/PPTM/POTX/POTM/POT, XLSX/XLSM/XLTX/XLTM | **≤ 4 MB**: larger files are **skipped (not scanned)**. Files **< 69 bytes** rejected with `InvalidDocumentInputException`. Extracted text is subject to token limits. Screens for safety, injection/jailbreak, sensitive data, malicious URLs |
| **Images** (**Preview**) | JPEG, PNG, BMP | ≤ 4 MB each. Methods: visual scanning (advanced SDP filter) and OCR for text in images. **us and eu multi-regions only**; elsewhere `invocation_result` = `FAILURE`. Images embedded in files are not screened. **One image per request**. Images alongside text are not screened via `SanitizeUserPrompt` / `SanitizeModelResponse` |
| **Audio / video** | **No** | |

**Multi-language detection can be enabled:**
- **Per request** (granular control), or
- **One time at template level** via the REST API.

---

## 2. Limitations (blind spots) [5.2]

| Limitation | Implication for architecture |
|---|---|
| **Single-turn only:** each prompt/response is inspected independently; no conversation history or multi-turn context | An attack split across turns is not seen as a whole by Model Armor |
| **No decoding:** Base64, hexadecimal, URL encoding, ciphertext are not decoded/inspected | Encoded payloads are opaque to it |
| **No audio/video** | Needs another approach for those modalities |
| **No combined text + image in one request** | Per the overview's limitations |
| **Files over 4 MB skipped** | An oversized file is not scanned (and not charged) |

> **Clarification (not in source):** The source states the limits but not the compensating controls. Typical mitigations (decoding/normalizing upstream, size limits at the entry point, stateful checks in the agent layer, least-privilege identity for tools) are architectural judgment, not course content. Verify against the governance and identity modules and official docs.

> **Gap:** Compensating controls such as Agent Identity / Principal Access Boundary and HITL are exam objectives (5.1, 5.2) not covered in this module's source.

**Takeaway:** Model Armor is a single-turn, no-decode, text/doc/image filter. Know the blind spots when designing the full guardrail stack.

---

## 3. Data handling and storage [5.2]

**What/why:** Teams worry about a security filter itself becoming a data store of sensitive prompts. Model Armor is designed for data minimization.

- **Stateless:** processes prompts and responses **in memory**; no durable retention during normal operation.
- **Cloud Logging is the only storage path** for content-related data, and only if **you enable** it. Scope and retention follow your Logging configuration.
- **Encryption:** TLS 1.2+ in transit.
- **Regional residency:** transient processing stays inside the geographic boundary you define, such as US or EU.
- **Selective processing:** data is sent only to **active filters**. A disabled filter's backing service receives nothing.
- **Compliance:** infrastructure audited for SOC 1/2/3 and ISO/IEC 27001.

```
Prompt ─► Model Armor (memory only) ─► verdict
                        └─ optional ─► Cloud Logging (your retention rules)
```

**Takeaway:** Stateless by default; logging is the only persistence and is opt-in.

---

## 4. Pricing and tokens

- Purchased **standalone** or as part of **Security Command Center**.
- **Skipped data is not charged.**
- A **token** is a unit of text (about **4 characters**). Model Armor uses the total tokens in prompts and responses for **pricing** and for **filter limit enforcement** (per-filter token limits exist; exact values not in source).

> Verify current pricing and per-filter token limits in the official docs.

---

## 5. Worked example: choosing what to rely on

**Scenario (*illustrative*):** A claims agent accepts PDF uploads from users and a user attaches a 6 MB PDF.

- Model Armor **skips** the file (over 4 MB), so no safety/injection/SDP verdict is produced for it.
- Cross-check against the matrix: documents ≤ 4 MB only ✔. Skipped data is also not charged ✔.
- Takeaway for design: you cannot treat "no block" as "scanned and safe" for oversized files. Check size before relying on the screening.

---

## Common confusions

| Looks like | Difference |
|---|---|
| "Skipped" file vs "scanned and clean" | A skipped (>4 MB) file got no verdict at all |
| Stateless vs no logging | Model Armor itself is stateless; Cloud Logging can still record events if enabled |
| Document screening vs image screening | Different limits: documents ≤ 4 MB in listed formats; images are **Preview**, single image, us/eu only |
| Prompt injection filter vs decoding | The filter catches code-disguised text, but does **not** decode Base64/hex/etc. |
| Model Armor vs Security Command Center | Model Armor screens traffic; SCC can aggregate its results (and is a purchasing path) |

Sources: Model Armor overview doc (see `sources/overview.md`).
