# Day 22: Managing Workspaces and Meta-Tags in the MLflow UI

### The Big Idea: Why Organize with Separate Experiments?

By default, MLflow logs every single pipeline run into a generic bucket called the `Default` experiment.

#### Without Separate Experiments: The Problem

If your data science department dumps all their tracking logs into the same space:

* **Metric Pollution:** Your financial churn prediction model logs will mix directly with fraud detection or image recognition metrics.
* **Dashboard Chaos:** It becomes impossible to compare or filter relevant training charts because different projects use entirely separate hyperparameter names and verification metrics.

#### With Separate Experiments: The Solution

You use the **MLflow UI** or API to provision distinct, isolated **Experiments**.

Each experiment acts as a dedicated folder or workspace namespace. This isolates run histories, applies relevant team meta-tags (`team: analytics`, `team: ml-platform`), and provides clean, separate comparison dashboards for individual business goals.

---

### Problem Statement

The xFusionCorp Industries ML platform team is onboarding two new machine learning projects. To prevent metric clutter, register both experiments using the graphical MLflow UI dashboard instead of sharing the baseline `Default` partition, and attach specific ownership metadata description attributes and team lookup keys.

---

### The UI Operations Solution

Since this task is performed entirely inside the graphical interactive web panel, no terminal commands are required. Click the **MLflow UI** button at the top of your interface and perform these two creation loops:

#### 1. Register the Fraud Detection Namespace

* Click the **Create** button in the top right corner of the dashboard layout.
* In the **Experiment Name** field, type: `fraud-detection`
* In the **Description** box, add any project context phrasing (e.g., `An end to end ML Project on detecting fraudulent entries`).
* Under the **Tags** subsection, define the ownership mapping:
* **Key:** `team`
* **Value:** `ml-platform`


* Save the workspace.

#### 2. Register the Churn Prediction Namespace

* Click the **Create** button again.
* In the **Experiment Name** field, type: `churn-prediction`
* In the **Description** box, add context phrasing (e.g., `An end to end ML project to predict user churn`).
* Under the **Tags** subsection, define the ownership mapping:
* **Key:** `team`
* **Value:** `analytics`


* Save the workspace.

---

### One-Look Workspace Architecture

The tree below displays exactly how your backend ledger database maps and organizes tracking paths once separate namespaces are established:

```text
 💻 [ Central MLflow Server ] ──► Port 5000 Proxy Gateway
        │
        ├──📁 Default Experiment  (ID: 0)     ──► Legacy Baseline Bucket
        │
        ├──📁 fraud-detection     (ID: 1)     ──► Tagged: [team: ml-platform]
        │    ├── 🏃 Run: kindly-mule-578         └── Only stores fraud model runs
        │    └── 🏃 Run: selective-hawk-12
        │
        └──📁 churn-prediction    (ID: 2)     ──► Tagged: [team: analytics]
             └── 🏃 Run: swift-fox-99            └── Only stores churn model runs

```

---

### MLFlow UI



### Quick Check Summary for Instant Recall

> **Never log production pipelines into the Default bucket.** Creating separate named experiments keeps project dashboards clean. Attaching metadata tags like `team` allows infrastructure managers to filter, group, and track project resource consumption directly from the master UI index view.
