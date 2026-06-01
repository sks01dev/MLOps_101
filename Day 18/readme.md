# Day 17: Machine Learning Experiment Tracking with DVC

### The Big Idea: Why Use DVC Experiments?

When tuning an ML model, you iterate through different hyperparameters to find the highest performance scores.

#### Without DVC Experiments: The Problem

If you use standard Git to track your experiments:

1. **Commit Clutter:** You have to make a separate Git commit for every single hyperparameter change you try, filling your Git history with useless commit messages like `"tried lr=0.01"`, `"tried lr=0.001"`.
2. **Messy Branching:** Creating a Git branch for short-lived, failed experiments makes your repository messy and difficult for teams to navigate.

#### With DVC Experiments: The Solution

DVC runs your parameter changes in an isolated background space without polluting your Git log. It allows you to run hundreds of iterations freely.

DVC saves each run under a unique, auto-generated name (e.g., `cased-agio`). You can view all results side-by-side in a clean spreadsheet table format right inside your terminal, and then choose to permanently roll out only your absolute best run back into your main working folder.

---

### Problem Statement

The xFusionCorp Industries data science team needs to find the optimal value for the `n_estimators` hyperparameter. Run three distinct background experiments ($50$, $200$, and $500$), evaluate their performance metrics side-by-side, and promote the single best-performing run permanently into the active production code workspace.

---

### The Terminal Solution

Run this exact sequence of commands to execute, compare, and promote your machine learning experiment variants:

```bash
# 1. Run Experiment 1 (n_estimators = 50)
dvc exp run -S n_estimators=50

# 2. Run Experiment 2 (n_estimators = 200)
dvc exp run -S n_estimators=200

# 3. Run Experiment 3 (n_estimators = 500)
dvc exp run -S n_estimators=500

# 4. Compare all experiment metrics side-by-side to find the winner
dvc exp show

# 5. Overwrite your active workspace with the winning experiment's files
dvc exp apply cased-agio

```

---

### The Experiment Commands 

| Command | What it does in simple terms | When exactly to use it |
| --- | --- | --- |
| **`dvc exp run -S <key>=<value>`** | Temporarily swaps a variable inside `params.yaml` and executes `dvc repro` completely in the background. | Use this to launch new training iterations with different learning rates, batch sizes, or model arguments. |
| **`dvc exp show`** | Prints a clean text table display showing every experiment run alongside its parameters and resulting metrics. | Use this after running your iterations to quickly scan, sort, and find your highest-performing parameters. |
| **`dvc exp apply <exp_name>`** | Extracts the specific parameter values, metrics, and model weights (`.pkl`) from the background run and forces them into your current workspace. | Use this when you have found a winning experiment and want to load its exact files back into your main directory to prepare for a production commit. |

---

### Understanding the Handoff Flow

```text
 1. LAUNCH RUNS                2. COMPARE METRICS            3. CHOOSE WINNER
 
 dvc exp run -S                dvc exp show                  dvc exp apply cased-agio
 n_estimators=50, 200, 500
                               ┌────────────┬──────────┐     Overwrites local workspace with:
                               │ Experiment │ f1_score │     • params.yaml (n_estimators: 200)
                               ├────────────┼──────────┤     • metrics.json (f1_score: 0.92)
                               │ amiss-stud │  0.8975  │     • models/model.pkl (Winning Weights)
                               │ cased-agio │  0.9200  │ ◄─── Winning Run Applied!
                               │ tinct-hate │  0.8300  │
                               └────────────┴──────────┘

```
