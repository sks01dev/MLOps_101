# Day 19: End-to-End Production ML Pipelines with DVC

### The Big Idea: The Complete Production Pipeline Loop

In production engineering, you don't track isolated scripts, metrics, or data frames. Instead, you orchestrate them as a single, multi-stage **Production-Grade Data and Training Lifecycle Pipeline**.

When you configure your inputs (`deps`), hyperparameters (`params`), outputs (`outs`), and data analytics files (`metrics`), DVC builds a cohesive dependency graph (**DAG**). Running `dvc repro` executes the pipeline safely, cache validation saves server compute time, `dvc push` archives your assets to cloud object storage, and a structured Git release tag (`v1.0`) anchors your historical code recipe state in time forever.

---

### Problem Statement

The xFusionCorp Industries `ml-pipeline` repository contains a broken and incomplete production pipeline definition layout. The goal is to:

1. Debug syntax paths inside the existing code pipeline stages (`ingest`, `validate`, `preprocess`).
2. Promote pre-staged python automation modules from `scripts-staging/` over to the primary `scripts/` runtime workspace.
3. Wire two missing downstream execution block layers (`train` and `evaluate`) cleanly into the `dvc.yaml` graph using custom tracking configurations.
4. Run an end-to-end evaluation pipeline pass via `dvc repro`, backup heavy outputs onto a shared cloud server bucket via `dvc push`, and anchor the complete infrastructure state using a clean Git checkpoint tag (`v1.0`).

---

### The Working Production File Mapping

#### The Corrected End-to-End Pipeline Map (`/root/code/ml-pipeline/dvc.yaml`)

```yaml
stages:
  ingest:
    cmd: python scripts/ingest.py
    deps:
      - scripts/ingest.py
    outs:
      - data/raw/transactions.csv

  validate:
    cmd: python scripts/validate.py
    deps:
      - data/raw/transactions.csv
      - scripts/validate.py
    outs:
      - reports/validation.json

  preprocess:
    cmd: python scripts/preprocess.py
    deps:
      - data/raw/transactions.csv
      - scripts/preprocess.py
    outs:
      - data/processed/cleaned.csv

  train:
    cmd: python scripts/train.py
    deps:
      - data/processed/cleaned.csv
      - scripts/train.py
    params:
      - n_estimators
      - max_depth
      - test_size
      - random_seed
    outs:
      - models/model.pkl
      - data/processed/test_split.csv
    metrics:
      - metrics.json:
          cache: false

  evaluate:
    cmd: python scripts/evaluate.py
    deps:
      - models/model.pkl
      - data/processed/test_split.csv
      - scripts/evaluate.py
    outs:
      - reports/evaluation.json:
          cache: false

```

---

### The Terminal Solution

Run this sequence of production tools inside your command line terminal to cleanly deploy and freeze the engineering workspace:

```bash
# 1. Enter the project tracking directory
cd /root/code/ml-pipeline/

# 2. Extract and copy operational scripts out of staging area 
cp scripts-staging/train.py scripts-staging/evaluate.py scripts/

# 3. Process and run the complete data lifecycle end-to-end
dvc repro

# 4. Upload heavy raw datasets and binary models safely to the cloud server
dvc push

# 5. Stage text configuration metadata recipe adjustments to the Git tracker
git add .

# 6. Save the production ledger checkpoint snapshot state
git commit -m "Added 2 new stages to the pipeline, ran it and pushed onto the cloud bucket"

# 7. Anchor the release snapshot marker tag to the completed history state
git tag v1.0

```

---

### Fast Lookup Look: One-Glance Mastery Table

| DVC Stage | Input Dependency (`deps`) | What it Physically Alters / Generates (`outs`) |
| --- | --- | --- |
| **`ingest`** | Ingest execution logic script | Fetches the raw operational file base (`data/raw/transactions.csv`) |
| **`validate`** | Raw file database + Validation check logic | Evaluates entry properties (`reports/validation.json`) |
| **`preprocess`** | Raw file database + Data-cleansing script | Drops standard feature-engineered layers (`data/processed/cleaned.csv`) |
| **`train`** | Cleaned dataset layer + Model logic + Hyperparameters | Exports weights file (`models/model.pkl`) & split (`test_split.csv`) |
| **`evaluate`** | Model weights + Split dataset + Evaluation script | Generates validation metrics file reports (`metrics.json`) |

---
