# Day 11: Correcting Git Ownership and Transferring to DVC

### The Quick Intuition

* **The Problem:** Git tracks files line-by-line. Committing a heavy dataset like `transactions.csv` directly to Git causes your repository history to balloon and slow down.
* **The Solution:** Use DVC to calculate a tiny text pointer hash for Git to track, while storing the heavy raw file safely inside a hidden local cache.

---

### Problem Statement

A teammate accidentally committed a raw dataset (`data/raw/transactions.csv`) directly to Git instead of tracking it with DVC. Bring the repository back to team standards: strip Git's tracking ownership without deleting the file from disk, hand it over to DVC, and commit the new tracking pointer file to Git.

---

### The Terminal Solution

```bash
# 1. Force Git to stop tracking the file (keeps the real file safe on disk)
git rm --cached data/raw/transactions.csv

# 2. Hand tracking ownership over to DVC
dvc add data/raw/transactions.csv

# 3. Stage the newly created .dvc pointer and updated .gitignore
git add .

# 4. Record the tracking fix in Git history
git commit -m "Track transactions dataset with DVC"

```

---

### Inside the File: The Unique DVC Hash

When you ran `dvc add`, DVC generated a tiny plain-text file named `data/raw/transactions.csv.dvc`.

Instead of saving the millions of lines of raw transactions data into Git, Git only tracks this tiny file. It looks exactly like this inside:

```yaml
outs:
  - md5: 0d71ab7b52e16b1bd93e41b995ebc116
    path: transactions.csv

```

* **`md5` (The Hash):** This is the unique mathematical fingerprint of your dataset. If even a single comma or digit changes inside `transactions.csv`, this string changes completely, telling DVC that a new version exists.
* **`path`:** Tells DVC which local file this specific fingerprint belongs to.

---
