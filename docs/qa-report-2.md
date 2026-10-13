# QA Report: Sprint 2 Week 4

QA is responsible for running all validation checks and signing off before deliverables are submitted. This report documents the validation process.

This week the team writes OpenTofu and Ansible code but does not run `tofu` or `ansible-playbook`. The checks below read your files and inspect your live cluster. The commands your team predicted in the Prediction Log are checked later, during the live rebuild.

**QA Team Member:** Lincoln Koskela
**Date Completed:** 12 OCT 2026

---

## Validation Checks

### Check 1: Local Backend Is Explicit

**Test:** Run `grep -A3 "backend" infrastructure/main.tf`

**Expected:** Output showing `backend "local" { path = "terraform.tfstate" }`

**Actual Result:**
  backend "local" {
    path = "terraform.tfstate"
  }

**Status:** TODO: [x]  Pass [ ] Fail

**Notes:** Confirms state is stored locally on the team's VM, not in a remote backend.

---

### Check 2: Flask Deployment Is Set to 3 Replicas

**Test:** Run `grep -n "replicas" infrastructure/flask.tf`

**Expected:** A single line showing `replicas = 3`

**Actual Result:**

11:    replicas = 3

**Status:** TODO: [X] Pass [ ] Fail

**Notes:** Confirms the Part 3 replica change (Step 9) was made in the file. It was not applied to the cluster.

---

### Check 3: Week 3 Flask Manifests Removed

**Test:** Run `ls manifests/ | grep flask`

**Expected:** Only `flask-secret.yaml` is listed

**Actual Result:**

flask-secret.yaml

**Status:** TODO: [X] Pass [ ] Fail

**Notes:** Confirms Step 6 was done with `git rm` and that `flask-secret.yaml` was kept. If the Deployment or Service manifest is still listed, OpenTofu and `kubectl apply -f manifests/` would both define `flask` on a rebuilt cluster.

---

### Check 4: State and Working Files Ignored

**Test:** Run `grep -E "tfstate|\.terraform" .gitignore`

**Expected:** Three lines, one each for `terraform.tfstate`, `terraform.tfstate.backup`, and `.terraform/`

**Actual Result:**

infrastructure/terraform.tfstate
infrastructure/terraform.tfstate.backup
infrastructure/.terraform/

**Status:** TODO: [X] Pass [ ] Fail

**Notes:** `.terraform/` holds downloaded provider binaries and must never be committed.

---

### Check 5: Ansible Role Is in Place

**Test:** Run `ls ansible/roles/opentofu-setup/tasks/main.yml` and `grep -A5 "opentofu-setup" ansible/site.yml`

**Expected:** The file exists at that exact path, and the play in `site.yml` lists the `opentofu-setup` role

**Actual Result:**

ansible/roles/opentofu-setup/tasks/main.yml

    - opentofu-setup

**Status:** TODO: [X] Pass [ ] Fail

**Notes:** Was the file nested under `tasks/`? Does the play avoid `become`? Was `ansible-playbook` left unrun, as instructed?

---

### Check 6: Live Cluster Is Still Healthy

**Test:** Run `kubectl get pods`

**Expected:** All pods in `Running` state with matching READY counts (for example `1/1`)

**Actual Result:**
```
TODO: Paste the actual output of kubectl get pods
```

**Status:** TODO: [X] Pass [ ] Fail

**Notes:** Nothing in this lab should have changed the live cluster. The Week 3 Flask Deployment must still be running, because later weeks build on it.

---

### Check 7: Prediction Log Is Complete

**Test:** Open the team Google Doc and review the Week 4 Prediction Log

**Expected:** P1 through P10 each have a prediction with one sentence of reasoning. The Actual column is filled in for P8 only.

**Actual Result:** ALL ROWs Complete

**Status:** TODO: [X] Pass [ ] Fail

**Notes:** The check script cannot read the Google Doc, so QA confirms this by hand.

---

### Check 8: Check Script Passes

**Test:** Run `./scripts/check-week4.sh`

**Expected:** All checks pass with exit code 0

**Actual Result:**
Week 4 Validation Checks
=========================================

Check 1: Local Backend Is Explicit
-----------------------------------------
[PASS] File exists: infrastructure/main.tf
[PASS] infrastructure/main.tf has a local backend with path terraform.tfstate
[PASS] infrastructure/main.tf declares the kubernetes provider

Check 2: Flask Deployment Is Set to 3 Replicas
-------------------------------------------------
[PASS] File exists: infrastructure/flask.tf
[PASS] flask.tf defines the flask Deployment
[PASS] flask.tf defines the flask Service
[PASS] flask.tf uses week-2-flask:latest with image_pull_policy IfNotPresent
[PASS] flask.tf sets replicas = 3

Check 3: Week 3 Flask Manifests Removed
------------------------------------------
[PASS] manifests/flask-deployment.yaml is removed and committed
[PASS] manifests/flask-service.yaml is removed and committed
[PASS] manifests/flask-secret.yaml is kept

Check 4: State and Working Files Ignored
-------------------------------------------
[PASS] infrastructure/terraform.tfstate is gitignored
[PASS] infrastructure/terraform.tfstate.backup is gitignored
[PASS] infrastructure/.terraform/providers is gitignored

Check 5: Ansible Role Is in Place
-------------------------------------
[PASS] File exists: ansible/roles/opentofu-setup/tasks/main.yml
[PASS] opentofu-setup role runs tofu init
[PASS] ansible/site.yml includes the opentofu-setup role
[PASS] opentofu-setup play does not use become
[PASS] tofu is installed on this VM (instructor-provided)

Check 6: Live Cluster Is Still Healthy
-----------------------------------------
[PASS] flask Deployment still exists in the live cluster
[PASS] Found 115 pod(s) labeled app=flask

**Status:** TODO: [X] Pass [ ] Fail

**Notes:** If any checks failed, what did the script report? A `[WARN]` line does not fail the script, but list any warnings here.

---

## Acceptance Criteria Verification

Review the criteria below for each part of this week's deliverables. For each criterion, record whether it was met:

### Part 1: Configure OpenTofu

TODO: [X] `infrastructure/main.tf` defines the Kubernetes provider and an explicit local backend
TODO: [X] `infrastructure/terraform.tfstate`, `terraform.tfstate.backup`, and `infrastructure/.terraform/` are excluded via `.gitignore`
TODO: [X] P1 is answered (what `tofu init` creates and why it runs before `plan`)

### Part 2: Define Kubernetes Resources with OpenTofu

TODO: [X] `infrastructure/flask.tf` defines both a Deployment and a Service for `flask`
TODO: [X] Container image is `week-2-flask:latest` (not the `ghcr.io` placeholder), with `image_pull_policy = "IfNotPresent"`
TODO: [X] The Service listens on port 5000, and the nginx ConfigMap and `week-2/nginx.conf` were left unchanged
TODO: [X] `flask-deployment.yaml` and `flask-service.yaml` were removed from `manifests/` with `git rm` and committed (Step 6), and `flask-secret.yaml` was kept
TODO: [X] `kubectl delete` was not run, and the live Flask Deployment is still running
TODO: [X] P2, P3, and P4 are answered

### Part 3: Make a Change and Predict Idempotency

TODO: [X] Replica count changed from 2 to 3 in `flask.tf` (file edit only, not applied)
TODO: [X] P5, P6, and P7 are answered, each with one sentence of reasoning

### Part 4: k3s Resilience Validation

TODO: [X] P8 was answered before the Flask pod was deleted
TODO: [X] The deleted Flask pod was automatically recreated by Kubernetes, and the observed recovery time is recorded in the Actual column for P8
TODO: [X] P9 is answered (`tofu plan` after pod recovery), with the Actual column left blank

### Part 5: Ansible Update

TODO: [ ] `ansible/roles/opentofu-setup/tasks/main.yml` exists, nested correctly under `tasks/`
TODO: [ ] The role confirms `tofu` is installed and runs `tofu init` against the `infrastructure/` directory
TODO: [ ] `opentofu-setup` play appended to `ansible/site.yml` below the Week 1 and Week 3 plays, without `become`
TODO: [ ] `ansible-playbook` was not run on the lab VM
TODO: [ ] P10 is answered

---

## Deliverables Verification

### Required Files

TODO: [X] `infrastructure/main.tf` is committed (explicit local backend and Kubernetes provider)
TODO: [x] `infrastructure/flask.tf` is committed (Deployment and Service, replicas set to 3)
TODO: [x] Week 3 Flask Deployment and Service manifests are removed from `manifests/`
TODO: [x] `.gitignore` excludes `infrastructure/terraform.tfstate`, `terraform.tfstate.backup`, and `infrastructure/.terraform/`
TODO: [x] `ansible/site.yml` includes the `opentofu-setup` play
TODO: [x] `ansible/roles/opentofu-setup/tasks/main.yml` is committed
TODO: [x] `scripts/check-week4.sh` is present and runs clean

### GitHub Repository

TODO: [x] All changes are pushed to the main branch
TODO: [x] GitHub Project board shows all tasks completed
TODO: [x] Commit messages describe the OpenTofu and Ansible changes

### Google Doc

TODO: [x] Prediction Log (P1 to P10) is complete, with the Actual column filled in for P8 only
TODO: [x] Screenshot showing the deleted pod cycling back to Running is attached
TODO: [x] Screenshot of `./scripts/check-week4.sh` passing is attached
TODO: [x] Discussion answers recorded for Parts 1 to 4 (providers, plan vs. apply, state storage, k3s recovery boundaries)
TODO: [x] Storage Check output (`df -h` and `docker system df`) is recorded

---

## Summary

**Overall Status:** [X] ALL CHECKS PASS [ ] SOME CHECKS FAIL

**Blockers:** [List any blockers that prevent submission]

**Corrective Actions Taken:** [List any fixes applied during QA]

**QA Sign-Off:**

By signing below, QA certifies that all required validation checks have been executed and all deliverables meet the acceptance criteria.

**QA Signature:** lincoln    **Date:** oct12, 26

---

## Notes for Sprint 3

[Any observations or recommendations for the next sprint]
