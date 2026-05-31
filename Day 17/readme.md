# Day 16: Tracking Performance Metrics with DVC

### The Big Idea: Why Track Metrics Differently?

When your pipeline evaluates a model, it outputs evaluation stats like `accuracy` or `f1_score` into a file (e.g., `metrics.json`).

Unlike multi-gigabyte datasets, metrics are tiny plain-text files. We want to see how these numbers change over time directly inside Git's text history (like when running `git diff`) while still letting DVC parse them for tracking UI dashboards.

---

### The Problem: `cache: true` vs. `cache: false`

By default, any file listed under `outs:` in `dvc.yaml` is treated with **`cache: true`**.

* **`cache: true` (Default for Big Files):** DVC steals the file from your folder, hides it inside the `.dvc/cache` directory, and adds it to `.gitignore`. Git becomes completely blind to the file's contents.
* **`cache: false` (The Special Rule for Metrics):** DVC tracks the file but leaves it entirely in place on your disk. It is **not** added to `.gitignore`. This allows **Git to track the text history directly**, while DVC reads it to generate visualization charts.

---

### When to Use Which Configuration

| If the file is... | Use this configuration | Why you do it |
| --- | --- | --- |
| 📦 **Heavy/Binary** <br>

<br>(Datasets, `.csv`, `.pkl` models) | **`cache: true`** *(Default)* | Keeps Git lightweight. Prevents repository bloat by locking raw weights into DVC storage. |
| 📄 **Lightweight Text** <br>

<br>(`metrics.json`, `plots.yaml`) | **`cache: false`** | Allows you to use `git diff` to see exactly how your model's accuracy changed between code commits. |

---

### The Configuration Setup (`dvc.yaml`)

To wire your metrics file correctly into the pipeline without hiding it from Git, configure your execution step like this:

```yaml
stages:
  train:
    cmd: python src/models/train.py
    deps:
      - data/processed/train.csv
      - src/models/train.py
    outs:
      - models/model.pkl     # Heavy binary file: cache=true automatically
    metrics:
      - metrics.json:
          cache: false       # Tiny text file: visible to Git for easy diffs

```

---

### The Fast Lookup Workflow Summary

```text
 1. Modify Experiment params  ──► 2. Run [ dvc repro ]  ──► 3. Inspect Results [ dvc metrics show ]
                                                               Path          accuracy    f1_score
                                                               metrics.json  1.0         1.0

```

1. **`dvc repro`**: Automatically executes the out-of-date stages and generates the fresh model evaluation file.
2. **`dvc metrics show`**: Scans the registered layout and surfaces your performance scores right inside the terminal screen.
