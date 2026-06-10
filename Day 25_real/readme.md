# Day 25: Fast Lookup Notes (Model Registry)

### The Quick Intuition

* **Why use the Registry?** Experiments are for tracking raw training trials. The Model Registry is a formalized archive for your production-ready candidates.
* **The Solution:** Register successful runs under a central model name. MLflow automatically assigns a sequential version number (`Version 1`, `Version 2`) and allows you to attach production-ready shortcodes called **Aliases** (`champion`, `challenger`) so your code can dynamically fetch models without hardcoded version indexes.

---

### Problem Statement

The xFusionCorp Industries ML platform team needs two trained model candidates promoted through the MLflow Model Registry so the ops side can track which model version is serving production traffic. Both runs already exist in the `fraud-detection` experiment. Register both as versions of a new `fraud-detector` model, add a model-level description, and assign `challenger` and `champion` aliases.

---

### The UI Operations Solution

Open the **MLflow UI**, go to the `fraud-detection` experiment view, and perform these steps in order:

1. **Register Version 1 (Baseline):**
* Click the **`baseline`** run $\rightarrow$ scroll to **Artifacts** $\rightarrow$ select the model directory $\rightarrow$ click **Register Model**.
* Choose **Create New Model**, name it **`fraud-detector`**, and register it.


2. **Register Version 2 (Improved):**
* Return to the experiment view $\rightarrow$ click the **`improved`** run $\rightarrow$ scroll to **Artifacts** $\rightarrow$ select the model directory $\rightarrow$ click **Register Model**.
* Select the existing **`fraud-detector`** model from the list and register it.


3. **Configure Tags & Aliases:**
* Go to the **Model Registry** tab on the left menu and open the **`fraud-detector`** asset page.
* Click **Edit** on the description and add: `fraud detection model for xFusionCorp transactions.`
* In the **Versions** grid at the bottom, click the **Aliases** field to add tags:
* Assign **`challenger`** to **Version 1**.
* Assign **`champion`** to **Version 2**.





---

### The One-Glance Layout Map

```text
  🏢 Central MLflow Model Registry
         └── 📦 Model Vault: `fraud-detector`  [Desc: Fraud detection model for xFusionCorp...]
                │
                ├── 📄 Version 1  (Baseline Run Artifacts) ──────► 🏷️ Alias: `@challenger`
                │
                └── 📄 Version 2  (Improved Run Artifacts) ──────► 🏷️ Alias: `@champion`

```

---

### Rule for Instant Recall

> **Git versions your code recipes; the Registry versions your model binaries.** Use production aliases like `@champion` inside your microservice code so your deployment pipeline always fetches the winning model weights automatically without manual config updates.
