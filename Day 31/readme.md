# Day 31: Config-Driven Machine Learning Pipelines

### The Quick Intuition

* **Why Config-Driven Training?** Hardcoding file paths, dataset schema columns, or model hyperparameters directly inside application source code makes machine learning pipelines brittle and error-prone.
* **The Solution:** Decouple structural runtime instructions into a separate **YAML Configuration File (`train_config.yaml`)**. This allows platform engineers and data scientists to modify architectures, update input resources, and alter optimization layers seamlessly without rewriting an underlying line of executable Python code.

---

### Problem Statement

The xFusionCorp Industries ML platform team maintains a config-driven training pipeline. The training configuration file at `/root/code/fraud-detection/configs/train_config.yaml` contains invalid entries that cause runtime execution crashes or drop output binaries outside the designated deployment tree directory. Identify and resolve these configuration errors, execute the authority trainer script, and confirm the metrics land successfully inside the local MLflow server space.

---

### The Corrected Production Configuration

#### The Working Layout File (`/root/code/fraud-detection/configs/train_config.yaml`)

```yaml
model:
  type: RandomForestClassifier
  n_estimators: 100
  max_depth: 5
  random_state: 42

data:
  train_path: /root/code/fraud-detection/data/train.csv
  target_column: is_fraud

output:
  model_path: /root/code/fraud-detection/models/model.pkl

mlflow:
  tracking_uri: http://localhost:5000
  experiment_name: fraud-detection

```

---

### The Terminal Solution

Run this command inside your terminal environment to execute the corrected pipeline pass:

```bash
# Execute the config-driven trainer pipeline pass
python3 /root/code/fraud-detection/src/models/train.py

```

---

### One-Glance Workspace Architecture Mapping

The dependency map highlights how the Python execution engine reads the structured keys from the YAML payload layout to guide data staging and validation loops:

```text
 📜 train_config.yaml                              🐍 train.py Execution Core
 
 ├── model:                                        ──► Resolves estimator type validation
 │     type: RandomForestClassifier                    Imports: RandomForestClassifier(**kwargs)
 ├── data:                                         ──► Guides dataset schema alignment
 │     train_path: /.../train.csv                      Reads: `train.csv` -> Drops: `is_fraud`
 ├── output:                                       ──► Serializes binary weights mapping
 │     model_path: /.../models/model.pkl               joblib.dump() -> /.../models/model.pkl
 └── mlflow:                                       ──► Registers telemetry tracking hooks
       tracking_uri: http://localhost:5000             Streams run logs -> http://localhost:5000

```
---
