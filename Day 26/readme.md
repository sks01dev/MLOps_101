# Day 26: Evaluating and Comparing Model Runs in MLflow

### The Big Idea: Side-by-Side Performance Analysis

When developing machine learning systems, you frequently train multiple distinct model architectures (e.g., Logistic Regression, Random Forest, Gradient Boosting) on the exact same dataset to find the most accurate algorithm.

The **MLflow Run Comparison View** allows you to select multiple active training trails and compare their metrics side-by-side using unified bar charts and scatter plots. Once you evaluate the performance trade-offs, you can flag the definitive winner with an explicit run-level tag (`production-candidate: true`). Downstream continuous integration and deployment (CI/CD) tooling can then scan the MLflow server via the API, find this tag, and automatically pick up the correct winning model weights file for packaging.

---

### Problem Statement

An xFusionCorp Industries data scientist has trained three candidate models (`RandomForest`, `GradientBoosting`, `LogisticRegression`) on the same problem space and logged them to the `model-comparison` experiment. Review these candidates side-by-side in the MLflow UI, identify the clear winner based on the $F_1$ score metric, and explicitly mark the winning run so downstream tooling can safely locate and consume it.

---

### The UI Operations Solution

Open the **MLflow UI** dashboard, navigate to the `model-comparison` experiment view, and perform these steps to evaluate and tag the winning model run:

#### 1. Compare the Performance Metrics

* On the experiment landing page, look at the **Model metrics (2)** chart pane or switch to the **Chart View** tab.
* Inspect the $F_1$ score metric attributes across the three matching entries:
* `LogisticRegression` $\rightarrow$ $F_1 = \mathbf{0.78}$
* `RandomForest` $\rightarrow$ $F_1 = \mathbf{0.85}$
* `GradientBoosting` $\rightarrow$ $F_1 = \mathbf{0.91}$ *(Highest Performer)*



#### 2. Flag the Winning Run File

* Return to the text table spreadsheet view and click directly on the run named **`GradientBoosting`**.
* Scroll down to the **Tags** configuration pane on the run's overview details tab page.
* Click the **Add Tag** option and input the exact selection keys:
* **Key:** `production-candidate`
* **Value:** `true`



> **Note:** Leave the `RandomForest` and `LogisticRegression` run pages completely blank; neither of the other two runs may carry a `production-candidate` tag attribute.

---

### One-Look Triage Dashboard

```text
  📊 Experiment: model-comparison
        │
        ├── 🏃 Run: LogisticRegression  ──► (f1_score: 0.78)  ──► [No action taken]
        ├── 🏃 Run: RandomForest        ──► (f1_score: 0.85)  ──► [No action taken]
        └── 🏃 Run: GradientBoosting    ──► (f1_score: 0.91)  ──► 🏷️ Tagged: production-candidate=true
                                                 ▲
                                                 └── Clear Performance Winner

```

---

### Quick Check Summary for Instant Recall

> **Use comparison charts to quickly isolate performance outliers.** Flagging the definitive winning run file with an explicit key-value pair like `production-candidate: true` decouples model evaluation from infrastructure code—allowing automated deployment scrapers to pick up the top candidate instantly.
