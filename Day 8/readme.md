# Day 8: Automated Code Quality with Pre-Commit Hooks

### Why Pre-Commit is Crucial (The Big Idea)

Even when you know how to use `ruff` and `black`, you or your teammates might occasionally forget to run them before pushing code. This oversight allows messy formatting and silent coding bugs to slip into the production repository.

#### Without Pre-Commit: The Problem

Checking your code health depends entirely on human memory. A developer finishes work in a rush, forgets to run the checkers, and commits broken code. This corrupts the shared codebase and causes the main cloud building servers (CI/CD pipelines) to crash later on.

#### With Pre-Commit: The Solution

A **Pre-Commit Hook** acts as an automated security guard standing right in front of your Git repository.

Every single time you type `git commit`, Git pauses and automatically forces your formatting scripts (`ruff`, `black`, and standard cleaners) to inspect your changes. If these tools spot a bug or messy spacing, **Pre-Commit blocks the commit** and forces you to fix the issues locally before the code can ever leave your computer.

---

### Problem Statement

The xFusionCorp Industries ML team enforces code quality on every commit via `pre-commit`. A draft `.pre-commit-config.yaml` exists in the git repository at `/root/code/fraud-detection/`, but it does not match the team's standard, causing verification runs to fail.

The configuration must declare five specific hooks:

* `trailing-whitespace`, `end-of-file-fixer`, and `check-yaml` from the `pre-commit/pre-commit-hooks` repository.
* `ruff` from the `astral-sh/ruff-pre-commit` repository.
* `black` from the `psf/black-pre-commit-mirror` repository.
* All entries must include valid release version tags inside a `rev:` field.

---

### Solution

#### 1. Correct the Configuration File

Open `/root/code/fraud-detection/.pre-commit-config.yaml` and rewrite it to match this standard layout:

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v6.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.15.13
    hooks:
      - id: ruff

  - repo: https://github.com/psf/black-pre-commit-mirror
    rev: 26.5.1
    hooks:
      - id: black

```

#### 2. Run the Activation Commands

Open your terminal inside the project directory and execute these steps to register and test the hooks:

```bash
# Move to the project root folder
cd /root/code/fraud-detection/

# Update the version tags automatically to their latest releases
pre-commit autoupdate

# Register the scripts into Git's internal commit sequence
pre-commit install

# Force a full test pass against all existing tracked files
pre-commit run --all-files

```

---

### Understanding the Flow

The flowchart below displays exactly what occurs behind the scenes during this process:

```text
[You Type: git commit -m "add model"]
               │
               ▼
   ┌───────────────────────┐
   │  Pre-Commit Triggers  │
   └───────────┬───────────┘
               │
               ▼
 ┌───────────────────────────┐
 │ Runs:                     │
 │ 1. Clean whitespace       │
 │ 2. Check YAML syntax      │
 │ 3. Ruff Linting Check     │
 │ 4. Black Code Formatting  │
 └─────────────┬─────────────┘
               │
       🟢 Passed cleanly?
        ───────┴───────┐
        │              │
       YES             NO 🔴
        │              │
        ▼              ▼
┌───────────────┐  ┌──────────────────────────────┐
│ Commit Saved! │  │ Block Commit!                │
│ Ready to Push │  │ Fix mistakes and try again.  │
└───────────────┘  └──────────────────────────────┘

```
