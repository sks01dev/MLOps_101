# Day 29: Production-Grade MLflow Storage Architecture (SQL + S3 Object Storage)

### The Quick Intuition

* **Why Separate Architecture?** In enterprise MLOps, a simple local filesystem crashes under load. Production deployments split operations: a **relational database (PostgreSQL)** indexes highly structural metadata, while an **S3-compatible object store (SeaweedFS)** archives heavy binary model artifacts.
* **The Gotcha (The Missing Link):** When using `s3://` artifact destinations, MLflow natively tries to connect to public Amazon Web Services (AWS) clouds. If your company deploys an internal open-source storage gateway (like SeaweedFS or MinIO), you **must** inject an explicit environment mapping rule (**`MLFLOW_S3_ENDPOINT_URL`**) so the server routes network traffic to your private on-premise servers instead of public internet nodes.

---

### Problem Statement

The xFusionCorp Industries ML platform team rolled out a production-style tracking environment. While metadata logging to PostgreSQL completes cleanly, model artifact uploads fail and hang because the MLflow tracking server cannot find its custom local storage gateway. Reconfigure the infrastructure script wrapper at `/root/code/start-mlflow.sh` to introduce proper S3 routing bindings, restart the service, and verify the full logging loop.

---

### The Code Fix

Open `/root/code/start-mlflow.sh` and add the missing object storage routing link environment variable right before launching the server executable:

```bash
#!/bin/bash
# Start the MLflow tracking server with production-style wiring
set -e

# Provide object store authentication tokens
export AWS_ACCESS_KEY_ID=weedadmin
export AWS_SECRET_ACCESS_KEY=weedadmin123

# FIX: Force MLflow to route S3 requests locally instead of querying public AWS
export MLFLOW_S3_ENDPOINT_URL=http://localhost:8333

exec mlflow server \
  --backend-store-uri postgresql://mlflow:mlflow123@localhost:5432/mlflow \
  --artifacts-destination s3://mlflow-artifacts \
  --host 0.0.0.0 --port 5000 \
  --allowed-hosts '*' --cors-allowed-origins '*'

```

---

### The Terminal Solution

Execute these commands to recycle the server process and re-run your validation suite:

```bash
# 1. Reset the server process tree to load the updated environment rule maps
bash /root/code/restart-mlflow.sh

# 2. Run the end-to-end smoke test script pass
python3 /root/code/log_test_run.py

```

---

### The Production Dual-Pillar Handoff Flow

Once configured, the tracking server delegates metadata and artifact management into two robust, independent endpoints:

```text
                               ┌───────────────────────┐
                               │  python3 code script  │
                               └───────────┬───────────┘
                                           │
                                  (mlflow.sklearn.log)
                                           │
                                           ▼
                               ┌───────────────────────┐
                               │ MLflow Server (:5000) │
                               └─────┬───────────┬─────┘
                                     │           │
       ┌─────────────────────────────┘           └─────────────────────────────┐
       ▼                                                                       ▼
 🏛️  [ PostgreSQL Ledger Database ]                               📂  [ SeaweedFS S3 Storage Vault ]
 postgresql://localhost:5432/mlflow                           MLFLOW_S3_ENDPOINT_URL=http://localhost:8333
  ├── Schema tables created on first init                      ├── Targets custom internal port bucket
  ├── Logs: run_name, params, metrics strings                  ├── Saves: `MLmodel` configuration recipe
  └── Highly indexed for lightning sorting                     └── Saves: `model.pkl` serialization binaries

```

