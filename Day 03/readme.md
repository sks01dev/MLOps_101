# Day 3: Modern Dependency Management with uv

### The Task

The objective was to fix an unstandardized `requirements.in` specification file for a fraud detection project and compile it into a fully pinned, production-ready `requirements.txt` lockfile using `uv`.

### The Problem

Traditional dependency management using standard `pip freeze` creates unmaintainable deployment setups. It dumps every hidden background library (transitive dependency) into one flat, messy text file with no hierarchy. This makes it impossible to distinguish between the core packages chosen by the developer and the background packages pulled in automatically by the system, frequently causing installation crashes when moving code between different machines.

---

### Core Workflow Comparison

| Metric | Traditional `pip freeze` | Modern `uv pip compile` |
| --- | --- | --- |
| **Source File** | None. You install packages blindly via the terminal. | `requirements.in` (A clean list of only your core tools). |
| **Who Solves Versions?** | You do. You hope the latest versions don't crash together. | **`uv` Engine**. It calculates the math of compatibility automatically. |
| **Output File** | `requirements.txt` (A messy dump of all packages mixed together). | `requirements.txt` (A cleanly structured, pinned lockfile). |
| **Cross-Platform Safety** | Low. Often breaks when moving between different OS platforms. | High. Resolves pure Python dependencies cleanly for any host. |

---

### The Solution

**1. Navigate to the Workspace**
Moved into the project directory where the dependency specifications are maintained.

```bash
cd /root/code/fraud-detection/

```

**2. Standardize the Input Specification**
Cleaned up `/root/code/fraud-detection/requirements.in` to declare *only* the explicit, top-level packages required by the data science team, leaving out specific versions so the resolution engine can find the most stable combination:

```text
scikit-learn
mlflow
pandas
numpy

```

**3. Compile the Lockfile**
Used `uv` to safely evaluate the dependency tree, resolve version compatibility against PyPI, and output a strictly pinned lockfile.

```bash
uv pip compile requirements.in -o requirements.txt

```

* `requirements.in`: The high-level human request containing only the primary tools we want.
* `requirements.txt`: The machine-generated lockfile containing our primary tools pinned with exact versions (`==`), along with all accompanying background dependencies.

---

### Summary of Day 3

* **The `.in` vs `.txt` Workflow:** Learned that `requirements.in` acts as our human intent file, while `requirements.txt` acts as our immutable machine deployment blueprint.
* **Automated Resolution:** Understood that instead of blindly installing packages and guessing version compatibility, we delegate the calculation of package interactions to a dedicated engine like `uv`.
* **Determinism in MLOps:** Realized that locking exact versions is mandatory for machine learning pipelines to guarantee that **Code + Fixed Environment = Same Model Predictions Every Single Time.**
