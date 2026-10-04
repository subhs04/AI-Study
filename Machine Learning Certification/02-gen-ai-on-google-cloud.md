# Module 2 — Gen AI on Google Cloud

> **Naming note:** product names follow the course (*Agent Platform*, *Agent Studio*, *Gemini Enterprise app*). Older docs and exam material may use *Vertex AI* / *Vertex AI Studio* for the platform and studio. Model lineups change often, so confirm against Google's current documentation.

## Module map

```
1. Gen AI overview     → what gen AI is, Google's gen AI stack
2. Foundation models   → the bottom layer: Gemini, specialty models, tuning, how to access
3. Idea to App         → the tools layer: who uses what; Agent Studio; prompt anatomy
4. Prompt engineering  → designing, parameterizing, evaluating prompts in Agent Studio
```

---

## 1. Gen AI on Google Cloud (overview)

**Gen AI** generates **content** and takes **action**.

| Capability | Detail |
|---|---|
| Content (multimodal) | Text, code, images, speech, video, 3D |
| Trigger | A **prompt** (a question or an instruction) |
| Actions (via **AI agents**) | Autonomous, goal-oriented: automate workflows, plan and book travel, schedule appointments |

**Google's timeline:**

```
2017 Transformer research  →  2023 natively multimodal Gemini  →  2026 platform-centric agentic AI
(architecture behind all modern gen AI)
```

### Gen AI stack

```
┌──────────────────────────────────────────────────────────────┐
│ AI APPLICATIONS   Gemini Enterprise app (no-code agents),    │
│                   Gemini Notebook                            │
├──────────────────────────────────────────────────────────────┤
│ AI DEVELOPMENT    Agent Studio, Agent Garden, Model Garden   │
│                   → prototype, deploy agents, fine-tune      │
├──────────────────────────────────────────────────────────────┤
│ FOUNDATION MODELS Gemini and specialty models                │
│ (built on Google's AI infrastructure)                        │
└──────────────────────────────────────────────────────────────┘
```

> **Takeaway:** Gen AI creates content and acts through agents. Google's stack runs from foundation models, to development tools, to no-code applications.

---

## 2. Foundation Models

### What a foundation model is

- Training on a huge amount of existing content (text, image, video) produces a **foundation model**.
- "Large" refers to **parameters**, training data, and compute. More parameters generally means more capacity to learn complex patterns (millions → trillions in recent years).

> **Takeaway:** A foundation model is a large pre-trained model whose parameter count signals its capacity to learn complex patterns.

### Google's model families

| Family | Model | Best for |
|---|---|---|
| **Gemini** (general purpose, multimodal) | **Pro** | Most capable; complex tasks needing advanced reasoning |
| | **Flash** | Fast, low latency; high-volume real-time apps (e.g. chatbots) |
| | **Flash-Lite** | Most cost-effective; high-volume tasks where time isn't critical (batch translation, summarization) |
| **Specialty** | **Nano Banana** | Image generation |
| | **Veo** | Video |
| | **Chirp** | Voice generation |
| | **Lyria** | Music composition |
| | **Embeddings models** | Semantic search, data representation |

**Quick picker:** need reasoning → Pro. Need speed → Flash. Need lowest cost at volume → Flash-Lite. Need multiple data types in one task → Gemini.

### Multimodal AI

- **Multimodal** = processes **and** generates multiple modalities (text, image, audio, video) together, which supports more human-like understanding of context.
- Because Gemini is general-purpose and multimodal, it can potentially replace some specialty models.
- **Business uses:** information **extraction** (read text from images/video), **analysis** (categorize expenses from a receipt), **seeking** (Q&A over mixed media), **content creation** (ads or stories inspired by images/video).

**Cross-check example:** assess home-insurance risk from property photos, weather histories, inspection reports and disaster videos. Mixed data types means **Gemini**. (Veo = video, Embeddings = search representation, Nano Banana = image generation, so none handles mixed input.)

> **Takeaway:** When a task mixes data types, choose the multimodal Gemini model.

### Pre-trained vs fine-tuned; horizontal vs vertical

```
Foundation model ──pre-trained on large general data──►  general capability
        │
        └── fine-tune with a much SMALLER field-specific dataset ──► tailored model
```

| Concept | Meaning |
|---|---|
| **Pre-trained** | Trained for general purposes on a large dataset |
| **Fine-tuned** | Further trained on a smaller, domain-specific dataset (healthcare, finance) because foundation models can lack specialized data |
| **Horizontal AI** | Broad foundation models (LLMs) that solve common problems across industries: content creation, summarization, Q&A |
| **Vertical AI** | Models fine-tuned for a specific industry niche (retail, finance, healthcare), e.g. disease diagnosis |

*Analogy:* 12 years of school = pre-training; medical school = fine-tuning.

> **Takeaway:** Pre-train broadly, fine-tune narrowly. Horizontal AI is broad, vertical AI is industry-specific.

### Three ways to use gen AI models

| Approach | Code level | Typical use |
|---|---|---|
| **Google Cloud console UI** | No-code | Explore and test prompts |
| **Gen AI model APIs** (e.g. Gemini API, with cURL) | Low-code | Call models directly |
| **SDKs** (Python, Java) | Code-based | Used in notebooks (Colab Enterprise, Workbench) |

---

## 3. Idea to App

### Who needs what (Cymbal Insurance use case)

| Persona | Need | Fitting tool |
|---|---|---|
| **Bea**, business analyst (non-technical) | Quickly prototype an idea (risk analysis and reports) | Low/no-code: **Agent Studio** prototype, **Gemini Enterprise app / Gemini Notebook** |
| **Ann**, AI developer | Draft, evaluate, refine and manage prompts | **Agent Studio** (prompt engineering) |
| **Ian**, ML engineer | Secure, scalable pipelines to deploy prompts and fine-tune models | **Agent Platform** |

### Google's gen AI products

| Product | Purpose |
|---|---|
| **Gemini Enterprise app** | Collaborative workspace for employees to discover, create, customize and orchestrate agents (**Gemini Notebook** is a component) |
| **Agent Platform** | For developers and technical teams to **build, scale, govern and optimize** enterprise-grade agents |
| **Agent Studio** | Low-code tool to design, build and test agents; the gateway to gen AI |
| **Gemini Enterprise for Customer Experience** | Suite for customer-facing industries (retail, telecom, finance, healthcare, travel, etc.) |

**Agent Studio lets you:** prototype fast, **tune models on your own data**, **augment with up-to-date information**, and **deploy with auto-generated code**.

> **Takeaway:** Business users go to Gemini Enterprise app, prompt designers to Agent Studio, and engineers to Agent Platform.

### Prompt vocabulary

| Term | Meaning |
|---|---|
| **Prompt** | A natural-language request (question, task, instruction) to the model |
| **Prompt design** | Creating prompts to get the desired response |
| **Prompt engineering** | The **iterative** process of designing, refining and optimizing prompts |

### Anatomy of a prompt

| Component | Required? | Role | Example |
|---|---|---|---|
| **Task** | **Required** | Core instruction | "Conduct a risk analysis for an insurance company." |
| **Context** | Optional | Background / system instructions | "You are a business analyst overseeing risk assessment." |
| **Examples** | Optional | Demonstrations, steps, output format | A report template |

- **Zero-shot** = task only, no examples (fine for simple tasks).
- **Few-shot** = task + examples (useful for complex tasks).
- Good prompts get **content** (instructions, context, examples) and **structure** (order, labels, delimiters) right.

**Cross-check example:** which prompt is best?
- A. "Provide a risk assessment report." Task only, vague.
- B. "Conduct a market risk analysis for a health insurer in the US." Task plus some specifics, no context or examples.
- **C.** Role/context + task with steps + report template. **Best:** it has all three components.

### Tips for effective prompts

- **Be direct and specific**, using keywords.
- **Use structure**: break complex tasks into steps, use delimiters.
- **Iterate and refine**: start simple, improve from output.
- **Advanced techniques:** few-shot, **chain-of-thought**, **RAG** (retrieval-augmented generation), covered later in the course.
- Basics: avoid jargon, set clear goals, create scenarios.

### Agent Studio helpers

- **"Help me write"**: AI-assisted prompting.
- **Prompt gallery**: examples filtered by modality, task, and features.
- **Multimodal prompts**: embed documents, PDFs, images, videos, YouTube content.
- **Build with Code → Deploy as App**: generates a web application from your prototype.

> **Takeaway:** An effective prompt has a required task plus optional context and examples, in a clear structure.

---

## 4. Prompt Engineering (design → evaluate → refine)

```
DESIGN prompt ─► CHOOSE model + PARAMETERS ─► EVALUATE (compare) ─► REFINE ─► MANAGE
 (system instr.,   (temperature, Top K,         (side-by-side,        (Colab         (versioning,
  prompt, examples) Top P)                       ground truth)        Enterprise)    sharing)
```

### Designing in Agent Studio

- **System Instructions** = context. **Prompt** = task or question. **Examples** via Input/Output (or Question/Answer).
- Add media from Cloud Storage, Google Drive, local files, a URL, or YouTube links.
- **Prompt templates**: prompts with **replaceable variables**, like a function with arguments in natural language. Reuse one prompt with different values (e.g. "Los Angeles tenant vacancy rate" / "real estate market analysis" → "annual crime rate" / "insurance risk assessment").

### Choosing a model

| Model source | Meaning |
|---|---|
| **Google models** | Gemini and specialty models; Agent Studio's key advantage |
| **Partner models** | Proprietary models from third-party AI companies (e.g. Anthropic) |
| **Open (open-weight) models** | Trained **weights are publicly released** |

Specialty models for generating media: Nano Banana (image), Chirp (voice), Veo (video), Lyria (music).

### Parameters: controlling randomness of output

Models predict a **probability for each possible next token**; these parameters decide how one is selected.

Illustrative next-word probabilities for *"The garden was full of beautiful…"*:

| Word | Probability |
|---|---|
| flowers | 45% |
| trees | 20% |
| herbs | 15% |
| bugs | 5% |
| (others) | 15% |

| Parameter | How it works | Effect |
|---|---|---|
| **Temperature** | Overall randomness setting | **Low** → narrow to high-probability, typical words (best for Q&A, summarization). **High** → includes lower-probability, unusual words (creative content) |
| **Top K** | Pick from the **K most probable** words. K=2 → "flowers" or "trees" | Fixed-size candidate set. Weakness: with a skewed distribution (flowers 80%, books 10%) a fixed K can admit odd words ("beautiful books") |
| **Top P** | Pick from the **smallest set whose cumulative probability ≥ P**. P=75% → flowers + trees + herbs (45+20+15 = 80%) | **Dynamic** set size that adapts to the distribution; created to fix Top K's weakness |

- Always picking the most probable word → repetitive, potentially biased text. Pure random sampling → unlikely words ("bugs"). Parameters balance **predictability vs variety**.
- You don't need to adjust Top K / Top P constantly.
- *Clarification:* in practice the model still samples within the candidate set in proportion to probabilities. The course's "equal chance" wording is a simplification.

> **Takeaway:** Temperature sets overall randomness; Top K limits candidates to a fixed count; Top P limits them by cumulative probability, so its set size adapts.

### Evaluation, refinement, management

| Step | Detail |
|---|---|
| **Compare** | Side-by-side prompts to see how prompt, model or parameter changes affect output |
| **Evaluate** | Add **ground truth** (your preferred answer) as a reference to score responses |
| **Optimize** | In a **Colab Enterprise** notebook, add labeled examples to refine results |
| **Prompt management** | Storage to save and share prompts, with **version control** and security |

Techniques also apply to real-time streaming, multimedia creation, translation, and speech/text conversion. *Next step (later lesson): deploying the prompt to code.*

---

## Common confusions

| Mix-up | Correct |
|---|---|
| Prompt design = prompt engineering | Design = creating a prompt. Engineering = the **iterative** refine-and-optimize process |
| Task, context and examples are all required | Only the **task** is required |
| Zero-shot vs few-shot | Zero-shot = no examples. Few-shot = examples included |
| Higher temperature = more accurate | Higher = more **random / creative**; use **low** for factual Q&A |
| Top K and Top P are the same | K = fixed count of words. P = cumulative probability, so set size varies |
| Open models = free models | Open means the **weights are publicly released** |
| Flash vs Flash-Lite | Flash = **speed / low latency**; Flash-Lite = **lowest cost**, time-insensitive |
| Fine-tuning needs as much data as pre-training | Fine-tuning uses a **much smaller**, domain-specific dataset |
| Agent Studio = Agent Platform | Studio = low-code prototyping/prompting tool; Platform = broader enterprise build/scale/govern platform |
