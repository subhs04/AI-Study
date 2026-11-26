# Ask Gemini Capture Prompts

Use **one fresh Ask Gemini chat session per course module**.

**Per-page flow:**
1. Expand any `+` accordions, tabs, or flashcards on the page so their text is in the DOM.
2. Run the **Per-page capture prompt** below (so all technical details are persisted in chat history before you navigate away).
3. Repeat for each page (typically 4–5 pages per module).
4. At the end of the module, run the **End-of-module export prompt** and download the `.md` file into `<Module Name>/sources/raw.md`.

---

## 1. Per-page capture prompt

```text
Summarize the main lesson content inside the lesson iframe as comprehensive, detailed technical study notes under `## <exact page title>`. Do NOT omit any technical details: include all concepts, definitions, comparisons/tables, code or JSON snippets, configuration keys, limits/defaults, console UI paths, diagram flows, and exact product/metric names. Ignore site navigation, footers, and popups/banners (such as Recertify or Update prompts).
```

---

## 2. Quiz capture prompt (optional, if a quiz is in the module)

```text
Document the quiz on this page under `## Quiz: <title> (<N> questions)` as structured study notes: list each question's topic/scenario, the options, my selected answer, and the correct answer with its explanation if shown. Ignore site navigation, footers, and popups/banners.
```

---

## 3. End-of-module export prompt

```text
Combine all captured lesson notes from this session into one downloadable file "<module-name>-raw.md". Include a "## Capture manifest" at the top, keep every lesson section in full detail under "## <page title>" (do NOT condense further), and add "## Terms & renames seen" at the end.
```

*(If you also captured a quiz and want it separate, ask: `Export the captured quiz into "<module-name>-quiz.md" verbatim.`)*

---

## 4. Process in AGY

1. Move the downloaded file(s) to `<Module Name>/sources/raw.md` (and `quiz.md` if separate).
2. In AGY, run:
   ```text
   process <Module Name>
   ```

> **Why per-page output matters:** Ask Gemini only attaches the active browser tab to the current turn and drops it once you navigate away. Outputting each page verbatim in its own turn keeps the text in chat history for the end-of-module export. If a very long module hits context/output limits during export, copy each turn's output directly into `raw.md` as you go.
