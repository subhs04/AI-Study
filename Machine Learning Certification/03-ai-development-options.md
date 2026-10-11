# Module 3 — AI Development Options

> **Naming note:** the course says "Agent Platform" where older material says **Vertex AI** (the transcript itself notes "Vertex AI, now Agent Platform"). Expect either name in practice questions.

## Module map

```
1. Options overview     → 4 ways to build an ML model; how to choose
2. Agent Platform       → the unified platform that hosts them
3. AutoML               → no-code: how it works (NAS, transfer learning, ensemble)
4. Pre-trained APIs     → low-code: use Google's ready-made models
5. Custom training      → code-based DIY: containers, notebooks, TensorFlow, JAX
6. Lab                  → Natural Language API in practice
```

These options apply to **both gen AI and predictive AI** projects.

---

## 1. AI Development Options

### The spectrum

```
No-code ───────────────► Low-code ───────────────► Code-based
Gemini Enterprise app    Pre-trained APIs          BigQuery ML, ADK
Gemini Ent. for CX       Agent Platform AutoML     Custom training (DIY)
(out-of-the-box)         (no-to-low code)
```

### Comparing the four ML-building options

| | **Pre-trained APIs** | **BigQuery ML** | **Agent Platform AutoML** | **Custom training** |
|---|---|---|---|---|
| Data types | Tabular, image, text, video, **audio** | **Tabular + semi-structured (JSON)** only | **Tabular + image** | Tabular, image, text, video |
| Training data needed | **None** | Large amount | (build with your own data) | Large amount |
| ML / coding expertise | **Low** | Know **SQL** | **Low** | **Highest** |
| Tune hyperparameters yourself? | No | **Yes** | No (automated for you) | **Yes** |
| Time to train | **None** (already trained) | Project-dependent | Project-dependent | Usually **longest** (built from scratch) |

Budget also matters (check Google Cloud pricing).

### Which one when?

| Situation | Choose |
|---|---|
| Little ML experience, no plan to train your own model; common perceptual tasks (vision, video, language) | **Pre-trained APIs** |
| Team knows SQL and data is already in BigQuery | **BigQuery ML** |
| Custom model on your own data, minimal coding, focus on the business problem | **Agent Platform AutoML** |
| ML engineers/data scientists want **full control** of the workflow | **Custom training** (Workbench or Colab Enterprise) |

> **Takeaway:** The more control you want, the more expertise, data and time you need; pre-trained APIs are the opposite end (no data, no training).

*Cross-check:* "No training data and no ML team" → pre-trained APIs (they are the only option needing zero training data). "Highest control and customization" → custom training.

*Course inconsistency:* this lesson lists AutoML as **tabular + image**, but Module 4 says AutoML "now mainly supports tabular data". Treat tabular as the safe core and check current docs for other types.

---

## 2. Agent Platform (the unified platform)

### Why it exists

- Getting ML to production is hard: **scalability, monitoring, CI/CD/CT**. Per Gartner (quoted in the course), only about **half** of enterprise ML projects get past the pilot phase.
- Ease-of-use gaps: tools require advanced coding, and without a unified workflow, tools are hard to find.

### "Unified" means two things

1. **End-to-end ML pipeline in one place:**

```
Data readiness ► Feature readiness ► Training + hyperparameter tuning ► Deployment + monitoring
(upload from Cloud    (create features,        (try models,                   (pipelines, automatic
 Storage, BigQuery,    share via Feature Store)  tune hyperparameters)          monitoring/improvement = MLOps)
 or local)
```

2. **Covers both gen AI and predictive AI** (Agent Studio / ADK for gen AI; AutoML / custom training for predictive).

### Building predictive models on Agent Platform

| | **AutoML** | **Custom training** |
|---|---|---|
| Style | No-code UI | Code-based |
| For | Data scientists who want to focus on the business problem | Data scientists/ML engineers wanting more control |
| Tools | Point-and-click UI | **Workbench** or **Colab Enterprise** notebooks |

Convenience: you can write **SQL in Workbench** to connect BigQuery and Agent Platform.

### The four S's (benefits)

| S | Meaning |
|---|---|
| **Seamless** | Smooth experience from data upload to production |
| **Scalable** | MLOps monitors and scales storage and compute automatically |
| **Sustainable** | Artifacts and features are reusable and shareable |
| **Speedy** | Models need significantly fewer lines of code than competitors |

---

## 3. Agent Platform AutoML

**AutoML** = automated machine learning: automates developing and deploying an ML model, from preprocessing to training and deployment, via a **no-code** point-and-click UI. (Announced January 2018; embedded in Vertex AI, now Agent Platform, since 2021.)

### Four phases

```
1. Data processing ─► 2. Search best models + tune ─► 3. Assemble best models ─► 4. Prediction
   (auto-convert         (neural architecture search    (ensemble of top ~10,       (serve the ensemble)
    numbers, datetime,    + transfer learning)           depends on budget)
    text, categories,
    arrays, nested fields)
```

### The two key technologies (phase 2)

| Technology | What it does |
|---|---|
| **Neural architecture search (NAS)** | Tries different architectures/models, compares their performance, and finds the best while automatically tuning parameters |
| **Transfer learning** | Reuses models **pre-trained** on large datasets as the starting point for new problems, so you reach higher accuracy with **much less data and compute** than learning from scratch |

*Analogy for transfer learning:* LLMs are pre-trained for general language tasks, then tailored to retail, finance, etc. with a relatively small field dataset (same idea as Module 2's pre-train/fine-tune).

### Phase 3: ensembling

AutoML doesn't rely on one model; it uses the **top models** (typically about 10, depending on training budget). Assembly can be as simple as **averaging predictions**, which greatly improves accuracy.

> **Takeaway:** AutoML automates feature processing, architecture search (NAS), hyperparameter tuning and model ensembling, with transfer learning making it fast and data-efficient.

---

## 4. Pre-trained APIs

**Why:** good custom models need **hundreds of thousands of records**. If you don't have that, call a model Google already trained.

**API analogy:** like a travel power adapter. You only need to know **which API, which parameters, what format**, not how the model was trained or deployed (like calling a predefined function).

### Gemini API example (4 steps, Python SDK)

```python
genai.configure(api_key="YOUR_API_KEY")            # 1. authenticate
model = genai.GenerativeModel("gemini-2.5-flash")  # 2. choose the model
response = model.generate_content("What are the three largest countries by area?")  # 3. send prompt
print(response.text)                               # 4. read the response
```

(Model name is just the lecture's example; versions change.)

### API families on Google Cloud

| Family | Examples |
|---|---|
| **Generative AI APIs** | Foundation model APIs (multimodal **Gemini API**), Agent Platform for building/deploying agents |
| **Machine learning APIs** | Agent Platform API: train, monitor, tune an ML model with minimal expertise |
| **Others** | Speech, image, document, conversation APIs, many of which **Gemini** (multitask, multimodal) can replace |

### Natural Language API (analyzes text)

| Feature | Method | Gives you |
|---|---|---|
| **Entity analysis** | `analyzeEntities` | Subjects in the text (e.g. Google = company, Mountain View = location) |
| **Sentiment (whole text)** | `analyzeSentiment` | Emotion at the **document** level |
| **Sentiment (per entity)** | `analyzeEntitySentiment` | Emotion for each **subject** |
| **Syntax** | `analyzeSyntax` | Linguistic structure, relationships between words |
| **Classification** | `classifyText` | Categories/topics, like a tag |

> **Takeaway:** Pre-trained APIs need no training data and no model-building; you send a request and use the response.

---

## 5. Custom Training

**When:** your needs exceed AutoML's automation and you need **full control** of model architecture, frameworks and training logic.

### Step 1: choose the training environment

| Option | Analogy | Use when |
|---|---|---|
| **Pre-built container** | Furnished kitchen | You need standard platforms (Python, TensorFlow, PyTorch) and don't care about the underlying infrastructure |
| **Custom container** | Empty room | You must define the environment, machine type, disks yourself |

### Step 2: choose the coding tool

| Tool | What it is |
|---|---|
| **Agent Platform Workbench** | Jupyter notebook in a single dev environment supporting explore → train → deploy |
| **Colab Enterprise** | Colab-style notebooks integrated into Agent Platform; familiar for data scientists |

### Step 3: use ML libraries

Pre-written, open-source code so you don't start from scratch: **TensorFlow**, **scikit-learn**, **PyTorch**.

**TensorFlow's abstraction layers (hierarchy):**

```
High-level APIs (e.g. Keras)    ← most used; hide details, automate training
TensorFlow model libraries      ← building blocks: network layers, evaluation metrics
Low-level TensorFlow APIs       ← custom operations, core numeric functions
Hardware                        ← CPU, GPU, TPU
```

Agent Platform fully hosts TensorFlow at every level as a managed service.

### `tf.keras` in three steps

| Step | Method | What you specify |
|---|---|---|
| 1. **Create** | `tf.keras.Sequential` | Piece together the layers (e.g. a three-layer network) |
| 2. **Compile** | `compile` | **Loss function** (how performance is measured) and **optimizer** |
| 3. **Train** | `fit` | Input data, expected output, and number of **epochs** |

Then deploy and predict. **JAX** is another Google high-performance numerical computation library, flexible and used in research and production.

> **Takeaway:** Custom training = choose a container, a notebook tool and a library (e.g. Keras: create → compile → fit) for maximum control.

---

## 6. Lab: Natural Language API

Using any API, think of **ordering at a deli**: the **features** are the menu, the **input** is your order, the **output** is the response.

- **REST API** with **JSON** requests and responses.
- **Request fields:** document `type` (e.g. PLAIN_TEXT), `language` (e.g. EN), `content` (text itself or a Cloud Storage location), `encodingType` (e.g. UTF8).
- **Calling it:** `cURL` (command-line transfer tool) or Python/Java SDKs. Example: call `analyzeEntities` with `request.json`, save the output to `result.json`, review with `cat result.json`.
- Production use means **embedding the API in code**, not just using the UI.

---

## Common confusions

| Mix-up | Correct |
|---|---|
| AutoML lets you tune hyperparameters | You **can't** manually tune them; AutoML automates it. BigQuery ML and custom training let you experiment |
| Pre-trained APIs need some training data | **None** |
| Custom training is fastest to build | Usually the **longest** (from scratch) |
| BigQuery ML handles images and text | Only **tabular + semi-structured** (JSON) |
| AutoML = one best model | An **ensemble of top ~10** models |
| NAS vs transfer learning | NAS = **search** for the best architecture; transfer learning = **reuse** pre-trained models to speed up and save data |
| Pre-built vs custom container | Pre-built = ready environment (Python/TensorFlow/PyTorch); custom = you define everything |
| Workbench vs Colab Enterprise | Both are notebooks: Workbench = Jupyter in one dev environment; Colab Enterprise = Colab-style integrated in the platform |
| `analyzeSentiment` vs `analyzeEntitySentiment` | Whole **document** vs each **entity/subject** |
