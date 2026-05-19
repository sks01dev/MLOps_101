# Day 6: Enforcing Code Quality with Ruff and Black

### Problem Statement

The xFusionCorp Industries ML team enforces strict code quality standards on every code contribution using `ruff` and `black`. The project at `/root/code/fraud-detection/` currently fails validation checks for both tools.

The corrected project must meet the following requirements:

* Both `ruff` and `black` must be configured with a standard line length of 120.
* Ruff lint rule selection must include `E`, `F`, `W`, and `I`, explicitly declared under the modern `[tool.ruff.lint]` schema block.
* Running `ruff check src/` and `black --check src/` from the project directory must both exit cleanly with a status code of 0.

---

### Solution

#### 1. Update the Configuration

Open `/root/code/fraud-detection/pyproject.toml` and configure both tools exactly as follows:

```toml
[project]
name = "fraud-detection"
version = "0.1.0"

[tool.ruff]
line-length = 120

[tool.ruff.lint]
select = ["E", "F", "W", "I"]

[tool.black]
line-length = 120

```

#### 2. Execute the Cleanup Commands

Run these commands in your terminal to fix the source files and verify they pass the team standards:

```bash
# Move into the project directory
cd /root/code/fraud-detection/

# Automatically fix linting bugs and sort imports
ruff check src/ --fix

# Automatically format the code style and spacing
black src/

# Verify both checks now pass with exit status 0
ruff check src/
black --check src/

```

---

### Core Comparison: Black vs. Ruff

| Feature | Black (The Formatter) | Ruff (The Linter) |
| --- | --- | --- |
| **Primary Focus** | **How the code LOOKS** (Style & Spacing). | **How the code BEHAVES** (Cleanliness & Safety). |
| **What it fixes** | Indentations, trailing commas, bracket spaces, line wraps. | Unused imports, undefined variables, wrong import order, dead code. |
| **If code is wrong...** | It directly forces and rewrites the file to match rules. | It reports the exact rule violated and can auto-fix many of them. |
| **Reporting Power** | Cannot explain *why* code style is bad; it just rewrites it. | **Can report specific error codes** (like `F401` for unused imports). |
| **ML Engineering Value** | Eradicates endless arguments over code appearance. | Catches silent bugs before they break a production pipeline model. |

---

### The Active Ruff Rules Explained

* **`F` (Pyflakes - Real Mistakes):** Catches critical bugs like calling variables that do not exist or leaving broken imports that will crash your code at runtime.
* **`I` (Isort - Import Sorting):** Automatically alphabetizes and groups your import statements cleanly at the top of the script.
* **`E` & `W` (PEP8 - Style Errors & Warnings):** Catches structural violations against standard Python style guidelines.

---

### The Professional 80/20 Production Workflow

Whenever you finish writing code or modifying pipelines, run this two-step terminal combination before pushing your code to Git:

```bash
# Step 1: Clean out the code garbage and sort imports
ruff check . --fix

# Step 2: Lock down the visual layout formatting
black .

```

#### Automating with Pre-Commit Hooks

To make sure you never forget to run these commands, you can automate them using a `.pre-commit-config.yaml` file placed in your project root:

```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.11.0
    hooks:
      - id: ruff
        args: [--fix]

  - repo: https://github.com/psf/black
    rev: 24.10.0
    hooks:
      - id: black

```

Enable it once by running `pre-commit install`. Now, every single time you type `git commit`, the system automatically runs Ruff and Black for you, keeping your ML repository permanently clean and production-ready.
