# Day 5: Pipeline Automation with Makefiles
### Problem Statement
The xFusionCorp Industries ML team uses a Makefile to automate common tasks: data processing, training, testing, and cleanup. The current draft of the Makefile fails when running make all. The goal is to correct the syntax errors, ensure proper directory handling, and make the entire pipeline complete successfully without human intervention.
### Solution
```makefile
# xFusionCorp Industries Fraud Detection Pipeline

.PHONY: setup data train test clean all

setup:
	python3 -m venv mlops-venv && mlops-venv/bin/pip install -r requirements.txt

data:
	python src/data/process_data.py

train:
	python src/models/train.py

test:
	pytest tests/

clean:
	find . -type d -name "__pycache__" -exec rm -rf {} +
	rm -rf .pytest_cache
	rm -rf models/*

all: setup data train test

```
### What Every Command Does
 * **setup:** Creates your local virtual environment (mlops-venv) and installs all the required packages automatically.
 * **data:** Runs your data preprocessing and cleaning script.
 * **train:** Starts your training code to create and save the model.
 * **test:** Runs your automated testing scripts to check if your code logic is correct.
 * **clean:** Finds and deletes every hidden Python cache folder (__pycache__) and empties out old model files so your workspace stays fresh.
 * **all:** Runs setup, data, train, and test automatically back-to-back in that exact sequence.
### The Most Important Purpose of a Makefile
Every time you start a new Machine Learning project, you find yourself running the exact same steps over and over again:
 1. You create a fresh virtual environment.
 2. You install your data science libraries.
 3. You run your data preparation code.
 4. You run your model training file.
 5. You run your model evaluation or testing code.
Instead of manually typing out these 5 separate commands every single time you want to build or run a project, a Makefile wraps them into a single file.
 * **The Main Benefit:** You only need to write your core training and testing code once.
 * **The Single Shortcut:** When you or a teammate move to a new machine or want to rerun the whole experiment, you don't type out multiple commands. You just type make all and the system sets up everything and runs the code for you in seconds.
