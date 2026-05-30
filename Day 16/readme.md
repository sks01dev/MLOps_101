# Day 15: Managing Experiment Hyperparameters with DVC Params

### The Big Idea: Why Use a Central Parameters File?

When running Machine Learning experiments, you constantly tweak hyperparameters like learning rates, maximum tree depths, or the number of estimators ($n\_estimators$).

#### With `params.yaml`: The Solution

You extract all configurations out of your source scripts and isolate them inside a dedicated text file named **`params.yaml`**.

You then map this file key directly inside your `dvc.yaml` pipeline under a `params:` section block. DVC now monitors that specific variable name. The moment you change the value inside your YAML configuration file, DVC instantly flags the training stage as out-of-date and re-executes your experiment training pipeline automatically when you type `dvc repro`.

---

### Problem Statement

The xFusionCorp Industries ML team manages model hyperparameters through `params.yaml` so experiments vary without forcing hard code rewrites. The current configuration fails to execute end-to-end because of a key mismatch—the parameter key is declared as `n_estimator` in one configuration and expected as `n_estimators` inside the training logic. Correct the parameter mapping and run the execution pipeline cleanly.

---

### The Working Solution Files

#### 1. The Isolated Configuration Block (`/root/code/fraud-detection/params.yaml`)

```yaml
n_estimators: 200

```

#### 2. The Updated Tracking Pipeline (`/root/code/fraud-detection/dvc.yaml`)

```yaml
stages:
  process_data:
    cmd: python src/data/process_data.py
    deps:
      - data/raw/transactions.csv
      - src/data/process_data.py
    outs:
      - data/processed/clean_transactions.csv

  split_data:
    cmd: python src/data/split_data.py
    deps:
      - data/processed/clean_transactions.csv
      - src/data/split_data.py
    outs:
      - data/processed/train.csv
      - data/processed/test.csv

  train:
    cmd: python src/models/train.py
    deps:
      - data/processed/train.csv
      - src/models/train.py
    params:
      - n_estimators
    outs:
      - models/model.pkl

```

---

### The Terminal Solution

Run these quick workflow tools in your command terminal to process your updated model experiment:

```bash
# 1. Navigate to the project path
cd /root/code/fraud-detection/

# 2. Run the pipeline (DVC sees the updated parameter and runs ONLY the train stage!)
dvc repro

```

---

### Smart Execution Flow (The 80/20 Rule)

Look closely at how DVC intelligently optimizes execution when you isolate your configuration variables:

```text
 ┌─────────────────┐       🟢 [ process_data ] Stage ───► No changes detected (SKIPPED)
 │   params.yaml   │                                       Outputs pulled from local cache
 │                 │                                       
 │ n_estimators:   │       🟢 [  split_data  ] Stage ───► No changes detected (SKIPPED)
 │     100 ──► 200 │                                       Outputs pulled from local cache
 └────────┬────────┘                                       
          │                                                
          ▼                                                
 ┌─────────────────┐       🔴 [     train     ] Stage ───► Parameter modified! 
 │    dvc.yaml     │                                       DVC forces script re-execution
 └─────────────────┘                                       Generates updated models/model.pkl

```

* **Targeted Compute:** Because `process_data` and `split_data` do not list `n_estimators` under their dependencies or parameters lists, DVC skips running them entirely, pulling their large datasets cleanly from cache in milliseconds.
* **Experiment History Tracking:** When the training stage concludes, DVC logs the final parameters used into an internal lock state file (`dvc.lock`). This links your model performance metrics directly to your experiment metrics.
