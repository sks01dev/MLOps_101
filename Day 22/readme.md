# Day 21: MLFlow API logging and running experiment

### The Quick Idea

* **Why API Logging?** Without it, your parameters and metrics vanish from terminal text logs when the execution finishes.
* **The Solution:** Wrap your model steps inside an active runtime context (`with mlflow.start_run():`). Your script automatically streams text metadata to a relational backend store and saves your physical model weight binaries directly to an artifact directory.

---

### Problem Statement

A xFusionCorp Industries data scientist needs a training run recorded in MLflow so the team has a baseline record on the tracking dashboard. The non-MLflow scaffolding has already been written at `/root/code/log_experiment.py`; the MLflow logging calls are left as TODO blocks. Complete the script so that every element of the run is captured by the MLflow tracking server.

---

### The Code Fix: 3 Core API Blocks

Open `/root/code/log_experiment.py` and modify the `with mlflow.start_run():` tracking block to look exactly like this:

```python
with mlflow.start_run():

    # TODO 1: Log every entry in `params` as an MLflow parameter
    mlflow.log_params(params)

    # TODO 2: Log evaluation scores as explicit metrics
    mlflow.log_metric("accuracy", accuracy)
    mlflow.log_metric("f1_score", f1)

    # TODO 3: Log the trained estimator as an MLflow model artifact
    mlflow.sklearn.log_model(sk_model=model, artifact_path="model")

    print(f"accuracy={accuracy}, f1_score={f1}")

```

---

### Terminal Execution

Run this command inside your terminal workspace to execute the logging pass:

```bash
python3 /root/code/log_experiment.py

```

---

### The One-Glance Handoff Flow

```text
 with mlflow.start_run():
        │
        ├──► 1. mlflow.log_params(params)  ──► Backend DB Ledger (Searchable Rows)
        │                                      Records: n_estimators=100, max_depth=5
        │
        ├──► 2. mlflow.log_metric(k, v)    ──► Backend DB Ledger (Performance Values)
        │                                      Records: accuracy=0.92, f1_score=0.89
        │
        └──► 3. mlflow.sklearn.log_model() ──► Artifact Storage (Binary File Assets)
                                               Packages: model.pkl, MLmodel schema

```
