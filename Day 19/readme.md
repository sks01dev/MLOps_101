

# Day 18: Time-Travel and Versioning with Git Branches and DVC

### The Big Idea: Data Time-Travel

Git is excellent for switching between different code branches, but it cannot handle switching between multi-gigabyte datasets.

By pairing **Git Branches** with **DVC Tracking**, you can unlock true **Data Time-Travel**. When you switch branches, Git swaps out the tiny text tracking pointer files, and DVC instantly swaps out the heavy physical datasets matching that branch. This allows your team to seamlessly jump between completely different dataset versions or model training iterations without manual file renaming or copying.

---

### Problem Statement

The xFusionCorp Industries ML team keeps different dataset and model versions on separate Git branches to enable clean rollbacks. The objective is to:

1. Tag the current production baseline dataset state on `main` as `v1.0`.
2. Create an isolated experiment branch named `v2-improved`.
3. Safely swap the baseline dataset with an upgraded data file payload (`transactions_v2.csv`).
4. Re-track the adjustments with DVC, recompute the processing pipeline execution stages via `dvc repro`, and commit the text tracking pointers to Git.
5. Switch back to the `main` branch and restore the physical `v1.0` dataset cleanly on disk.

---

### The Production Terminal Solution

```bash
# PHASE 1: Establish the Baseline Checkpoint on Main Branch
git tag v1.0

# PHASE 2: Create and Switch to the Isolated Sandbox Branch
git switch -c v2-improved

# PHASE 3: Unlock and Inject the New Data Payload
dvc unprotect data/raw/transactions.csv
cp data/raw/transactions_v2.csv data/raw/transactions.csv

# PHASE 4: Re-Index the Data and Run the Processing Pipeline
dvc add data/raw/transactions.csv
dvc repro

# PHASE 5: Lock the New Text Metadata into the Branch Ledger
git add data/raw/transactions.csv.dvc dvc.lock
git commit -m "Upgraded the dataset to v2 and re-ran the pipeline"

# PHASE 6: Time-Travel Back to Main and Synchronize Physical Disk Data
git checkout main
dvc checkout

```

---


### Summary Rule for Instant Recall

> **Git changes the map (`.dvc` pointer files), DVC changes the territory (the actual raw data files).** Always run `dvc checkout` immediately after `git checkout` to make sure your physical dataset matches your active code timeline.
