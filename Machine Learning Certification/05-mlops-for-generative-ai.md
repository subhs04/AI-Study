# Module 05 — MLOps for Generative AI

> Numbers/code marked **(illustrative)** are mine. Everything else comes from the course material. Product names follow the current branding (see the rename table in §5).

## Module map

| Section | Concept | Built from lectures |
|---|---|---|
| 1 | MLOps basics and the traditional (predictive) lifecycle | Intro · Evolution of MLOps |
| 2 | What changes for Gen AI: the framework | MLOps framework for generative AI |
| 3 | Five challenges → mitigations on Agent Platform | Navigating generative AI |
| 4 | Evaluation, monitoring and enterprise data (deep dive) | Navigating generative AI |
| 5 | Product renames | (cross-cutting) |

The intro lecture is only a course overview, so it is merged into §1.

---

## 1. MLOps basics and the traditional lifecycle

**MLOps** = standardised processes + technology infrastructure to **rapidly and reliably build, deploy, and operationalise ML systems**. It exists because training and deploying models is slow and iterative.

Course vocabulary (not a universal definition):

| Term | Meaning | Examples |
|---|---|---|
| **Predictive AI** (non-generative) | Solves specific tasks using existing data and algorithms; **cannot create new content** | Regression, classification, object detection, forecasting |
| **Generative AI** | Creates **new, original content** by learning patterns from existing data | Text, images, music |

### Traditional ML workflow

Two stages: **training** (experiment, labeled data + algorithm → trained model) and **serving/inference** (deploy model to an **endpoint**, send prediction requests).

```
 TRAINING STAGE                                SERVING STAGE
 ┌──────────────┐   ┌──────────┐     ┌────────────┐   ┌────────────┐
 │ Experimentation│─►│ Training │ ──► │ Deployment │─► │ Prediction │
 └──────────────┘   └──────────┘     └────────────┘   └────────────┘
 ═══════════ EVALUATION and MONITORING across all phases ═══════════
 ═══ GOVERNANCE of data and artifacts across the whole lifecycle ═══
```

Supporting practices the industry standardised:

| Artifact | Stored in |
|---|---|
| Trained models | **Model registry** |
| Features | **Feature store** |
| Pipeline DAGs (directed acyclic graphs) | **Template gallery** |

> **Takeaway:** Predictive MLOps = 4 phases (experiment → train → deploy → predict) wrapped by continuous evaluation, monitoring, and governance.

*Key message of the course:* **your existing MLOps investment stays valuable.** Gen AI extends it; it does not replace it. The same platform can serve code generation, text generation, and summarisation.

---

## 2. What changes for Gen AI: the framework

**MLOps for Gen AI** = practices, techniques, and technologies that **extend MLOps principles** to operationalise Gen AI applications.

### Where the lifecycle changes

```
 DISCOVERY & EXPERIMENTATION        CUSTOMISATION         TRAIN / SERVE (as usual)
 ┌───────────────────────────┐     ┌────────────────┐     ┌──────────────────────┐
 │ • Pre-trained MODEL       │ ──► │ Customise and  │ ──► │ Training, deployment,│
 │   DISCOVERY (don't build  │     │ TUNE a         │     │ prediction           │
 │   from scratch)           │     │ pre-trained    │     └──────────────────────┘
 │ • DIRECT PREDICTION       │     │ model          │
 │   SHORTCUT: prompt it in  │     └────────────────┘
 │   natural language now    │            ▲
 └───────────────────────────┘     CURATED DATA (domain data, not massive labeled sets)

 Plus: governance of NEW artifacts  |  new EVAL/MONITOR metrics  |  ENTERPRISE DATA integration
```

| Area | Predictive ML | Generative AI |
|---|---|---|
| **Experimentation** | Build models from scratch | **Discover and reuse pre-trained models** |
| **Fast path to a result** | Must train first | **Direct prediction shortcut**: send a natural-language prompt straight away |
| **Training stage** | Train from scratch | **Customise and tune** a pre-trained model |
| **Data** | Massive **labeled** datasets | **Curated** datasets, so data lake and warehouse strategy must be rethought |
| **Artifacts to govern** | Models, features, pipelines | **+ tuning jobs, adapter layers, embeddings** (and prompts) |
| **Evaluation metrics** | Accuracy, precision | **Fluency, factuality, brand reputation** |
| **Knowledge** | Fixed by training data | Foundation models are **static** (limited to pre-training knowledge), so connect **enterprise data** |

*Clarification:* the source lists "curated data" twice. They are two different ideas:
1. **Curated data** = domain-specific datasets used to **tune** the model (training side).
2. **Enterprise data integration** = giving the model access to **your live/internal data at inference time** (grounding, vector search, extensions; see §4). Doing so adds new evaluation and monitoring complexity.

> **Takeaway:** Gen AI MLOps = discover → customise → serve, with prompts as a shortcut, new artifacts to govern, new quality metrics, and enterprise data plugged in.

---

## 3. Five challenges → mitigations

| # | Challenge | Why it is hard | Mitigation on Agent Platform |
|---|---|---|---|
| 1 | **Infrastructure** for large pre-trained multi-task models | Needs lots of compute (**GPUs/TPUs**) for training, experimenting, deploying | **Model Garden**, **Agent Studio**, **Managed Training**, **Inference** (fully managed) |
| 2 | **Customising and tuning** models | Needs supervised tuning, RLHF, and data curation | Supervised tuning, **RLHF**, data curation |
| 3 | **Managing new artifacts** (prompts, embeddings, adapter layers, tuning jobs) | New asset types need versioning and lineage | Prompt tools, **Feature Store**, **Model Registry**, **Pipelines** |
| 4 | **Evaluating and monitoring** unstructured outputs | Outputs are big chunks (images, chat logs), not one number | **Gen AI Evals**, **safety scores**, **recitation checking** |
| 5 | **Using enterprise data** | Foundation models are static | **Embeddings + Vector Search**, **grounding**, **extensions** |

### 3.1 Infrastructure (challenge 1)

| Service | Role |
|---|---|
| **Model Garden** | **Discovery gateway**: Google, open-source, and third-party models. Pick a model for your use case **without collecting/labelling millions of examples**; do classification, summarisation, entity extraction via **simple text instructions** instead of a dedicated pipeline per task. Provides code examples for training on GPUs/TPUs; many models support **distributed training** |
| **Agent Studio** | **Fully managed** environment to test sample prompts, design custom prompts, and **fine-tune with your own data**; Google runs the infrastructure |
| **Managed Training / Inference** | Fully managed compute: upload data; it runs and scales with **high availability** |

### 3.2 Customising and tuning (challenge 2)

| Technique | Use when | Example |
|---|---|---|
| **Supervised tuning** | The task has a **well-defined output** | Classification, labelled Q&A |
| **RLHF** (reinforcement learning from human feedback) | The "right" output is **hard to define**, so humans rate outputs | Summarising chat conversations |
| **Data curation** | Augment generic pre-training data with **domain-specific** data | Legal or medical corpus |

RLHF is offered for **Gemini** and **open-source models** (the course cites Llama 2; check the docs for the current list).

### 3.3 Managing the new artifacts (challenge 3)

| Artifact | What it is | Managed with |
|---|---|---|
| **Prompts** | Instructions that guide the model | Prompt-analysis/debug tools, e.g., **LangChain**, **Weights & Biases** (instructions + context + output indicators) |
| **Embeddings** | **Dense vectors** representing text/images/video; power search, recommendations, similarity matching | **Feature Store** (stores, manages, serves embeddings alongside normal features) |
| **Adapter layers** | Small set of tuned weights (**tens of MB**) passed to the foundation model **together with your prompt** at inference | **Model Registry** (holds predictive and generative models, including adapters) |
| **Tuning jobs** | Runs that optimise a model | **Pipelines**: orchestration, **reproducibility, lineage tracking (dataset → model)**, metadata; works across Spark, XGBoost, TensorFlow, PyTorch |

> **Takeaway:** Existing MLOps tools still apply: Feature Store for embeddings, Model Registry for adapters, Pipelines for tuning jobs.

---

## 4. Evaluation, monitoring, and enterprise data

### 4.1 Evaluation: Gen AI Evals

*Why it is hard:* outputs are unstructured and long, so you cannot just compare one label.

**Metric ideas:** fluency, factuality, brand reputation. The actual metric depends on the **task**; check official docs for supported tasks and metrics.

```
 Evaluation dataset: pairs of (PROMPT, GROUND TRUTH = ideal response)
        │
        ▼  prompt ──► model ──► generated OUTPUT
                                    │
        ground truth ───────────────┤
                                    ▼
                         compute evaluation METRICS
```

**Worked example (illustrative): a 3-pair eval set, using exact-match as the metric**

| # | Prompt | Ground truth | Model output | Match? |
|---|---|---|---|---|
| 1 | Capital of France? | Paris | Paris | ✔ |
| 2 | 12 × 3? | 36 | 36 | ✔ |
| 3 | Largest ocean? | Pacific | Atlantic | ✘ |

Score = 2 / 3 = **0.67**. *Cross-check:* 2 matches + 1 miss = 3 pairs; 0.67 × 3 ≈ 2 ✔. (Real Gen AI tasks such as summarisation use richer metrics than exact match.)

### 4.2 Monitoring: what is new

| Gen AI monitoring | Purpose |
|---|---|
| **Safety scores** | Agent Platform auto-generates a payload with safety scores in **more than 10 categories** for each input and response; set **thresholds per use case** |
| **Recitation checking** | Scans model output against existing **code repositories and web articles**. On significant matches it can **block** them or **return a citation/recitation** in the API output if the match exceeds a character threshold |

**Illustrative threshold:** a kids' app might block any response with a safety score ≥ 0.5, while an internal tool might use 0.8. The point is that **thresholds are per use case**.

*Contrast:* these differ from **traditional** monitoring such as **training-serving skew** and **feature / prediction drift**. Gen AI monitoring *adds* content-quality and authenticity checks.

> **Takeaway:** Gen AI evaluation = prompt/ground-truth pairs scored by task metrics; monitoring adds safety scores and recitation checks on top of drift/skew.

### 4.3 Enterprise data: three capabilities

| Capability | What it does |
|---|---|
| **Embeddings + Vector Search** | Embeddings APIs (**text and image** projected into the **same vector space** so meaning can be compared) + a **fully managed vector database**; **Feature Store** holds embeddings next to existing ML features; integrates with third-party tools such as LangChain |
| **Grounding** | Generate responses **based on enterprise data**, which **reduces hallucination risk** |
| **Extensions** | Author/manage extensions that connect the model to **real-time data and real-world actions** |

```
 user question ─► embed ─► VECTOR SEARCH over enterprise data ─► relevant chunks
                                                                   │
                                  (GROUNDING) prompt + chunks ─► model ─► answer based on your data
                                  (EXTENSIONS) can also call live systems / take actions
```

**Worked example (illustrative): why same-vector-space matters (cosine similarity)**

| Item | 2-D embedding |
|---|---|
| a = "refund policy" | [0.9, 0.1] |
| b = "return an item" | [0.8, 0.2] |
| c = "pizza menu" | [0.1, 0.9] |

- cos(a, b) = (0.9·0.8 + 0.1·0.2) / (‖a‖‖b‖) = 0.74 / (0.906 × 0.825) ≈ **0.99** (very similar)
- cos(a, c) = (0.9·0.1 + 0.1·0.9) / (‖a‖‖c‖) = 0.18 / (0.906 × 0.906) ≈ **0.22** (unrelated)

*Cross-check:* the two related phrases score near 1 and the unrelated pair near 0, so search by meaning works. (Real embeddings have hundreds of dimensions.)

> **Takeaway:** Embeddings + vector search find relevant enterprise data, grounding makes answers rely on it, and extensions let the model act on live systems.

---

## 5. Product renames (source uses the new names; older material uses the old ones)

| Older name | Current name |
|---|---|
| **Vertex AI** | **Gemini Enterprise Agent Platform** ("Agent Platform") |
| **Vertex AI Studio** | **Agent Studio** |
| Vertex AI Prediction / Inference | Agent Platform Inference |
| Vertex AI Pipelines | Agent Platform Pipelines |
| Vertex AI Endpoints | Agent Platform Endpoints |
| Vertex AI Agent Builder | Now part of Agent Platform |
| "Gen AI evaluation service" | **Gemini Enterprise Agent Platform Evals** ("Gen AI evals") |

Names that stayed the same in the source: **Model Garden**, **Feature Store**, **Model Registry**, **Vector Search**, **Managed Training**.

---

## Common confusions

| Confusion | Reality |
|---|---|
| "Gen AI needs a brand-new MLOps stack" | It **extends** existing MLOps; Feature Store, Model Registry, and Pipelines still apply |
| "Gen AI still starts with training from scratch" | It starts with **discovering a pre-trained model**; the prompt gives a **direct prediction shortcut** |
| "Need massive labeled data" | Gen AI uses **curated** domain data for tuning |
| "Accuracy/precision are enough to evaluate Gen AI" | Use **fluency, factuality, brand reputation**, plus task-specific Evals |
| "Supervised tuning vs RLHF are interchangeable" | Supervised = **clear target output**; RLHF = **hard-to-define output**, human feedback |
| "Adapter layers replace the foundation model" | They are **small add-ons** passed **with the prompt** to the unchanged base model |
| "Embeddings are only for search" | Also recommendations and similarity matching; stored in **Feature Store** or **Vector Search** |
| "Grounding = fine-tuning" | Grounding supplies **enterprise data at inference**; the model weights are not changed |
| "Gen AI monitoring = drift monitoring" | Adds **safety scores** and **recitation checks**; skew/drift still exist |
| "Vertex AI and Agent Platform are different products" | Agent Platform is the **renamed evolution** of Vertex AI |
