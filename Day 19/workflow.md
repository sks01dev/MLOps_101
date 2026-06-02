
## Phase 1: Establish the Baseline Checkpoint## 1. Check out the production branch

git checkout main


* What it alters: Switches your active Git workspace to the main branch.

## 2. Tag the production baseline

git tag -a v1.0 -m "Baseline dataset v1.0"


* What it alters: Creates an immutable Git tag named v1.0 locked to the current commit, freezing the V1 metadata tracking hashes in history.

------------------------------
## Phase 2: Sandbox Isolation## 3. Create and switch to the experiment branch

git checkout -b v2-improved


* What it alters: Creates the v2-improved branch and moves you into it. It inherits the exact same V1 data and tracking files from main.

------------------------------
## Phase 3: Content Mutation & Extraction## 4. Unlock the data file link

dvc unprotect data/raw/transactions.csv


* What it alters: Breaks the read-only cache link on data/raw/transactions.csv so it can be safely modified without corrupting the global data vault.

## 5. Inject the new data payload

cp data/raw/transactions_v2.csv data/raw/transactions.csv


* What it alters: Copies the rows of transactions_v2.csv over transactions.csv. At this moment, both files on your disk hold identical V2 data, but the pipeline outputs (train.csv, test.csv) are still stuck on V1.

------------------------------
## Phase 4: Re-Indexing & Compute Execution## 6. Calculate the new entry-point hash

dvc add data/raw/transactions.csv


* What it alters: Recomputes the file's MD5 hash, updates data/raw/transactions.csv.dvc with the new V2 hash code, and moves the V2 payload securely into the hidden .dvc/cache/ vault.

## 7. Execute the data pipeline processing

dvc repro


* What it alters: Detects the input hash change and runs your Python data-split scripts. This overwrites clean_transactions.csv, train.csv, and test.csv on your disk with fresh V2 data and records their new output hash signatures inside dvc.lock.

------------------------------
## Phase 5: Locking the Ledger## 8. Stage and lock the text tracking metadata

git add dvc.lock data/raw/transactions.csv.dvc
git commit -m "Upgrade dataset to v2 and update pipeline pointers"


* What it alters: Stages and commits only the text tracking recipe files (dvc.lock and the .dvc pointer) into the v2-improved branch timeline, keeping the heavy CSV files completely out of Git.

------------------------------
## Phase 6: Time-Travel Verification## 9. Revert the text pointers to baseline

git checkout main


* What it alters: Swaps the active branch to main. Your text files (.dvc and dvc.lock) instantly flip back to their old V1 hash values. Note: The actual data inside transactions.csv and the processed folder is still V2 data right now.

## 10. Synchronize physical data with the pointers

dvc checkout


* What it alters: DVC reads the V1 text hashes brought back by Git, pulls the corresponding V1 data layers out of the hidden cache, and completely overwrites transactions.csv, clean_transactions.csv, train.csv, and test.csv on your disk back to their original states. transactions_v2.csv remains completely untouched.

------------------------------
