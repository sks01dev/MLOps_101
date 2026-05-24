# Day 10: Data Version Control (DVC)

### The Big Idea: Why Use DVC?

Git is built to handle lightweight text code scripts. It is not designed to store massive, multi-gigabyte files like datasets or machine learning model weights (`.pkl`, `.pth`). Trying to force large files into Git makes your repository slow and corrupts your history tracking.

* **Without DVC:** Engineers manually rename files to track versions (e.g., `data_v1.csv`, `data_v2_final.csv`). Nobody knows which version of the dataset was actually used to train which specific model file, leading to untraceable pipelines.
* **With DVC:** DVC acts exactly like **"Git for your Data and Models."** It keeps the huge files out of your Git repository. Instead, it securely uploads your data to external storage and leaves a tiny, lightweight text pointer file (like `data.dvc`) inside Git to track the exact version changes.

---

### Problem Statement

The xFusionCorp Industries ML team is adopting DVC so that datasets and model files are versioned separately from code. The objective is to initialize DVC inside the existing Git repository at `/root/code/fraud-detection/`, track the project's data and model artifacts, and commit the system adjustments directly to Git.

---

### The Terminal Solution

Run these commands sequentially from the project root directory:

```bash
# 1. Initialize the DVC tracking engine inside your project
dvc init

# 2. Tell DVC to track your data and model directories
dvc add data/ models/

# 3. Stage the lightweight DVC pointer files and configuration adjustments to Git
git add .

# 4. Save the snapshot of your project configuration to Git history
git commit -m "Initialize DVC"

```

---

### The Core DVC Lifecycle Workflow

This flowchart displays how Git and DVC work hand-in-hand to save, update, and switch between your data and model versions over time:

```text
       [ SAVE / ADD WORKFLOW ]
            data_v1.csv
                 │
            dvc add data/  ─────► Creates tiny text pointer: data.dvc
            git add .
            git commit
            dvc push       ─────► Saves raw large file safely to remote storage
                 │
                 ▼
            [ Saved V1 ]


      [ CHANGING / UPDATING WORKFLOW ]
            data_v2.csv    (Data changes or model retrained)
                 │
            dvc add data/  ─────► Updates the data.dvc pointer file
            git add .
            git commit
            dvc push       ─────► Saves raw new file to remote storage
                 │
                 ▼
            [ Saved V2 ]


      [ SWITCHING / ROLLING BACK WORKFLOW ]
  Want Old V1?                   Want New V2?
  git checkout OLD_COMMIT_HASH    git checkout NEW_COMMIT_HASH
  dvc pull                        dvc pull
       │                            │
       ▼                            ▼
  ➡ data_v1.csv              ➡ data_v2.csv

```

---

### The major DVC Commands

These four core commands cover 90% of everything you will ever do with DVC in an enterprise MLOps engineering pipeline:

| Command | What it does | When to use it |
| --- | --- | --- |
| **`dvc init`** | Sets up the core background DVC directories inside your repository. | Run **once** at the very start of a fresh ML repository workspace. |
| **`dvc add <path>`** | Instructs DVC to take over tracking a large file/folder. Creates a matching `.dvc` file. | Every time you add a new dataset, change raw data, or export a freshly trained model. |
| **`dvc push`** | Uploads the actual heavy data files safely from your computer to your secure storage server. | Right after you make a Git commit, ensuring your data is backed up off your machine. |
| **`dvc pull`** | Looks at your current Git commit pointer file and downloads the exact matching heavy data file. | When you checkout a teammate's branch or log into a new cloud computing engine to run training. |
