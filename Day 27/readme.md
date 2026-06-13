# Day 27: Fast Lookup Notes (Batch Inference serving)

### The Quick Intuition

* **Why use Registry Aliases?** If your production code points to a hardcoded file path or version number (e.g., `version/1`), your application breaks the moment a teammate updates the model weights.
* **The Solution:** Point your production inference script to a permanent alias tag shortcut (**`models:/fraud-detector@champion`**). MLflow dynamically fetches the active winning weights from the vault, allowing you to deploy model updates instantly without modifying a single line of serving code.

---

### Problem Statement

The xFusionCorp Industries deployment team needs a batch-prediction wrapper around the registered `fraud-detector` champion model, complete with custom preprocessing, before the model is exposed to downstream services. Complete the MLflow-side plumbing that loads the champion and runs the batch.

---

### The Code Fix

Open `/root/code/predict_with_preprocessing.py` and update the two `TODO` sections:

```python
# TODO 1: Load the champion model by its tracking URI alias
inner_model = mlflow.pyfunc.load_model(MODEL_URI)

# (Scaffolding extracts mean/std metrics from inputs automatically...)
inputs = pd.read_csv(INPUT_CSV)
mean = inputs.values.mean(axis=0)
std = inputs.values.std(axis=0)
std[std == 0] = 1.0  

predictor = ScaledPredictor(inner_model, mean, std)

# TODO 2: Generate batch array predictions, append column, and write to CSV
preds = predictor.predict(None, inputs.values)
inputs['prediction'] = preds
inputs.to_csv(OUTPUT_CSV, index=False)

```

---

### Terminal Execution

Run this command in the shell to execute the inference pipeline:

```bash
python3 /root/code/predict_with_preprocessing.py

```

---

### One-Glance Workspace Architecture

```text
 1. FETCH BY ALIAS                2. CUSTOM PREPROCESSING            3. EXPORT BATCH RESULTS
 
  mlflow.pyfunc.load_model()       predictor.predict()                inputs.to_csv(index=False)
          │                                 │                                      │
          ▼                                 ▼                                      ▼
  Pulls model dynamically          Applies mean/std scaling scaling   Writes: predictions.csv
  tagged `@champion` from          to the rows before scoring         • 10 rows matching input features
  central registry vault           them with the model weights        • Includes new `prediction` column

```

