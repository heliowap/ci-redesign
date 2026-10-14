# Phase 0: Baseline Metrics & Dataset

## Objective
Establish a durable, reproducible baseline measurement of the existing CI pipeline performance before applying any changes.

## Steps

1. **Extract Historical Runs**:
   Query the CI provider API (e.g. GitHub Actions API via `gh run list`) for the last 30–50 runs across representative branches (`main`, `dev`, `integration` or feature PRs).
   ```bash
   gh run list --workflow ci.yml --limit 50 --json databaseId,headSha,event,status,conclusion,createdAt,updatedAt > raw_runs.json
   ```

2. **Calculate Core Metrics**:
   Build or run a metric extractor (`scripts/ci_metrics.py`) computing:
   - **Wall-clock Duration**: from run creation to completion.
   - **Aggregate Runner Minutes**: sum of execution time across all jobs and matrix shards.
   - **Percentiles**: p50, p90, and p95 wall-clock latency.
   - **Failure & Cancellation Rates**: count of infrastructure vs test failures.

3. **Establish Matched Comparison Cohort**:
   Select representative changes across distinct work types:
   - Frontend-only changes (UI/CSS/TS components).
   - Backend/API-only changes.
   - Shared contracts / cross-area interfaces.
   - Database migrations / schema alterations.
   - Documentation-only or metadata-only edits.
   - Dependency manifest updates (`package.json`, `requirements.txt`).

4. **Persist Baseline Snapshot**:
   Commit or save the sanitized metrics JSON to `docs/testing/ci-baseline.json` or equivalent.

## Completion Criteria
- Baseline JSON exists with documented sample size ($N \ge 20$).
- Baseline p95 latency and average runner minutes explicitly stated.
- Matched cohort defined for before/after comparison in Phase 9.
