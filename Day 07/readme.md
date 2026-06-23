# Day 7: Packaging ML Code as a Python Distribution

### Why Packaging is Crucial (The Big Idea)

Imagine you build a great machine learning project. You want to give it to your deployment team so they can run it on a production server.

#### Without Packaging: The Problem

If you do not package your project, your code is just a collection of loose Python files sitting inside folders on your computer. If you try to share it like this:

1. **Manual File Syncing:** The deployment team has to copy-paste your exact folder structure onto their server by hand.
2. **Guessing Dependencies:** They have to manually look through your code, guess which libraries you used, and install them one by one (`pip install pandas`, `pip install numpy`, etc.).
3. **Version Mismatch Crashes:** If their server is running an older version of Python, or a different version of `scikit-learn`, your code will crash instantly.

#### With Packaging: The Solution

**Packaging** fixes all of this by combining your entire code project into a single, clean file called a **Wheel (`.whl`)**.

* **Turn Folders into a Library:** It changes your loose files into a standard, ready-to-use Python library (just like `pandas` or `numpy`). The deployment team can now install your whole project onto any server with one single command: `pip install fraud_detection-0.1.0-py3-none-any.whl`.
* **Lock Down the Rules:** The package saves the exact rules your code needs to run (like requiring Python version `>=3.10` and needing `pandas`). When someone installs your file, Python reads these rules and automatically downloads any missing libraries for them, preventing crashes.

---

### Problem Statement

The xFusionCorp Industries deployment team needs the fraud-detection model code packaged as an installable Python distribution. A draft `pyproject.toml` exists at `/root/code/fraud-detection/`, but it does not build a wheel that meets the team's standard. Correct the file and produce a compliant package under `dist/`.

The corrected `pyproject.toml` must satisfy every one of the following:

* Declares a `[build-system]` section using `setuptools.build_meta`.
* `name` is exactly `fraud_detection`.
* `version` is `0.1.0`.
* `requires-python` is `>=3.10`.
* `dependencies` is `["scikit-learn", "pandas", "numpy"]`.

---

### Solution

#### 1. Configure the Packaging Rules

Open `/root/code/fraud-detection/pyproject.toml` and paste this clean configuration:

```toml
[project]
name = "fraud_detection"
version = "0.1.0"
description = "Fraud detection model for xFusionCorp Industries"
requires-python = ">=3.10"
dependencies = [
    "scikit-learn",
    "pandas",
    "numpy"
]

[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"

[tool.setuptools.packages.find]
where = ["src"]

```

#### 2. Run the Build Command

Open your terminal, move into the project folder, and run Python's built-in packaging module:

```bash
# Move to the project folder
cd /root/code/fraud-detection

# Compile the package
python3 -m build

```

This creates a new folder named `dist/` containing your final production file: `fraud_detection-0.1.0-py3-none-any.whl`.

---

### What Every Configuration Block Does

| Section Name | What it tells Python | Why it is needed |
| --- | --- | --- |
| **`[build-system]`** | Use `setuptools` and `wheel` as the tools to package this project. | This is the actual machine that zips your loose files into a single installable file. |
| **`name = "fraud_detection"`** | This is the official name of your custom library. | Sets the name so people can type `import fraud_detection` in their scripts later. |
| **`requires-python = ">=3.10"`** | This code needs at least Python version 3.10 to work. | Stops people from running your code on old systems where your code features will crash. |
| **`dependencies = [...]`** | Lists the third-party libraries (`pandas`, `numpy`, etc.) your code uses. | Tells the computer to automatically download these background packages during installation. |
| **`[tool.setuptools.packages.find]`** | Look inside the `src/` folder to locate the code files. | Automatically finds and grabs all your internal scripts so you don't have to list them one-by-one. |

---

### Summary of Day 7

* **From Folders to Libraries:** Learned how to turn a random folder of loose code scripts into a professional, shareable Python package.
* **Central Rules:** Used `pyproject.toml` to list all project requirements and details in one clear setup file.
* **Production Deployment:** Generated an official `.whl` file, meaning any server in the company can now install and run your machine learning pipeline perfectly.
