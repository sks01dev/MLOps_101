# Day 37: Multi-Stage ML Pipelines & Linear Data Lineage

### Problem Statement

The xFusionCorp Industries multi-stage training pipeline contains a broken stage-chain invariant. The featurization script at `src/featurize.py` reads data directly from the raw data path instead of consuming the clean, dropped-row output file created by the preprocessing script. Fix the input variable configuration inside the module script to restore linear lineage, run the orchestrator, and verify that a single run maps to the server tracking dashboard.

---

### The Code Fix

Open `/root/code/fraud-detection/src/featurize.py` and correct the input data path tracking variable to read from the processed layout mapping instead of the raw data blueprint:

```python
# ... (Configuration loading script scaffolding blocks remain untouched) ...

# FIX: Switch the input configuration path from raw_path to processed_path
input_path = config["data"]["processed_path"]
features_path = config["data"]["features_path"]

df = pd.read_csv(input_path)
df["amount_log"] = np.log1p(df["amount"])

```

---

### The Terminal Solution

Run this command in your terminal interface to execute the orchestrated pipeline script:

```bash
# Run the complete pipeline orchestrator harness
python3 /root/code/fraud-detection/run_pipeline.py

```

---

### UI Verification 
Open your browser and launch the MLflow UI dashboard. Confirm that your experiment list showcases exactly one execution pass labeled `full-pipeline` containing the three evaluation metrics under the `training-pipeline` namespace folder window.

<img width="1366" height="645" alt="image" src="https://github.com/user-attachments/assets/9be8ec6e-57f0-4a90-92f5-30bccd14b262" />

---

### Rule for Instant Recall

> **Data pipelines must always flow forward linearly.** When writing or debugging config-driven multi-stage execution code blocks, double-check that the input path of stage $N$ points exactly to the output path of stage $N-1$ to prevent data leakages and maintain chain invariants across runs.

---
