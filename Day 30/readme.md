# Day 30: End-to-End MLOps Model Deployment & Automated Health Monitoring

### The Big Idea: The Complete MLOps Production Loop

The final frontier of any Machine Learning lifecycle is moving a model out of the isolated research registry and exposing it as a live, functional production microservice endpoint.

* **Why Serving Matters:** A model cannot add business value sitting as an idle file inside a backup storage bucket. Shifting to **Production Serving** spins up a real-time REST API server gateway that listens continuously for external network data payloads and yields instant prediction scores.
* **Why Health Monitoring is Mandatory:** In enterprise production systems, servers drop packets, network ports crash, and underlying container states fail. Writing an **Automated Health Monitor (`monitor.sh`)** implements an automated shell watchdog that pings the server gateway. If the service crashes, the script detects it instantly, preventing silent system failures.

<img width="1780" height="1100" alt="image" src="https://github.com/user-attachments/assets/ac21aa39-be8f-49c5-8e1f-13b8dc28a6eb" />

Credits: https://mlflow.org/docs/latest/ml/deployment/
---

### Problem Statement
The xFusionCorp Industries engineering team requires the `fraud-detection-v2` project candidate promoted to live inference and monitored continuously. Find the highest performing run candidate based on its $F_1$ score, promote it to the Model Registry under the namespace `fraud-detector-v2`, tag it with the `@champion` deployment alias, serve the weights file over local port `5001`, and write a production shell script to verify the application's runtime health.

---

### The Code Fixes

#### 1. The Automated Verification Watchdog Script (`/root/code/monitor.sh`)
```bash
#!/usr/bin/env bash
# Strict error variable handling layout
set -u

# Probe the live served model's health gateway endpoint
if curl -sf -o /dev/null http://localhost:5001/health; then
  exit 0
fi

exit 1

```

---

### The Terminal Solution

Run this sequence of core production tools inside your command line terminal to cleanly deploy and freeze the engineering workspace:

```bash
# 1. Establish path resolution context back to the primary tracking instance
export MLFLOW_TRACKING_URI=http://localhost:5000

# 2. Boot up the real-time inference microservice in the background
nohup mlflow models serve \
    -m "models:/fraud-detector-v2@champion" \
    --port 5001 \
    --env-manager=local > /dev/null 2>&1 &

# 3. Grant system execution capabilities to your custom monitoring script
chmod +x /root/code/monitor.sh

# 4. Trigger the validation check to verify 200 OK network connectivity
bash /root/code/monitor.sh

```

---

### UI Verification Checkpoints

#### 1. Tracking and Triage Comparison View

Verify that your training metrics match the benchmark criteria before promoting. The `improved` run shows an unambiguous edge over the baseline and regression trials.

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/c644d9ef-5e49-49e5-bbbc-fac46b4ddd4b" />

#### 2. Central Registry Alias Governance

Ensure your top performer version is safely stored inside the Model Registry vault with the explicit `@champion` tracking alias attached to its line item.

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/e4ba57db-97e5-4781-9eb2-69c1d3c84e9b" />


---

### Fast Lookup: Serving Architecture Overview

```text
 1. UI REGISTRY PROMOTION       2. BACKGROUND INFERENCE ENGINE         3. AUTOMATED WATCHDOG
 
  models:/...-v2@champion  ──►   mlflow models serve --port 5001  ──►   bash /root/code/monitor.sh
          │                                   │                                    │
          ▼                                   ▼                                    ▼
 Dynamically maps to the        Spins up a lightweight Uvicorn        Pings: `GET /health`
 'improved' run artifact        REST API instance to listen for      • If returns 200 OK  ──► exit 0
 containing `f1_score: 0.92`    incoming inference payloads          • If network drops   ──► exit 1

```

