# Day 9: Standardizing Projects with Cookiecutter Templates

### The Big Idea: Why Use Templates?

When starting a new Machine Learning project, developers waste hours manually creating folders, writing documentation headings, and copying setup files.

* **Without Templates:** Everyone builds random layouts. One engineer uses `data/`, another uses `raw_data/`, and a third dumps files everywhere. This breaks automated deployment tools because they don't know where to look.
* **With Templates:** You design a master blueprint folder **once**. With one command, the system stamps out an identical, perfectly organized project structure instantly.

---

### The Blueprint Layout

Your template folder must look exactly like this on disk:

```text
/root/code/mlops-template/          <-- The Blueprint Box
├── cookiecutter.json               <-- Defines the variables
└── {{cookiecutter.project_name}}/  <-- The dynamic template directory
    ├── data/
    ├── models/
    ├── src/
    ├── tests/
    ├── README.md
    └── requirements.txt

```

---

### The Files Needed to Implement It

#### 1. The Variable Brain (`cookiecutter.json`)

Defines the questions, variables, and default settings.

```json
{
  "project_name": "my-ml-project",
  "author": "xFusionCorp",
  "python_version": "3.11",
  "ml_framework": ["sklearn", "pytorch", "tensorflow"]
}

```

#### 2. The Smart Requirements (`requirements.txt`)

Uses logic to print **only** the selected framework.

```text
{% if cookiecutter.ml_framework == 'sklearn' %}
scikit-learn
{% elif cookiecutter.ml_framework == 'pytorch' %}
torch
{% elif cookiecutter.ml_framework == 'tensorflow' %}
tensorflow
{% endif %}

```

#### 3. The Auto-Filled Documentation (`README.md`)

Dynamically pastes the values directly into your text.

```markdown
# {{ cookiecutter.project_name }}
**Author:** {{ cookiecutter.author }}

```

---

### The Execution Automation

Run this command inside your terminal to process the layout rules and generate the customized project folder:

```bash
cookiecutter /root/code/mlops-template/ -o /root/code/ --no-input project_name=churn-model ml_framework=sklearn

```

#### What happens behind the scenes:

1. The engine reads the configuration boundaries inside `cookiecutter.json`.
2. It overrides default variables with your terminal arguments (`churn-model` and `sklearn`).
3. It performs a find-and-replace operation across all filenames and text inside `{{cookiecutter.project_name}}/` to drop a clean, concrete directory at your output path.
