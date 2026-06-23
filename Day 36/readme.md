# Day 36: Multi-Model Evaluation and Programmatic Selection with the MLflow API

### Problem Statement

The xFusionCorp Industries ML platform team is running a three-way bake-off competition among multiple model families. The programmatic orchestrator component at `/root/code/fraud-detection/src/models/bakeoff.py` queries tracking histories via the MLflow API but extracts an incorrect winner due to a lack of sorting rules, writing an invalid format schema. Fix the sorting parameters and dictionary parsing loops to extract and register the highest-$F_1$ candidate run metadata safely.

---

### The Code Fix

Open `/root/code/fraud-detection/src/models/bakeoff.py` and modify the sorting parameters inside `mlflow.search_runs()` along with the structured report payload block to extract the top record:

```python
    # FIX 1: Enforce descending sort logic by f1_score to place the top model on top
    runs = mlflow.search_runs(
        experiment_ids=[exp.experiment_id],
        order_by=["metrics.f1_score DESC"],
        max_results=10,
    )
    if runs.empty:
        raise SystemExit(
            f"No runs found in {EXPERIMENT!r}. Run the three trainer "
            "scripts first."
        )

    winner = runs.iloc[0]
    
    # FIX 2: Correct metadata structural property keys to map the checklist fields
    report = {
        "model_type": winner["tags.candidate"],
        "run_id": winner["run_id"],
        "f1_score": float(winner["metrics.f1_score"]),
    }

```

---

### The Terminal Solution

Run these consecutive commands in the workspace shell to execute the target model families and register the evaluation artifact:

```bash
# 1. Run the separate training algorithms to populate the database tables
python3 /root/code/fraud-detection/src/models/train_rf.py
python3 /root/code/fraud-detection/src/models/train_gb.py
python3 /root/code/fraud-detection/src/models/train_lr.py

# 2. Run the fixed orchestrator to extract the top metadata row
python3 /root/code/fraud-detection/src/models/bakeoff.py

```

---

### MLFLow UI 

#### 1. Multi-Model Performance Metric Chart

Open the MLflow dashboard, highlight the three individual completed model iterations inside your experiment, and switch to the **Bar Chart** comparison interface tab to verify that the target evaluation metric maps out accurately.

<img width="1366" height="645" alt="image" src="https://github.com/user-attachments/assets/947a3a7f-3a8c-467a-a3d3-b0c2aa8b8f25" />


#### 2. Generated Winner Summary Output Schema

Open the file at `/root/code/fraud-detection/reports/winner.json` to verify that your programmatic script successfully extracted the top model's metadata properties.

<img width="1366" height="646" alt="image" src="https://github.com/user-attachments/assets/943d6945-ab79-465a-b415-ce0c971fc4c5" />


---

### Main Idea

> **`mlflow.search_runs()` yields a Pandas DataFrame containing all parameters and tags.** When retrieving the top model candidate from an experiment programmatically, always pass the parameter string `"metrics.<your_metric> DESC"` inside the `order_by=[]` list argument to ensure the winning record safely populates row index zero (`iloc[0]`).
