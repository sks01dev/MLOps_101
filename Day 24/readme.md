# Day 23: Triaging and Tagging Models in the MLflow UI

### The Big Idea: Querying and Triaging Training Runs

As you scale up machine learning experiments, pipelines generate dozens of runs simultaneously. Checking each execution manually to identify the best candidates becomes impractical.

The **MLflow Evaluation Dashboard** provides powerful SQL-like filtering expressions (e.g., `metrics.f1_score > 0.85`) to search through your metadata repository instantly. Once filtered, you can flag models right from the UI using custom tracking metadata tags. This allows deployment teams to immediately distinguish production-ready candidates from stale or underperforming iterations.

---

### Problem Statement

An xFusionCorp Industries data scientist has accumulated ten training runs in the `fraud-detection` MLflow experiment. Triage these runs via the MLflow UI: query the ledger to find the single best-performing candidate to shortlist, and flag every clearly underperforming run for removal based on their $F_1$ score thresholds.

---

### The UI Operations Solution

Open the **MLflow UI** dashboard, navigate to the `fraud-detection` experiment workspace under **Evaluation runs**, and execute these two triaging steps:

#### 1. Shortlist the Top Candidate

* In the search bar at the top of the runs table, type the filter expression:
```sql
metrics.f1_score > 0.85

```


* Hit Enter. Locate the single run with the absolute highest performance score:
* **Run Name:** `trusting-zebra-872` (with an `f1_score` of **0.95**)


* Click on that run name, scroll down to the **Tags** section, and click **Add Tag**:
* **Key:** `review-status`
* **Value:** `shortlisted`



#### 2. Flag the Under-Performers

* Clear the search bar and input the threshold filter for poor runs:
```sql
metrics.f1_score < 0.75

```


* Hit Enter. This surfaces the underperforming runs:
* `polite-mouse-578` (with an `f1_score` of **0.70**)
* `magnificent-flea-145` (with an `f1_score` of **0.72**)


* For **each** of these two runs, click on their names, navigate to the **Tags** pane, and select **Add Tag**:
* **Key:** `review-status`
* **Value:** `rejected`



> **Note:** Do not add any `review-status` tags to the remaining runs falling within the standard $0.75 \le F_1 \le 0.85$ buffer band.

---

### One-Glance Triaging Rules

| If the Run Has... | Target Threshold | Run-Level Action | Assigned Metadata Tag |
| --- | --- | --- | --- |
| 🏆 **Top Performer** | $F_1 > 0.85$ (Max: **0.95**) | Promote as production candidate | `review-status: shortlisted` |
| ⚖️ **Baseline Buffer** | $0.75 \le F_1 \le 0.85$ | Retain as historical reference | *No tag applied* |
| ❌ **Under-Performer** | $F_1 < 0.75$ | Mark for removal/cleanup | `review-status: rejected` |

---

### Quick Check Summary for Instant Recall

> Use syntax filters like `metrics.f1_score > 0.85` to parse massive run tables instantly. Applying systematic tags such as `review-status` right inside the UI bridges the gap between raw training numbers and downstream model registry deployment steps.
