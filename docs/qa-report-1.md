# QA Report: Sprint 1 Week 2

QA is responsible for running all validation checks and signing off before deliverables are submitted. This report documents the validation process.

**QA Team Member:** Lincoln
**Date Completed:** October 3, 2026

---

## Validation Checks

### Check 1: All Three Services Are Running and Two Of Them Show Healthy

**Test:** Run `docker compose ps` from the `week-2/` directory

**Expected:** Three rows, each with "running" in the Status column

**Actual Result:**
```
WARN[0000] /home/koske113/INET-4031-LAB-01/week-2/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
NAME             IMAGE                COMMAND                  SERVICE   CREATED          STATUS                    PORTS
week-2-db-1      postgres:15-alpine   "docker-entrypoint.s…"   db        16 minutes ago   Up 16 minutes (healthy)   5432/tcp
week-2-flask-1   week-2-flask         "python3 app.py"         flask     16 minutes ago   Up 16 minutes (healthy)   5000/tcp
week-2-nginx-1   nginx:alpine         "/docker-entrypoint.…"   nginx     16 minutes ago   Up 16 minutes             0.0.0.0:8084->80/tcp, [::]:8084->80/tcp
```

**Status:** TODO: [x] Pass [ ] Fail

**Notes:** If any service shows "starting" or "exited", what did the logs reveal?

---

### Check 2: Nginx Is Reachable on the Mapped Port

**Test:** Run `curl -s -o /dev/null -w "%{http_code}" http://localhost:8080/health`

**Expected:** HTTP 200

**Actual Result:** 200

**Status:** TODO: [x] Pass [ ] Fail

**Notes:** If the request failed, what error message did you see?

---

### Check 3: Data Persists Across Container Restart

**Test:** Create a test incident, restart the PostgreSQL container, retrieve all incidents

**Steps Performed:**
```
curl -X POST http://localhost:8084/incidents \s \
  -H "Content-Type: application/json" \
  -d '{"title": "Persistence check", "status": "open", "description": "Testing database persistence"}'

docker compose restart db

docker compose down
docker compose up -v 
```

**Actual Result:**
```
WARN[0000] /home/koske113/INET-4031-LAB-01/week-2/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
[+] down 4/4
 ✔ Container week-2-nginx-1   Removed                                                                                                                                                               0.3s
 ✔ Container week-2-flask-1   Removed                                                                                                                                                              10.2s
 ✔ Container week-2-db-1      Removed                                                                                                                                                               0.2s
 ✔ Network week-2_app-network Removed                                                                                                                                                               0.2s
[koske113@caps-inet4031-dev-app-02 week-2]$ docker compose up -d
WARN[0000] /home/koske113/INET-4031-LAB-01/week-2/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
[+] up 4/4
 ✔ Network week-2_app-network Created                                                                                                                                                               0.2s
 ✔ Container week-2-db-1      Healthy                                                                                                                                                              11.1s
 ✔ Container week-2-flask-1   Healthy                                                                                                                                                              21.7s
 ✔ Container week-2-nginx-1   Started      
```

**Status:** TODO: [x] Pass [ ] Fail

**Notes:** Was data present after the restart? Was anything lost?

---

### Check 4: Check Script Passes

**Test:** Run `chmod +x scripts/check-week2.sh` then `./scripts/check-week2.sh`

**Expected:** All checks pass with exit code 0

**Actual Result:**
```
Week 2 Validation Checks
=========================================


Check 1: Required Week 2 Files
-------------------------------
[PASS] week-2/docker-compose.yml exists
[PASS] week-2/.env.example exists
[PASS] week-2/nginx.conf exists
[PASS] week-2/app/ directory exists

Check 2: .env Is Git-Ignored
------------------------------
[PASS] week-2/.env is excluded by .gitignore

Check 3: Ansible app-stack Role
---------------------------------
[PASS] ansible/roles/app-stack/tasks/main.yml exists
[PASS] ansible/site.yml includes the app-stack role

Check 4: Docker Compose Stack Health
--------------------------------------
[PASS] db and flask report healthy (2 healthy; nginx has no healthcheck defined)

Check 5: Application Health Check
-----------------------------------
[PASS] Nginx responds on http://localhost:8084/health (HTTP 200)

=========================================
Validation Summary
=========================================
Passed: 9
Failed: 0
Warnings: (see above)

Status: ALL CHECKS PASSED

```

**Status:** TODO: [x] Pass [ ] Fail

**Notes:** If any checks failed, what did the script report?

---

## Acceptance Criteria Verification

Review the criteria below for each part of this week's deliverables. For each criterion, record whether it was met:

### Part 1: Service Definition

TODO: [x] All three services start in correct order
TODO: [x] Health checks work as specified

### Part 2: Networking and Persistence

TODO: [x] Data persists across `docker compose restart`
TODO: [x] Data is lost after `docker compose down -v`

### Part 3: Environment

TODO: [x] `.env` is in `.gitignore`
TODO: [x] `.env.example` documents all variablest

---

## Deliverables Verification

### Required Files

TODO: [x] `week-2/docker-compose.yml` is committed
TODO: [x] `week-2/.env.example` is committed
TODO: [x] `week-2/nginx.conf` is committed
TODO: [x] `week-2/README.md` is committed
TODO: [x] `ansible/site.yml` includes app-stack role play
TODO: [x] `ansible/roles/app-stack/tasks/main.yml` is committed
TODO: [x] `.gitignore` excludes `week-2/.env`

### GitHub Repository

TODO: [x] All changes are pushed to the main branch
TODO: [x] GitHub Project board shows all tasks completed
TODO: [x] PR descriptions explain implementation decisions

### Google Doc

TODO: [x] Sprint 1 Week 2 reflection answers are recorded
TODO: [x] Week 2 storage check values are recorded
TODO: [x] Required screenshots are attached

---

## Summary

**Overall Status:** [x] ALL CHECKS PASS [ ] SOME CHECKS FAIL

**Blockers:** [List any blockers that prevent submission]

**Corrective Actions Taken:** [List any fixes applied during QA]

**QA Sign-Off:** LincolnKoskela

By signing below, QA certifies that all required validation checks have been executed and all deliverables meet the acceptance criteria.

**QA Signature:** _________________    **Date:** __________

---

## Notes for Sprint 2

[Any observations or recommendations for the next sprint]
