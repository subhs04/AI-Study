# Prompt Engineering, Model Parameters, Grounding and Tuning

**Module map:** Supplementary reading-list lessons (Introduction to Vertex AI Studio; not in the main course transcript). Prompt anatomy, zero/few-shot, system instructions, templates, model selection, sampling parameters (temperature, top-K, top-P), evaluation, grounding vs RAG, and the model-customization ladder (prompt design → parameter-efficient tuning → full fine-tuning; supervised fine-tuning). Covers **1.1** (system instructions, few-shot, chain-of-thought), **3.1** (model selection, tuning), **3.2** (grounding/RAG), **4.1** (comparison with ground truth). Prerequisite: `prototyping-tools-and-ucp.md`.

> **Product note:** "Vertex AI Studio" is now part of **Agent Platform** (formerly Vertex AI). UI names (e.g., "Codey", menus) may have changed; verify in current docs. Strip: the Bea/Ann/Ian storyline.

---

## 1. Prompt anatomy [1.1]

**What/why:** A **prompt** is a natural-language request to a model. **Prompt design** = crafting the prompt; **prompt engineering** = the *iterative* loop of drafting, refining and assessing responses. Simple tasks often work without it; complex tasks need it.

| Component | Role | Notes |
|---|---|---|
| **Task** | What the model should do (core) | Only a task = **zero-shot** prompting |
| **Context / system instructions** | Sets the stage: role, background | e.g., "You are a business analyst overseeing risk for an insurer..." |
| **Examples** | Show how to respond (sample answers, step-by-step instructions, output format) | Providing examples = **few-shot** prompting |

Quality = **content** (all relevant instructions, context, examples) + **structure** (order, labels, **delimiters** such as bullets, dashes, headings).

Tips: be **direct and specific** (state request up front, add context and keywords: "risk report for real estate 2024-2025 considering market trends and regulation" beats "generate a risk report"); **structure** with delimiters and break complex tasks into small steps; **iterate and refine**; advanced: few-shot, **chain-of-thought** (prompting the model to reason step by step), RAG.

Quiz: the best prompt is the one with **role/context + steps + template** (option C), not a bare request.

**Takeaway:** Task + context (system instructions) + examples; zero-shot = task only, few-shot = with examples.

---

## 2. Studio features that map to enterprise practice [1.1]

| Feature | What it does |
|---|---|
| **System instructions** field | Set the scene (left side), separate from the task prompt |
| **Prompt templates** | Prompts with **replaceable variables**, like a function with arguments but in natural language; reuse by changing values |
| **Multimodal input** | Docs, images, video from Cloud Storage, Drive, local, URL; YouTube links |
| **"Help me write"**, **Prompt gallery** | AI-assisted prompt polishing; browsable examples (filter by modality, task, feature) |
| **Build with code** | Auto-generates SDK code (e.g., Python notebook) or **API/cURL**; "Create generative app" scaffolds a web app |
| **Prompt management** | Save/share prompts, version control, security; compare, optimize, evaluate |

---

## 3. Model selection and sampling parameters [3.1]

| Need | Model family (as stated) |
|---|---|
| General purpose, **multimodal** | **Gemini** (Flash/Pro) |
| Image generation | Imagen |
| Code completion | Codey (older name; verify current) |
| Video | Veo |
| Semantic search/data representation | **Embeddings** models |

Studio also exposes third-party models (Anthropic Claude, Meta Llama, OpenAI GPT).

**Sampling:** the model predicts a probability for each candidate next token; parameters control which is selected.

| Parameter | What it does | Use |
|---|---|---|
| **Temperature** | Controls randomness. **Low** = high-probability, typical words; **high** = lower-probability, unusual words | Low for Q&A/summarization (predictable); high for creativity |
| **Top-K** | Pick randomly among the **K** most probable tokens (K=2 → two most likely) | Weakness: with a skewed distribution (flowers 80%, books 10%) it can pick a poor token |
| **Top-P** | Sample from the **smallest set** whose cumulative probability ≥ P (e.g., P=0.75 → flowers, trees, herbs) | Shortlist size adapts dynamically to the distribution |

Not required to adjust top-K/top-P constantly. Always picking the single top token → repetitive text; pure random → odd output.

```
"The garden was full of beautiful ___"
 flowers 0.40  trees 0.20  herbs 0.15  bugs 0.02 ...
 top-K=2  → {flowers, trees}            (fixed size)
 top-P=.75→ {flowers, trees, herbs}     (cum 0.75; size varies)
```
*(Probabilities illustrative.)*

---

## 4. Evaluation and refinement [4.1]

- **Compare prompts side by side** across prompts/models/parameters.
- **Ground truth** = your preferred answer; other responses are evaluated against it (you can create custom metrics).
- **Optimize** in a Colab Enterprise notebook by adding labeled examples.

Matches the Scale-stage "compare to ground truth" idea in `framework-and-lifecycle.md`.

---

## 5. Grounding vs RAG vs tuning [3.1][3.2]

**Problem:** models are **pretrained**; answers depend on training data that may be outdated or wrong.

| Concept | What | Relationship |
|---|---|---|
| **Grounding** | Link the model to reliable external data so responses are checked against current information ("fact-checker") | The **concept** |
| **RAG** (retrieval-augmented generation) | Retrieve relevant data and supply it to the model | One **implementation** of grounding |
| **Fine-tuning** | Train on task examples to refine the model's **internal** knowledge and abilities | Changes the model |
| Grounding | **Augments** knowledge with external, real-time data | Does not change weights |

Grounding sources in Studio: **Google real-time search** (most current info) or **your own data** (field-specific).

### Customization ladder (less → more compute)
| Method | Alters model parameters? | Best when | Cost |
|---|---|---|---|
| **Prompt design** | No: guides behavior via instructions/examples | Rapid experimentation; no ML skills needed | Lowest |
| **Parameter-efficient tuning** (adapter tuning) | Updates a **small subset** of parameters | Efficient adaptation to a task/domain | Medium |
| **Full fine-tuning** | Updates **all** parameters | Highly complex tasks needing higher quality | Highest (tune **and** serve) |

**Supervised fine-tuning (SFT)** (what Vertex AI supported per the course): teaches a **new skill** with **hundreds of labeled examples**; each example shows the desired output; result = new model combining new parameters with the original. Good for well-defined tasks with labeled data: **classification, summarization, extraction, chat**.
- Data format: **JSONL**, each row = input text (prompt) + output text (expected response); e.g., "The room was terrible..." → *negative*.
- Flow: Tuning → Create tuned model → specify model + dataset → monitor job in console → tuned model appears in the **Model Registry** → **deploy to an endpoint** for serving or test in Studio.

**Decision (scenario):**
| Situation | Pick |
|---|---|
| Answers outdated / need current or private facts | **Grounding/RAG** (no retraining) |
| Need consistent new behavior/format on a defined task with labeled data | **Supervised fine-tuning** |
| Quick behavior change, no ML skills | **Prompt design** |

Pointer from course: for RAG pipelines see BigQuery and Vertex AI vector search courses (not covered here). **Gap:** exam 3.2 expects embeddings, similarity scoring, reranking, Vector Search, Agent Retrieval, which this source does not teach.

---

## Common confusions

| Looks like | Difference |
|---|---|
| Prompt design vs prompt engineering | Design = crafting; engineering = iterative refine-evaluate loop |
| System instructions vs task prompt | Instructions = persistent role/context; prompt = the specific task |
| Zero-shot vs few-shot | Few-shot adds examples |
| Top-K vs top-P | K = fixed count; P = cumulative-probability threshold (dynamic size) |
| Temperature vs top-P/K | Temperature reshapes randomness overall; top-K/P limit the candidate shortlist |
| Grounding vs RAG | Grounding is the goal; RAG is a way to do it |
| Grounding vs fine-tuning | Grounding adds external knowledge at response time; fine-tuning changes the model |
| Adapter tuning vs full fine-tuning | Subset vs all parameters |

Sources: Reading-list lessons (`sources/ReadlistList.md`, supplemental; not part of the main course transcripts).
