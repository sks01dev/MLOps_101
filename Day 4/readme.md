# Day 4: Standardizing Project Structure

### Problem Statement

A colleague started a new ML project at `/root/code/fraud-detection/`, but the layout does not match the company standard. Bring the project in line with the team's conventions.

The final layout must match the tree below exactly:

```text
fraud-detection/
├── data/
│   ├── raw/
│   └── processed/
├── models/
├── notebooks/
├── src/
│   ├── data/
│   ├── features/
│   ├── models/
│   └── utils/
├── tests/
├── configs/
├── requirements.txt
└── README.md

```

* Every subdirectory under `src/` must contain an `__init__.py` file so that Python recognizes it as a package.
* `requirements.txt` must list these exact packages: `scikit-learn`, `pandas`, `numpy`, and `mlflow`.
* `README.md` must begin with the heading `# fraud-detection`.

---

### Solution

Run these commands from your terminal:

```bash
# 1. Move into the project folder
cd /root/code/fraud-detection/

# 2. Create the exact folder structure
mkdir -p data/raw data/processed models notebooks src/data src/features src/models src/utils tests configs

# 3. Turn the src folders into Python packages
touch src/data/__init__.py src/features/__init__.py src/models/__init__.py src/utils/__init__.py

# 4. Create the required README file
echo "# fraud-detection" > README.md

# 5. Create the required requirements file
cat << EOF > requirements.txt
scikit-learn
pandas
numpy
mlflow
EOF

```

---

### Explanation

#### 1. Why `__init__.py` Matters (Code Communication)

By default, Python sees a folder as just a place to hold files. When you add an empty `__init__.py` file, you tell Python: *"This folder is an official code package."* This allows different scripts in your project to talk to each other. Instead of dealing with complicated file paths, any script can cleanly import code from another folder using dot notation:

```python
from src.utils import data_cleaner

```

#### 2. Separation of Concerns (Keeping Things Organized)

Regular software only manages **Code**. Machine Learning projects must manage three completely different assets: **Code, Data, and Models**.

Mixing them together causes errors. A standard layout separates these assets into clear roles:

* `data/`: The `raw/` folder holds original data that never changes. The `processed/` folder holds clean data ready for training.
* `src/`: Holds only the clean, final Python code that will run in production.
* `notebooks/`: Isolated scratchpads used for quick experiments. They are never deployed.

#### 3. Standard Entry Points (Automation Ready)

Automated tools and deployment scripts need to know exactly where to look when they build your project. Standardizing the project name in `README.md` and listing dependencies line-by-line in `requirements.txt` removes all guesswork. It changes a messy personal workspace into a predictable, professional system.
