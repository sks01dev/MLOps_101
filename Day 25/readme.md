# Day 25: Hands-Free Experiment Tracking with MLflow Autologging

### The Big Idea: Eliminating Boilerplate Code

Manual tracking using explicit programmatic logging calls becomes cluttered and error-prone as your machine learning architectures scale up.

#### Without Autologging: The Problem

If you rely completely on manual hooks:

* **The Boilerplate Monster:** You have to write dozens of lines of `mlflow.log_param()` for every single internal model hyperparameter, plus more for training iteration losses and artifact paths.
* **Missed Metrics:** If you miss a parameter or default argument in your code wrapper, that metadata is lost forever from your dashboard tracker.

#### With Autologging: The Solution

**MLflow Autologging** acts like an automated supervisor for your machine learning training loop framework.

By adding a single orchestration command (`mlflow.autolog()`), MLflow dynamically hooks into your framework's underlying training hooks (such as Scikit-Learn's `.fit()` function). The moment `.fit()` is executed, MLflow automatically scrapes all default and explicit hyperparameters, streams intermediate training metrics, packages environmental dependencies, and serializes the final trained weights into your artifact repository without any manual intervention.

---

### Problem Statement

The training scaffolding script at `/root/code/autolog_experiment.py` handles model instantiation but requires configuration automation telemetry hooks. Complete the two programmatic `TODO` blocks to enable framework-wide Scikit-Learn metric capture and route the resulting hands-free training run logs directly into an isolated experiment workspace named `autolog-demo`.

---

### The Final Telemetry Code Layout (`/root/code/autolog_experiment.py`)

```python
import numpy as np
import mlflow
import mlflow.sklearn
from sklearn.linear_model import LogisticRegression

# Set the connection path to our active tracking server
mlflow.set_tracking_uri("http://localhost:5000")


# TODO 1: Enable framework-wide automatic tracking capture
mlflow.autolog()


# TODO 2: Route the automated logs into a specific workspace partition
mlflow.set_experiment("autolog-demo")


# Synthetic toy array to give the trainer something to execute
X = np.array([[0, 0], [0, 1], [1, 0], [1, 1]])
y = np.array([0, 0, 1, 1])

# Instantiate the estimator
model = LogisticRegression(C=1.0, max_iter=100, random_state=42)

# MLflow automatically initializes a run context and extracts data here!
model.fit(X, y)

print("Autolog run complete — check the MLflow UI")

```

---

### The Terminal Solution

Run this command inside your terminal environment to execute the automated tracking training pass:

```bash
python3 /root/code/autolog_experiment.py

```

---

### The One-Glance Handoff Flow

```text
  1. USER SETUP                 2. AUTOMATED CAPTURE             3. END-STATE RESULT
 
   mlflow.autolog()                                               📊 MLflow UI Dashboard
          │                                                       ├── 📂 autolog-demo
          ▼                                                       └── 🏃 zealous-bass-415
   model.fit(X, y)  ───────────► [ MLflow Hooks `.fit()` ]             ├── 📋 Params: C, max_iter, solver...
                                 Automatically Extracts:               ├── 📈 Metrics: training_accuracy_score
                                 • 14+ Constructor Parameters          └── 📦 Artifacts:
                                 • 5+ Model Metric Evaluators               └── 📁 model/ (MLmodel, model.pkl)
                                 • Pickled Estimator Weights

```

---

### Quick Check Summary for Instant Recall

> **Always invoke `mlflow.autolog()` BEFORE executing your model's `.fit()` function.** This allows MLflow to place its diagnostic wrappers on the underlying training loops, capturing both your explicitly declared arguments and the framework's internal implicit default hyperparameter matrix seamlessly.
