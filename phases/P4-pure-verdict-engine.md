# Phase 4: Pure Complete Verdict Engine

## Objective
Construct a pure, deterministic verdict evaluator that computes whether all obligations of the candidate's verification plan are satisfied by authentic, bound evidence.

## Steps

1. **Data Model (`scripts/ci_verdict.py`)**:
   - `UnitVerdict`:
     - `status`: `passed`, `reused`, `not_applicable`, or `blocked`.
     - `reason`: Machine-readable reason for status.
     - `evidence`: Bound evidence record or lookup ref.
   - `CompleteVerdict`:
     - `candidate`: `head_sha`, `base_sha`, `base_ref`, `pr_number`.
     - `stage`: `fast` or `premerge`.
     - `units`: Mapping of `unit_name` to `UnitVerdict`.
     - `overall_status`: `passed` (all required units passed/reused, all others not_applicable) or `blocked`.
     - `summary_markdown`: Formatted table for `$GITHUB_STEP_SUMMARY`.

2. **Evidence Binding Rules**:
   Evidence is accepted only if:
   - Originating workflow matches trusted CI workflow path.
   - Run attempt and run ID match active or verified historical run.
   - Input digests match current candidate's relevant files.
   - Executed matrix parts account for all declared shards.
   - Prior success is rejected if a later attempt failed for the same identity.

3. **Pure Evaluation (No Skipping Illusion)**:
   - A skipped GitHub Actions job CANNOT mark a unit `passed` or `not_applicable`.
   - `not_applicable` is only granted if the authoritative `ci_plan.py` explicitly omitted that unit from the candidate's plan.

4. **Shadow Deployment**:
   - Add a `verdict` job in `.github/workflows/ci.yml`:
     - Depends on all upstream test jobs via `needs: [...]`.
     - Runs with `if: ${{ !cancelled() }}`.
     - Evaluates plan vs needs/evidence in shadow mode (`--shadow`), publishing `$GITHUB_STEP_SUMMARY` without failing the workflow.

## Completion Criteria
- Comprehensive test suite in `scripts/tests/test_ci_verdict.py` covering:
  - Rejection of forged records and spoofed app IDs.
  - Rejection of false non-applicability.
  - Rejection of missing matrix shards.
  - Acceptance of genuine complete execution.
- Shadow job active in CI, producing visible markdown summaries on PRs.
