# Day 35: Hyperparameter Optimization Tracking with Optuna and MLflow

### The Quick Idea

* **Why orchestrate Optuna with MLflow?** Optuna handles the mathematical execution search strategy to find optimal hyperparameters (e.g., pruning bad configurations, exploiting high-performing zones). However, it lacks a native collaborative interface.
* **The Solution:** Wrapping Optuna trials inside an active MLflow run block context (`with mlflow.start_run():`). This tracks every single optimization guess as an independent line item on the central server, allowing teams to use parallel charts, contour plots, and coordinate views to inspect the full search space.

---

### Problem Statement

The xFusionCorp Industries hyperparameter optimization pipeline at `/root/code/fraud-detection/src/models/tune.py` contains structural routing errors: trials fail to sync with the MLflow storage network, and the optimization logic lacks appropriate telemetry tracking hooks. Correct the objective training context to track all 20 individual searches under the `hyperopt-tuning` workspace partition.

---

### The Code Fix

Open `/root/code/fraud-detection/src/models/tune.py` and modify the `objective` function to wrap each trial in an active MLflow tracking block:

```python
def objective(trial, X, y):
    # FIX: Wrap every evaluation trial in an independent MLflow tracking run
    with mlflow.start_run(run_name=f"trial_{trial.number}"):
        n_estimators = trial.suggest_int("n_estimators", 50, 500)
        max_depth = trial.suggest_int("max_depth", 3, 20)

        # Log parameters explicitly for the trial run
        mlflow.log_params({
            "n_estimators": n_estimators,
            "max_depth": max_depth
        })

        model = RandomForestClassifier(
            n_estimators=n_estimators,
            max_depth=max_depth,
            random_state=42,
        )
        cv = StratifiedKFold(n_splits=N_SPLITS, shuffle=True, random_state=42)
        scores = cross_val_score(model, X, y, cv=cv, scoring="f1")
        score = float(np.mean(scores))

        # FIX: Log the averaged cross-validation score matching release requirements
        mlflow.log_metric("f1_score", score)

        return score

```

---

### The Terminal Solution

Run this command inside your shell terminal interface to execute the hyperparameter tuning search space:

```bash
python3 /root/code/fraud-detection/src/models/tune.py

```

---

### UI Verification Checkpoint Placeholders

#### Hyperparameter Optimization Scatter Grid View

Open your browser and navigate to the MLflow UI dashboard. Select all 20 successfully tracked tuning trials inside the `hyperopt-tuning` experiment workspace and switch to the **Parallel Coordinates** or **Scatter Plot** view to trace the parameter zone clusters.

```

<img width="1366" height="649" alt="image" src="https://github.com/user-attachments/assets/75d2015f-e6be-4a80-83c7-93fe50a228a9" />

```

```
<img width="1366" height="646" alt="image" src="https://github.com/user-attachments/assets/25fe9e3a-1470-409b-9aa5-6938d54fbe81" />
```
### Conclusion

> **Optuna navigates the parameter search space; MLflow logs the search journey.** Always make sure your study initialization uses `direction="maximize"` if your metric target is an evaluation criteria like $F_1$ score or Accuracy, and nest your parameter suggestions directly inside an open `mlflow.start_run()` block to stream trial variants cleanly to the dashboard.
