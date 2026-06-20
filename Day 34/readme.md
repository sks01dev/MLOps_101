# Day 34: Nested Run Architecture and Stratified Cross-Validation in MLflow

### The Quick Idea

* **Why Stratified $K$-Fold for Imbalanced Data?** Standard $K$-Fold partitioning shuffles datasets randomly. On highly imbalanced datasets (e.g., fraud detection), a random slice might accidentally contain zero fraud examples, causing model evaluation to fail. **Stratified $K$-Fold** solves this by enforcing that every single fold replicates the exact class ratio (e.g., 70/30) of the overall dataset.
* **Why Nested Runs (`Parent-Child` Logging)?** If you treat every validation fold as a top-level independent experiment run, your dashboard fills with tracking noise. Grouping them by nesting fold loops inside a parent run context keeps your dashboard clean, allowing you to expand or collapse the iteration history on demand.

---

### Problem Statement

The xFusionCorp Industries cross-validation script at `/root/code/fraud-detection/src/models/cross_validate.py` uses a non-stratified splitter that skews target class ratios on imbalanced arrays. It also lacks the specific aggregated key schemas needed to pass the release checklist. Modify the code to leverage a stratification-aware cross-validation splitter and structure the final nested summary report payload.

---

### The Code Fix

Open `/root/code/fraud-detection/src/models/cross_validate.py` and modify the splitter definition and aggregate schema dictionary:

```python
# FIX 1: Initialize the stratification-aware splitter to lock the class ratios
cv = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)

# ... (Scaffolding executes fold loops and populates metrics arrays automatically) ...

# FIX 2: Format the summary dictionary keys to match the checklist exactly
aggregate = {
    "mean_accuracy": round(np.mean(acc_scores), 6),
    "std_accuracy": round(np.std(acc_scores), 6),
    "mean_f1": round(np.mean(f1_scores), 6),
    "std_f1": round(np.std(f1_scores), 6),
    "mean_roc_auc": round(np.mean(auc_scores), 6),
    "std_roc_auc": round(np.std(auc_scores), 6),
    "folds": fold_records
}

```

---

### The Terminal Solution

Run this command inside your terminal workspace to execute the validation loop:

```bash
python3 /root/code/fraud-detection/src/models/cross_validate.py

```

---

### MLFlow UI
<img width="1366" height="646" alt="image" src="https://github.com/user-attachments/assets/878dfdb3-e479-41e8-a17c-07139f74f78e" />

---
