# Module 02 — Generative AI (incl. Large Language Models)

> Numbers/code marked **(illustrative)** are mine, added to make an idea concrete. Everything else comes from the course material.

## Module map

| Section | Concept | Built from lectures |
|---|---|---|
| 1 | The landscape: AI → ML → DL → Gen AI | Where Gen AI fits · Supervised/Unsupervised · Gen AI as subset of DL |
| 2 | What makes a model *generative* | Is it Gen AI or not · Gen AI: a formal approach |
| 3 | Transformers, hallucinations, prompts | Transformers, Hallucinations and Prompts |
| 4 | Model types, foundation models, code generation | Gen AI Model Types · Gen AI for Code Generation |
| 5 | Large Language Models (LLMs) | Intro to LLM (definition, benefits, vs traditional ML) |
| 6 | Prompting: design vs engineering, LLM types, chain-of-thought | Intro to LLM |
| 7 | Tuning ladder: prompt → task model → fine-tune → PETM | Intro to LLM |
| 8 | Google Cloud tooling (+ product renames) | Leveraging Google Cloud · Intro to LLM (repeated) |

The two "Google Cloud tools" segments (Gen AI intro and LLM intro) were identical, so they are merged into §8.

---

## 1. The landscape: AI → ML → DL → Gen AI

```
┌─ Artificial Intelligence (discipline: intelligent agents that reason, learn, act) ─┐
│  ┌─ Machine Learning (learn a model from data, no explicit programming) ───────┐  │
│  │  ┌─ Deep Learning (multi-layer artificial neural networks) ──────────────┐  │  │
│  │  │  ┌─ Generative AI ─┐   ┌─ Large Language Models ─┐                    │  │  │
│  │  │  └─────────────────┘   └─────────────────────────┘  (overlap heavily) │  │  │
│  │  └────────────────────────────────────────────────────────────────────────┘  │  │
│  └──────────────────────────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────────────────────────┘
```

- **AI** is a *discipline* (like physics is to science), a branch of computer science.
- **ML** is a *subfield* of AI: a system that trains a **model** from input data, then makes predictions on new, unseen data drawn from the same distribution as the training data.
- **Deep learning** = ML using **artificial neural networks** (many layers of interconnected "neurons"), which capture more complex patterns than classical ML.
- **Gen AI** and **LLMs** are both subsets of deep learning.

> **Takeaway:** AI ⊃ ML ⊃ Deep Learning ⊃ Gen AI / LLMs.

### Learning styles

| Style | Data | Goal | Course example |
|---|---|---|---|
| **Supervised** | **Labeled** (tag = name, type, or number) | Learn from past examples to predict future values | Predict **tip amount** from bill amount + order type (pickup/delivery) |
| **Unsupervised** | **Unlabeled** | **Discovery**: find natural groups in raw data | Cluster employees by **tenure and income** to spot the "fast track" group |
| **Semi-supervised** | Small labeled + large unlabeled | Labeled data teaches the basics; unlabeled data helps generalise | Neural networks can do this |

> **Takeaway:** Supervised has labels (predict); unsupervised has none (discover); semi-supervised mixes both.

### How supervised learning improves (the error loop)

```
 input x ──► model ──► prediction ──► compare with actual label ──► error (loss)
              ▲                                                         │
              └────────── adjust model to shrink error ◄────────────────┘
```

Training is an **optimisation problem**: keep adjusting the model until predictions and actual values are close.

*Clarification:* the source says "testing data x is input" and compares to "training data". Cleaner version: the model predicts from inputs; the prediction is compared with the **known label**; the gap is the **error/loss**.

**Worked example (illustrative): tip prediction, mean absolute error**

| Order | Actual tip | Predicted | \|Error\| |
|---|---|---|---|
| 1 | $3.00 | $2.50 | 0.50 |
| 2 | $5.00 | $4.00 | 1.00 |
| 3 | $2.00 | $3.00 | 1.00 |

MAE = (0.50 + 1.00 + 1.00) / 3 = **0.83**. *Cross-check:* total error 2.50 ÷ 3 orders = 0.83 ✔. Training succeeds when this number shrinks.

---

## 2. What makes a model *generative*

### Discriminative vs generative

| | Discriminative | Generative |
|---|---|---|
| Job | **Classify / predict labels** for data points | **Generate new data instances** |
| Learns | **Conditional** probability **P(y \| x)** | **Joint** distribution **P(x, y)** (how the data itself is distributed) |
| Dog/cat example | "Is this a dog or a cat?" → *dog* | Can say "dog" **and** draw a new dog picture |
| Trained on | Labeled data | Existing content (labeled and/or unlabeled) |

*Why a generative model can also classify:* P(y\|x) = P(x, y) / P(x). If you know the joint, you can derive the conditional, but not the other way round. That is why generative ⊇ discriminative in capability.

> **Takeaway:** Discriminative models *discriminate between* data; generative models *create* new data.

### The "Is it Gen AI or not?" test

Think of the model as **y = f(x)**: **x** = input (CSV, text, audio, image), **f** = the model, **y** = output.

> **Rule: look at y.** If y is a **number or class/label** → *not* Gen AI. If y is **natural language, image, audio, video, code** → Gen AI.

| Task | y | Gen AI? | Cross-check |
|---|---|---|---|
| Spam / not spam | class | **No** | A label, not new content |
| Predicted sales = 1,250 **(illustrative)** | number | **No** | A number |
| Probability of default | probability | **No** | A number |
| "Define sales" → a sentence | text | **Yes** | Free text generated |
| Prompt → picture of a dog | image | **Yes** | New pixels created |

### Traditional ML vs Gen AI pipeline

```
Traditional supervised ML:
  training code + labeled data ──► model ──► prediction / classification / cluster

Generative AI:
  training code + labeled + UNLABELED data (all types) ──► FOUNDATION MODEL
                                                              │
                                    ──► new content: text, code, image, audio, video
```

### Three eras of building intelligence

| Era | How the "cat" knowledge is encoded |
|---|---|
| **Traditional programming** | Hand-coded rules (legs = 4, ears = 2, fur = yes …) |
| **Neural networks** | Show labelled cat/dog pictures → model predicts *cat / not cat* |
| **Generative** | Models (e.g., Gemini) trained on huge multi-source data → you **ask** (type or speak a prompt) and it **generates** an answer |

### Formal definition and how it works

> **Gen AI** is AI that **creates new content based on what it learned from existing content**. Learning = **training**, which produces a **statistical model**. Given a **prompt**, the model **predicts the most likely expected response**.

```
 existing content ──training──► statistical model ──(prompt)──► predicted response = NEW content
```

- It learns the **underlying structure** of the data, then generates new samples *similar* to the training data.
- Example: "I'm making a sandwich with peanut butter and…" → **jelly**. Generative language models are **pattern-matching systems** that predict what comes next.
- LLMs are only **one type** of Gen AI.

| Model | Input → Output examples |
|---|---|
| **Generative image model** | image → text (visual Q&A), image (completion), video (animation) |
| **Generative language model** | text → text (Q&A), image, audio, decisions |

> **Takeaway:** Gen AI = learn patterns from data (training → statistical model), then predict/generate new content from a prompt.

---

## 3. Transformers, hallucinations, prompts

### Transformer

```
 input sequence ──► ENCODER ──(representation)──► DECODER ──► output for the task
```

The encoder encodes the input; the decoder learns to decode that representation for the task. Transformers drove the modern NLP revolution (roughly 2018 onward).

*Clarification:* the encoder-decoder layout is the original design; many popular LLMs use only the decoder part. Certification level: just know "transformer = encoder + decoder".

### Hallucinations

**Hallucination** = model output that is **nonsensical, grammatically wrong, or factually incorrect/misleading**.

*Clarification:* the source stresses nonsense/grammar errors, but the dangerous kind is **fluent text that is confidently wrong**.

| Cause | Typical fix (see Module 05 for Google tooling) |
|---|---|
| Not trained on **enough data** | Better/more data, or tune |
| Trained on **noisy/dirty data** | Clean, curate data |
| Not given **enough context** | Richer prompt |
| Not given **enough constraints** | Tighter prompt (format, scope) |

> **Takeaway:** Hallucinations come from weak data, missing context, or missing constraints, and they make outputs misleading.

### Prompts

- **Prompt** = short text input given to an LLM to control its output.
- **Prompt design** = crafting the prompt that yields the desired output.
- A browser-based prompt box lets any user generate content without coding.

---

## 4. Model types, foundation models, code generation

### Model types by input → output

| Type | What it does | Notes |
|---|---|---|
| **Text-to-text** | Natural-language in → text out; learns mapping between text pairs | e.g., translation |
| **Text-to-image** | Trained on images with short captions | **Diffusion** is one method |
| **Text-to-video** | Sentence or full script → video | |
| **Text-to-3D** | Text → 3D object | Games / 3D worlds |
| **Text-to-task** | Text → a **defined action** | Answer a question, search, predict, **act in a UI** |

*Clarification:* the lecture says a "text-to-text model" can navigate a web UI. That behaviour is the **text-to-task** category.

### Foundation model

> **Foundation model** = a large AI model **pre-trained on a vast quantity of data**, designed to be **adapted/fine-tuned for many downstream tasks** (sentiment analysis, image captioning, object recognition). Uses: fraud detection, personalised customer support, health care, finance.

**Model Garden** (Google Cloud catalog) includes foundation models:
- **Language:** chat, text, code
- **Vision:** e.g., **Stable Diffusion** (text → high-quality images)
- **Task-specific models:** e.g., **sentiment analysis** (language), **occupancy analytics** (vision)

> **Takeaway:** A foundation model is pre-trained once on broad data and adapted many times for specific tasks.

### Code generation (Gemini)

Gemini can: **debug** code, **explain** code line by line, **write SQL**, **translate** between languages, **generate documentation/tutorials**. Course demo: convert a Pandas DataFrame (filename + hour columns) into a specified JSON format; the notebook code can be exported to Colab.

---

## 5. Large Language Models (LLMs)

> **LLM** = a **large, general-purpose** language model that is **pre-trained** on a huge dataset, then **fine-tuned** for specific purposes with a much smaller dataset. LLMs are a subset of deep learning and are almost exclusively **transformer-based**.

**Analogy:** all dogs learn sit/stay/come (pre-training); a police or guide dog gets extra specialised training (fine-tuning).

### What "Large", "General-purpose", "Pre-trained & fine-tuned" mean

| Word | Meaning |
|---|---|
| **Large** | (1) **Huge training dataset** (up to petabyte scale), (2) **huge parameter count** |
| **General-purpose** | Solves common language tasks (classification, Q&A, summarisation, generation) across industries. Reasons: language is common across tasks; only a few organisations can afford to train such models, so they build base models for others |
| **Pre-trained → fine-tuned** | Pre-train broadly, then adapt with a small domain dataset (retail, finance, entertainment…) |

*Clarification:* the lecture says parameters "are often called hyperparameters". **They are not.** **Parameters** (weights) are **learned during training** and hold the model's knowledge. **Hyperparameters** are **settings you choose** before/while training (learning rate, batch size).

### Benefits

1. **One model, many tasks** (translation, completion, classification, Q&A…).
2. **Minimal domain data needed** → works in **few-shot** or **zero-shot** settings.
3. **Performance keeps improving** with more data and parameters.

*Clarification on few/zero-shot:* **zero-shot** = the model does a task it was never explicitly trained for, with no examples. **Few-shot** = you give a few examples (typically *in the prompt*) and the model's weights are not changed.

### LLM development vs traditional ML development

| | Pre-trained LLM | Traditional ML |
|---|---|---|
| ML expertise | Not required | Required |
| Training examples | Not required | Required |
| Train a model | **No**; use prompt design | Yes |
| Compute / hardware | Minimal for prompting | Significant |
| Effort goes into | **Prompt design** (clear, concise, informative) | Data + training |

*Clarification:* "no training" applies to **prompting** a ready-made model. Tuning (§7) does involve training.

**Generative Q&A:** the model generates free text directly from context, so you do not need to hand-build a domain-specific Q&A model.

### Worked examples (from the course) with cross-checks

| Prompt | Calculation | Cross-check |
|---|---|---|
| Sales $100,000; expenses $60,000; net profit? | 100,000 − 60,000 = **$40,000** | 40,000 + 60,000 = 100,000 ✔ |
| Inventory 6,000; order needs 8,000; units to fill? | 8,000 − 6,000 = **2,000** | 6,000 + 2,000 = 8,000 ✔ |
| 1,000 sensors in 10 regions; average per region? | 1,000 ÷ 10 = **100** | 100 × 10 = 1,000 ✔ (average, not guaranteed per region) |

---

## 6. Prompting: design vs engineering, LLM types, chain-of-thought

### Prompt design vs prompt engineering (course framing)

| | Prompt **design** | Prompt **engineering** |
|---|---|---|
| What | Tailor a clear, concise prompt to the **specific task** | Craft prompts to **improve performance** |
| How | e.g., "Translate this English text to French" | Domain knowledge, **examples of desired output**, effective keywords |
| Need | **Essential** | Only when **high accuracy/performance** is required |

*Clarification:* outside this course the two terms are often used interchangeably; for the exam, remember the course's "general vs specialised" split.

### Three kinds of LLMs (each prompts differently)

| Type | Trained to | Prompt style | Example |
|---|---|---|---|
| **Generic language model** | **Predict the next word** from training-data patterns | Completion | "The cat sat on …" → **the** (like search autocomplete) |
| **Instruction-tuned** | Predict a **response to an instruction** | Instruction | "Summarize text X", "Classify as neutral/negative/positive" |
| **Dialog-tuned** | Hold a **dialog** by predicting the next response | Question-like, in a longer back-and-forth | Chatbot |

Dialog-tuned is a **special case of instruction-tuned**.

> **Takeaway:** Generic = autocomplete; instruction-tuned = follows commands; dialog-tuned = conversational (a special case of instruction-tuned).

### Chain-of-thought (CoT) reasoning

Models answer more accurately when they **first output the reasoning**, then the answer.

```
Q: Roger has 5 tennis balls. He buys 2 cans, each with 3 balls. Total?
Without reasoning → higher chance of a wrong answer
With reasoning    → "5 + (2 × 3) = 5 + 6 = 11"  → 11
```

*Cross-check:* 2 cans × 3 = 6 new balls; 5 + 6 = **11**; counting the other way, 11 − 5 = 6 = 2 × 3 ✔.

---

## 7. Tuning ladder: cheapest to most expensive

A model that can do everything has practical limits; **task-specific tuning makes LLMs more reliable**.

```
 Prompt design ─► Task-specific foundation model ─► Parameter-efficient tuning (PETM) ─► Full fine-tuning
 (no training)    (e.g., sentiment, occupancy)       (small add-on layers trained)          (all weights retrained)
 cheapest ────────────────────────────────────────────────────────────────────────────► most expensive
```

| Method | What changes | Cost / ops | Key point |
|---|---|---|---|
| **Tuning** (umbrella) | Adapt model to a domain (e.g., legal, medical) using new data | Varies | Customises responses using examples of the target task |
| **Fine-tuning** | **Every weight** in the LLM is retrained on your dataset | **Big training job + you host your own model** | Expensive; not realistic in many cases |
| **Parameter-efficient tuning (PETM)** | Only a **small number of add-on layers**; **base model untouched** | Much cheaper; no model duplication | Add-on layers are **swapped in/out at inference** |

**Worked example (illustrative): why PETM is cheaper to store**
Base model = 100 GB; 3 tasks (legal, medical, retail).
- Full fine-tune: 3 × 100 GB = **300 GB**
- PETM: 100 GB + 3 × 0.05 GB (50 MB adapters) = **100.15 GB**
*Cross-check:* PETM adds only ~0.15 GB on top of one base copy ✔. (Module 05 notes adapter layers are "tens of megabytes".)

> **Takeaway:** Fine-tuning retrains everything; PETM trains small adapters on a frozen base, which is cheaper and swappable.

---

## 8. Google Cloud tooling and product renames

| Tool | What it is | Who it suits |
|---|---|---|
| **Studio** (prompt/tuning playground) | Explore & customise gen AI models: model library, **tuning tool**, **deploy-to-production** tool, community forum | Developers |
| **Agent Builder** | Build gen AI **search and conversational apps** (chatbots, digital assistants, custom search, knowledge bases) with **little/no code, no ML experience** | Non-coders |
| **Gemini** | **Multimodal** model: analyses text, images, audio, and code | Diverse, scalable apps |
| **Model Garden** | Continuously updated catalog of foundation and task-specific models | All |

### Renames (the source videos use older names)

| In the lecture | Current name |
|---|---|
| **Vertex AI** | **Gemini Enterprise Agent Platform** (short: **Agent Platform**), announced April 2026 as the evolution of Vertex AI |
| **Vertex AI Studio** | **Agent Studio** |
| **Vertex AI Agent Builder** (formerly *Vertex AI Search and Conversation*) | Now part of Agent Platform |
| Vertex AI Pipelines / Endpoints / Inference | Agent Platform Pipelines / Endpoints / Inference |
| "Gemini app" | Same (Google's chat app for Gemini) |

*Exam tip:* older questions or docs may say "Vertex AI". Treat it as the same platform as Agent Platform.

---

## Common confusions

| Confusion | Reality |
|---|---|
| "Gen AI and ML are separate" | Gen AI ⊂ deep learning ⊂ ML ⊂ AI |
| "Gen AI only means chatbots/LLMs" | LLMs are **one type**; image, video, audio, 3D, and task models exist too |
| "Predicting a number with a neural network is Gen AI" | Output a **number/class** → **not** Gen AI |
| "Discriminative = bad, generative = good" | Different jobs: label vs create |
| "Parameters = hyperparameters" | Parameters are learned weights; hyperparameters are settings you choose |
| "Zero/few-shot means retraining on little data" | Usually **no weight change**; examples (or none) go in the prompt |
| "Pre-trained LLM needs no training ever" | No training for **prompting**; **tuning** does train |
| "Fine-tuning and PETM are the same" | Fine-tuning updates **all weights**; PETM updates **small add-on layers**, base frozen |
| "Prompt design = prompt engineering" | Course: design is general/essential; engineering is specialised for high accuracy |
| "Hallucination = gibberish only" | Also **fluent but false** output |
| "Foundation model = LLM" | LLM is a *kind* of foundation model; foundation models can be vision, multimodal, etc. |
| "Vertex AI and Agent Platform are different products" | Agent Platform is the **new name/evolution** of Vertex AI |
