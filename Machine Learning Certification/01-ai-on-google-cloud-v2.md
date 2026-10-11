# Module 1 — AI on Google Cloud

> **Naming note:** this course uses the newer product names (*Agent Platform*, *Agent Studio*). Older docs and exam material may call the same things *Vertex AI* / *Vertex AI Studio*. If a name looks unfamiliar on a practice question, check Google's current documentation.

## Module map

```
1. Overview        → WHAT kinds of AI problems exist, and Google's 3-layer stack
2. AI Infra        → the bottom layer: compute, storage, data-to-AI workflow
3. AI Models       → ML vocabulary: supervised vs unsupervised and their model types
4. BigQuery ML     → apply (3) inside (2): build a model with plain SQL
5. Lab walkthrough → the 5 phases in practice; training vs evaluation loss; precision/recall
```

---

## 1. AI on Google Cloud (overview)

### Predictive AI vs Generative AI

| | Predictive AI | Generative AI |
|---|---|---|
| Also called | Traditional / discriminative AI | Gen AI |
| What it does | Uses historical data to **classify** or **predict** outcomes | **Creates** new content (text, image, video, code) and takes action |
| Examples | Sales forecasting, traffic prediction for route optimization, churn prediction | Customer responses, marketing campaigns, summaries |
| Use when | Forecasting / prediction | Multimodal content generation, automation |

**Simplified decision tree:** forecasting or prediction → predictive. Content creation or automation → generative. Often the best answer is **both**.

**Combining them (the predictive output feeds the gen AI prompt):**

| Predictive step | Generative step |
|---|---|
| Forecast customer churn | Chatbot that helps the sales team explore the predictions |
| Identify customer segments | Personalized marketing content for each segment |

> **Takeaway:** Predictive AI analyzes and predicts; generative AI creates and acts, and they work best chained together.

### Google's three-layer AI stack

```
┌─────────────────────────────────────────────────────────────┐
│ APPLICATIONS & SOLUTIONS  (business users / analysts)       │
│ Out-of-the-box tools to prototype quickly, no code needed   │
├─────────────────────────────────────────────────────────────┤
│ AI DEVELOPMENT  (developers, engineers, data scientists)    │
│ Agent Platform: design → deploy; powered by Gemini;         │
│ integrates with data products such as BigQuery              │
├─────────────────────────────────────────────────────────────┤
│ AI INFRASTRUCTURE                                           │
│ Compute, network, storage                                   │
└─────────────────────────────────────────────────────────────┘
```

> **Takeaway:** Infrastructure at the bottom, development platform in the middle, no-code apps on top. Pick the layer that matches the user's technical skill.

*Why Google (context only):* long history of AI in its own products (Search, Maps, Workspace), leadership in AI/ML innovation (Gemini), and a responsible-AI approach.

*Course roadmap:* Module 1 infra and data tools → Module 2 gen AI → Module 3 options for training ML models → Module 4 build an ML model end to end.

---

## 2. AI Infrastructure

### Three infrastructure layers

```
┌─────────────────────────────────────────────────────┐
│ DATA & AI PRODUCTS   ingest, store, process, deliver│
│                      insights, pipelines, ML models │
├─────────────────────────────────────────────────────┤
│ COMPUTE  |  STORAGE   ← decoupled, scale separately │
├─────────────────────────────────────────────────────┤
│ NETWORKING & SECURITY                               │
└─────────────────────────────────────────────────────┘
```

**Decoupled compute and storage** means each scales independently based on need. This is a key difference from desktop computing.

> **Takeaway:** Cloud decouples compute from storage so each scales on its own.

### Compute options: control vs convenience

| Service | Model | Control |
|---|---|---|
| **Compute Engine** | Virtual machines, like managing a physical server | Highest control |
| **GKE** (Google Kubernetes Engine) | Containerized apps with orchestration | Medium |
| **Cloud Run** | Serverless, Google manages the infrastructure | Most convenient |

### Hardware: CPU vs GPU vs TPU

| Chip | Type | Note |
|---|---|---|
| CPU | General-purpose | May no longer scale to AI demand |
| GPU | General-purpose (graphics origin) | Same limitation, at larger scale |
| **TPU** | **Domain-specific** (Google custom, introduced 2016) | Built for ML workloads such as **matrix multiplication**; faster and more energy-efficient for AI/ML |

> **Takeaway:** TPUs are Google's custom chips specialized for ML math, trading generality for efficiency.

### Storage: choose by data type

| Data type | Choice |
|---|---|
| **Unstructured** (documents, images, audio) | **Cloud Storage** |
| **Structured** (tables, rows, columns), analytics | **BigQuery** (flagship data warehouse) |
| Structured, transactional / relational | AlloyDB for PostgreSQL, Cloud SQL, Spanner |
| NoSQL | Bigtable, Firestore |

**BigQuery is versatile:** built for structured data, optimized for **semi-structured** (JSON), and can even query **unstructured** data (logs, images in Cloud Storage) through an **external table**, which gives that data a structured reference.

### Data-to-AI workflow

```
 INGEST & PROCESS  ──►  STORE & ANALYZE  ──►  ACTIVATE WITH AI
```

| Stage | Tools |
|---|---|
| Ingest & process (batch + real-time) | Pub/Sub, Dataflow, Managed Service for Apache Spark, Cloud Data Fusion |
| Store & analyze | Cloud Storage; BigQuery, AlloyDB, Cloud SQL, Spanner (SQL); Bigtable, Firestore (NoSQL); **Looker** (visualization) |
| Activate with AI | **Agent Platform**: Agent Studio, AutoML, Notebooks. Train predictive models or use gen AI |

**Integration points to remember:**
- BigQuery has **embedded SQL commands to train ML models** (BigQuery ML, lesson 4).
- Notebooks in Agent Platform can pull data from BigQuery with SQL.

> **Takeaway:** Data flows ingest → store/analyze → activate with AI, and BigQuery and Agent Platform are the two anchor products.

---

## 3. AI Models (ML foundations)

### How the terms nest

```
┌ Artificial Intelligence: anything mimicking human intelligence (robots, self-driving cars)
│  ┌ Machine Learning: computers learn without being explicitly programmed
│  │  ┌ Deep Learning: adds layers between input and output (deep neural networks)
│  │  │  ┌ Generative AI: foundation models (e.g. LLMs) that create content
```

- **ML vs traditional programming:** ML learns patterns from data, while traditional code is told explicitly what to do.
- ML mainly = **supervised + unsupervised**.

> **Takeaway:** AI ⊃ ML ⊃ deep learning ⊃ gen AI, with foundation models like LLMs being a type of deep learning.

### Supervised vs Unsupervised

| | Supervised | Unsupervised |
|---|---|---|
| Data | **Labeled** (each example has the answer) | **Unlabeled** |
| Driven by | Task-driven | Data-driven |
| Finds | Identifies a **goal** (predicts the label) | Identifies a **pattern** |
| Example | Label pictures cat/dog, then classify new ones | Group unlabeled dog pictures into breeds |

> **Takeaway:** Labels present → supervised. Labels absent → unsupervised.

### Model-type tree

```
ML
├── SUPERVISED
│   ├── Classification → predicts a CATEGORY   → Logistic regression
│   └── Regression     → predicts a NUMBER     → Linear regression
└── UNSUPERVISED
    ├── Clustering               → group similar points   → K-means
    ├── Association              → find relationships     → Apriori
    └── Dimensionality reduction → reduce # of features   → PCA
```

| Type | Example from course |
|---|---|
| Classification | Cat vs dog; will a customer buy? |
| Regression | Forecast sales; predict customer spending |
| Clustering | Customer segmentation |
| Association | Products bought together, so place them close for a promotion |
| Dimensionality reduction | Combine age, violation history, car type into a simpler insurance-quote rule |

### Worked examples (with cross-checks)

| Scenario | Labeled? | Supervised / Unsupervised | Problem type | Model |
|---|---|---|---|---|
| Predict customer spending from purchase history | Yes (amount spent) | Supervised | Regression (continuous number) | **Linear** regression |
| Find customer segments without stereotypes | No segment labels | Unsupervised | Clustering | **K-means** |
| Will this visitor buy? | Yes (bought / didn't) | Supervised | Classification | **Logistic** regression |

**Cross-check habit:** (1) Is there a label? (2) Is the output a category or a number? (3) Pick the model.

> **Misconception:** logistic regression is a **classification** model despite its name. Linear regression is the **regression** model.

---

## 4. BigQuery ML

### What it is

- BigQuery = **managed storage** + **fast SQL analytics engine**, linked by Google's high-speed internal network, so storage and compute scale independently.
- **BigQuery ML** lets you build, evaluate and use ML models on **tabular data** with SQL, inside the warehouse. It tunes parameters and manages the ML workflow for you.
- It avoids the usual iterative grind (prepare data, try models, tune, retrain, deploy).

### The 5 phases and key commands

```
1 ETL data  →  2 Select/preprocess features  →  3 CREATE MODEL  →  4 ML.EVALUATE  →  5 ML.PREDICT
```

| Phase | What happens | SQL / note |
|---|---|---|
| 1. Load data | ETL into BigQuery if not already there; enrich with SQL **joins**; use connectors for other Google products (e.g. YouTube) | |
| 2. Features | Build the training dataset with SQL. BigQuery ML auto **one-hot encodes** categorical variables (converts categories to numbers) | |
| 3. Create model | Name the model, set the **model type**, define the **label column** | `CREATE MODEL` |
| 4. Evaluate | Check performance on the evaluation set: accuracy, precision, recall | `ML.EVALUATE` |
| 5. Predict | Returns predictions plus the model's **confidence**; the label column appears as **`predicted_<label>`** | `ML.PREDICT` |

Illustrative shape (the label column name is a placeholder):

```sql
CREATE OR REPLACE MODEL `ecommerce.classification`
OPTIONS (model_type = 'logistic_reg',        -- classification
         input_label_cols = ['will_buy'])    -- supervised → label required
AS SELECT ... FROM ...;

SELECT * FROM ML.EVALUATE(MODEL `ecommerce.classification`, (...));
SELECT * FROM ML.PREDICT (MODEL `ecommerce.classification`, (...));
```

### Supported model types (from the course)

- Logistic regression (classification), linear regression (regression)
- K-means (clustering)
- Time series forecasting
- Deep neural networks (DNN)
- BigQuery ML also supports **MLOps** (deploy, monitor, manage models in production)

**Best practice:** start with simple models (logistic/linear regression) and use the results as a **benchmark** before trying complex models (DNN), which cost more time and compute.

> **Takeaway:** BigQuery ML = SQL-only ML on tabular data: `CREATE MODEL` → `ML.EVALUATE` → `ML.PREDICT`, with a label column required for supervised models.

*Lab (referenced in the course):* predict whether a Google Merchandise Store visitor will make future purchases, with Gemini Code Assist helping with SQL.

---

## 5. Lab walkthrough: predict purchases with BigQuery ML

Goal: predict whether a Google Merchandise Store visitor will buy. This applies the 5 phases from lesson 4 (the lab skips the ETL phase because the data is already in BigQuery).

```
PREPARE (SELECT)  ─►  TRAIN (CREATE MODEL)  ─►  EVALUATE (ML.EVALUATE)  ─►  PREDICT (ML.PREDICT)
 label + features      logistic regression       loss / metrics            new, unseen data
```

| Step | What the lab did | Why |
|---|---|---|
| **Prepare** | **Label** = `IF(totals.transactions IS NULL, 0, 1)` (1 = purchase). **Features** = `os`, `is_mobile`, `country`, `pageviews`. `IFNULL(...)` replaces missing values. Training window: Aug 2016 → Jun 2017 | The model needs examples of both success (1) and failure (0) to learn the difference |
| **Train** | `CREATE MODEL ... OPTIONS (model_type='LOGISTIC_REG', input_label_cols=['label'])` | Supervised + yes/no outcome = classification = logistic regression |
| **Evaluate** | `ML.EVALUATE(MODEL ..., TABLE training_data)` plus the training-metrics table (iterations and loss) | Confirms the model learned something useful |
| **Predict** | `ML.PREDICT` on **July 2017** data the model never trained on. Output has `predicted_label`; the lab then summed predictions **by country** to see where buyers are expected | Using the model on new data is the real goal |

### Iteration, training loss, evaluation loss

- **Loss** = a number for how *wrong* the predictions are (lower is better). For logistic regression the underlying measure is log loss / cross-entropy (explained in Module 4).
- **Iteration** = one "study session" in which the model adjusts itself to reduce loss. The training output lists loss per iteration.
- **Training loss** = error on the data the model is learning from.
- **Evaluation loss** = error on a slice BigQuery ML **automatically holds out** (the model never trains on it). For a dataset of this size (10,000 rows) the hold-out is roughly 20%, as far as I know.

| Training loss | Evaluation loss | Diagnosis |
|---|---|---|
| High | High | **Underfitting**: hasn't learned enough |
| Low | High | **Overfitting**: memorized the training data, fails on new data |
| Low | Low, and close to each other | **Good fit** (what the lab showed) |

> **Caveat to cross-check:** the lab ran `ML.EVALUATE` on the `training_data` table, so that query measures performance on data the model **did** train on. It's a sanity check, not a true test. The real "unseen data" test is the July prediction step. The training and evaluation loss pair comes from the automatic hold-out split.

### Precision and recall: do they apply?

**Yes.** This is a **classification** model, so precision and recall apply. For logistic regression, `ML.EVALUATE` reports classification metrics (as far as I know: accuracy, precision, recall, F1, log loss, ROC AUC). The lab focused on the loss curves instead.

| Metric | Question it answers | Formula | High value means |
|---|---|---|---|
| **Precision** | Of those predicted to buy, how many **actually** bought? | TP / (TP + FP) | Few wasted actions on non-buyers |
| **Recall** | Of those who actually bought, how many did the model **find**? | TP / (TP + FN) | Few missed buyers |

*Worked example:* the model flags 100 visitors as buyers and 80 really buy. Precision = 80/100 = **80%**. There were 200 real buyers in total, so recall = 80/200 = **40%**.

*Choose by cost of error:* an expensive physical gift to predicted buyers → prioritize **precision**. A free email nudge → prioritize **recall**. (Full confusion-matrix treatment is in Module 4.)

> **Takeaway:** Compare training vs evaluation loss to spot underfitting or overfitting, and use precision (quality of positives) and recall (coverage of positives) to judge classification, chosen by the business cost of each error.

---

## Common confusions

| Mix-up | Correct |
|---|---|
| Logistic regression = regression | It's **classification**; linear regression is regression |
| Supervised = "has a human supervising" | Supervised = **labeled data** |
| Unstructured data can't be queried in BigQuery | Can, via an **external table** over Cloud Storage |
| GPU and TPU are the same kind of chip | GPU = general purpose; **TPU = domain-specific** to ML |
| Predictive and generative AI are either/or | Often **combined** (predictive output feeds the prompt) |
| Compute and storage scale together | They're **decoupled** and scale independently |
| Low training loss = good model | Only if **evaluation loss** is also low and close; low train + high eval = overfitting |
| Precision and recall are the same | Precision = quality of predicted positives; recall = coverage of actual positives |
| Cloud Run vs Compute Engine | Cloud Run = serverless convenience; Compute Engine = maximum control |
