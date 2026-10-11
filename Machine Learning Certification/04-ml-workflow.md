# Module 4 — ML Workflow

> **Naming note:** "Agent Platform" (Feature Store, Pipelines, Workbench, Colab Enterprise) = what older material calls **Vertex AI**.

## Module map

```
1. ML workflow overview   → 3 stages (restaurant analogy), iterative
2. Data preparation       → upload + feature engineering, Feature Store
3. Model development      → train + evaluate: confusion matrix, precision/recall, feature importance
4. Model serving          → deploy (online / batch / edge) + monitor
5. MLOps & automation     → Pipelines (KFP / TFX), components, 3 automation phases
6. Lab                    → AutoML loan-risk model; reading the metrics
7. How machines learn     → neural-network learning: weights, activation, loss, gradient descent
```

---

## 1. ML Workflow overview

```
DATA PREPARATION          MODEL DEVELOPMENT           MODEL SERVING
upload → feature eng.  →  train ⇄ evaluate        →   deploy → monitor
(raw ingredients)         (experiment with recipes)   (serve the meal)
        ▲_______________ iterative: feedback loops back ______________|
        └──────────── MODEL MANAGEMENT spans all stages ─────────────┘
```

- **Iterative, not linear:** during training you may return to the raw data to build better features. During monitoring you may find **data drift** or a sudden accuracy drop and need to revisit data sources or parameters. **MLOps** can automate this.
- **Two ways to run the workflow on Agent Platform:**

| Approach | How | Who |
|---|---|---|
| **AutoML** | No-code UI | Low ML expertise, no coding |
| **Code with Pipelines** | Workbench / Colab Enterprise + Agent Platform Pipelines SDKs | Experienced ML engineers/data scientists automating programmatically |

> **Takeaway:** Prepare data → develop (train ⇄ evaluate) → serve (deploy + monitor), and loop back when quality drops.

---

## 2. Data preparation

**Step 1: upload data.** Sources: Cloud Storage, BigQuery, or local machine. Data can be batch or real-time, structured (tables) or unstructured (images, video). **Quality and quantity** of data decide how well the model learns.

**AutoML mainly supports tabular data**, with three **objectives:** regression, classification, forecasting (vital in retail).

**Step 2: feature engineering.** A **feature** = a factor contributing to the prediction (an independent variable, a column). Feature engineering = processing data before training (peel, chop, rinse the ingredients).

### Feature Store

A **centralized repository to manage, serve and share features.** It aggregates features from BigQuery, serves them **online** (real-time) or **offline** (batch), scales with low latency, and is ready for gen AI (manages and serves **embeddings**, retrieves similar items in real time).

**Online serving steps:**
1. Prepare the data source in **BigQuery**
2. *(Optional)* register sources by creating **feature groups** and features
3. Create a **feature view**: defines which features to copy into the online store
4. Serve the latest feature values online from the feature view

| Benefit | Meaning |
|---|---|
| Shareable | Same features for training and serving; consistency across the organization |
| Reusable | Saves time, avoids duplicate work |
| Scalable | Auto-scales for low-latency serving |
| Easy to use | Simple UI |

> **Takeaway:** Feature Store = one place to define, share and serve features consistently for training (offline) and prediction (online).

---

## 3. Model development

### Setting up training (AutoML)

```
Pick dataset → training OBJECTIVE (by data type) → METHOD (AutoML or custom)
   → target column (supervised) → choose/transform features → budget → Start training
```

AutoML trains and picks the best among thousands of candidates (NAS + transfer learning, Module 3).

### Evaluation: the confusion matrix

A table of **predicted vs actual** values (two-class case):

|  | **Actual positive** | **Actual negative** |
|---|---|---|
| **Predicted positive** | **TP** true positive | **FP** false positive (**Type 1 error**) |
| **Predicted negative** | **FN** false negative (**Type 2 error**) | **TN** true negative |

*Cat example:* predicted cat & is cat = TP; predicted not-cat & isn't = TN; predicted cat but isn't = FP; predicted not-cat but is = FN.

### Precision and recall

| Metric | Looks at | Formula | Question |
|---|---|---|---|
| **Recall** (true positive rate) | All **actual positives** | **TP / (TP + FN)** | Of all real positives, how many did we catch? |
| **Precision** | All **predicted positives** | **TP / (TP + FP)** | Of all we called positive, how many were right? |

**Worked cross-check (spam filter, illustrative):** TP=40, FP=10, FN=20, TN=130.
Precision = 40/(40+10) = **80%**. Recall = 40/(40+20) = **66.7%**.

**They trade off.** Spam example:
- Catch as much spam as possible → prioritize **recall** (accept some good mail flagged).
- Only block what's definitely spam, never real mail → prioritize **precision**.

**Confidence threshold** (shown on the precision-recall curve):

| Threshold | Precision | Recall |
|---|---|---|
| Higher | ↑ | ↓ |
| Lower | ↓ | ↑ |

### Feature importance

A bar chart showing how much each feature contributes to predictions (longer bar / larger value = more important). Use it to decide which features to keep.

> **Takeaway:** Confusion matrix → precision (quality of positives) and recall (coverage of positives); the threshold moves you along the trade-off.

---

## 4. Model serving

Two steps: **deployment** and **monitoring**. Model management runs throughout so data scientists focus on *what*, not *how*.

### Deployment options

| Option | How | Best when |
|---|---|---|
| **Online (real-time) prediction** | Deploy the model to an **endpoint** (required) | Immediate, low-latency results (instant recommendations) |
| **Batch prediction** | Request a prediction job directly from the **model resource**; **no endpoint needed** | No immediate response needed (e.g. campaign every two weeks) |
| **Edge deployment** | Model runs **off cloud** on devices | Lower latency, privacy, offline use (e.g. factory camera object detection) |

Deploy through the Agent Platform UI or via API code.

### Monitoring

Agent Platform **Pipelines** automates, monitors and governs ML systems, orchestrating the workflow **serverlessly**, and can trigger warnings when predefined thresholds are crossed.

> **Takeaway:** Real-time predictions need an endpoint; batch predictions don't; edge keeps the model on-device.

---

## 5. MLOps and workflow automation

**MLOps** = ML development + operations, applying **DevOps** principles to ML. It targets the hard part: **building an integrated ML system and operating it in production**, because both data and code constantly change. Goal: automate and monitor every step for **CI (continuous integration), CT (continuous training), CD (continuous delivery)**.

### Pipelines toolkit

- Backbone: **Agent Platform Pipelines**, supporting **Kubeflow Pipelines (KFP)** and **TensorFlow Extended (TFX)**.

| Use | When |
|---|---|
| **TFX** | You already use TensorFlow on **terabytes of structured data** |
| **KFP** | Otherwise (good alternative) |

### Pipeline structure

```
EXPERIMENT / DEV / TEST environment            STAGING / PRE-PROD / PROD environment
data extract → analyze → prepare               serve model
   → train → evaluate → validate  ──► Model Registry ──►  predict + monitor
```

- A **component** = self-contained code that does **one task** (like a function). One responsibility per component promotes reuse.
- Use **pre-built Google components** first; write a **custom component** only for special needs (e.g. a custom deployment threshold).

### Three automation phases

| Phase | State |
|---|---|
| **0** | No MLOps; manual **GUI** workflow (e.g. AutoML). Critical: build the workflow by hand before automating |
| **1** | Build **components** with the Pipelines SDKs (e.g. a training pipeline) |
| **2** | **Integrate components** into the full workflow, achieving CI, CT, CD |

### Example: AutoML pipeline for classifying beans (7 types)

Build: plan components → build custom ones → assemble with pre-built ones. Then **compile** (`compiler.Compiler().compile()`) and **define/run** the pipeline job.

| Component | Role |
|---|---|
| `TabularDatasetCreateOp` | Create tabular dataset (from Cloud Storage or BigQuery) |
| `AutoMLTabularTrainingJobRunOp` | Run the AutoML training job |
| `classification_model_eval_metrics` (**custom**) | Compare metrics to a threshold; decide deploy vs **retrain** |
| `EndpointCreateOp` | Create an endpoint |
| `ModelDeployOp` | Deploy the model to the endpoint |

Agent Platform provides **templates** (e.g. classification/regression for tabular AutoML) and **visualizes** the pipeline and artifacts.

> **Takeaway:** MLOps = pipelines of single-purpose components that automatically train, evaluate (against a threshold), deploy or retrain.

---

## 6. Lab: AutoML loan-risk model

Predict loan risk with no code. Dataset: **2,050 data points** (AutoML needs **at least 1,000**). Practices all three stages.

### Reading the lab's confusion matrix (positive = *repays the loan*)

| Rate | Value | Formula | Meaning |
|---|---|---|---|
| True positive rate (= recall) | 100% | TP/(TP+FN) | Every good borrower is identified |
| True negative rate | 87% | TN/(FP+TN) | 87% of defaulters correctly identified |
| False positive rate | 13% | FP/(FP+TN) | 13% of **defaulters wrongly predicted to repay** |
| False negative rate | 0% | FN/(TP+FN) | No good borrower wrongly rejected |

> **Correction to the lecture wording:** it describes the 13% FPR as good borrowers wrongly labeled defaulters. By its own formula and with *positive = repays*, FP means a **defaulter predicted to repay**, so the real risk is **approving loans that default**. Wrongly rejecting good borrowers would be the **false negative** (0% here).

### Threshold extremes (lab)

| Threshold | Recall | Precision | Business meaning |
|---|---|---|---|
| **0** | 100% | 50% | Predict *everyone* repays; only half actually do, so you recover about half your investment |
| **1** | 1% | 100% | Nearly everyone predicted to repay does, but you approve only ~1% of applicants (large business loss) |

Pick an **appropriate threshold** between the extremes.

---

## 7. How machines learn (neural-network basics)

**Family:** ANN (basic) → DNN, CNN, RNN, LLMs. ANN = **input layer, hidden layer(s), output layer** of neurons connected by **weighted** links.

### The 7 learning steps

```
1 Weighted sum (Σ input×weight [+bias])
2 Activation function on hidden layer
3 Weighted sum for output layer
4 Activation function on output  → ŷ (predicted)
5 Cost/loss: compare ŷ with y (actual)
6 Backpropagation: adjust weights/biases if error is large (gradient descent decides how)
7 Iterate: repeat until cost stops decreasing
```

- **Weights** store what the network has learned.
- **Epoch** = one complete pass of the process (steps 1 → 6). Training repeats for the number of epochs set.

### Why activation functions? (non-linearity)

Without them, any number of layers collapses into a **linear function** of the inputs (ŷ = a·x1 + b·x2), so the hidden layers add nothing. Activation functions add **non-linearity**.

| Function | Output range | Use |
|---|---|---|
| **ReLU** | 0 if negative, else the value | Common in hidden layers |
| **Sigmoid** | 0 to 1 | **Binary** classification, logistic regression (e.g. spam or not) |
| **Tanh** | −1 to +1 | Shifted sigmoid |
| **Softmax** | 0 to 1, outputs **sum to 1** (probability distribution) | **Multi-class** classification |

Layers can use different functions (e.g. ReLU hidden + softmax output).

### Loss, cost and common choices

| Term | Meaning |
|---|---|
| **Loss function** | Error for a **single** training instance |
| **Cost function** | Error across the **whole** training set |
| **MSE** (mean squared error) | Average of squared (ŷ − y); typical for **regression** (linear regression) |
| **Cross-entropy** | Difference between predicted and actual probability distributions; typical for **classification** (logistic regression) |

### Gradient descent: finding the bottom of the cost surface

1. **Direction:** use the **derivative** of the cost function. Negative slope → move right/down; positive → move left; repeat.
2. **Step size = learning rate** (a hyperparameter): too small → training takes too long; too large → bounces around and may never converge.

### Parameters vs hyperparameters

| | Parameters | Hyperparameters |
|---|---|---|
| Examples | **Weights and biases** | Number of layers/neurons, activation functions, **learning rate**, **epochs** |
| Set by | **Learned by the machine** (you only set initial values) | **You** (before training); AutoML selects them automatically |
| Role | Hold what was learned | Determine **how** the machine learns |

> **Takeaway:** The network predicts, measures error with a cost function, backpropagates, and uses gradient descent (guided by the learning rate) to adjust weights over many epochs.

---

## Common confusions

| Mix-up | Correct |
|---|---|
| Workflow is linear | It's **iterative** (data drift or accuracy drops send you back) |
| Batch prediction needs an endpoint | **No**; only online/real-time prediction does |
| Precision vs recall | Precision uses **FP** (TP/(TP+FP)); recall uses **FN** (TP/(TP+FN)) |
| False positive = Type 1; false negative = Type 2 | Correct, and "positive" is whatever the model calls the target class (check the definition per problem) |
| Raising the threshold raises recall | Raises **precision**, **lowers** recall |
| TFX vs KFP | TFX for TensorFlow on terabytes of structured data; KFP otherwise |
| Phase 0 of MLOps is wasted effort | It's **critical**: build manually (GUI) before automating |
| Parameters = hyperparameters | Parameters are **learned** (weights, biases); hyperparameters are **set by you** |
| Loss vs cost | Loss = one instance; cost = entire training set |
| Sigmoid vs softmax | Sigmoid = **binary**; softmax = **multi-class** |
| Epoch | One full pass through steps 1–6, not a single weight update |
