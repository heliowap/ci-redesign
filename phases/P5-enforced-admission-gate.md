# Phase 5: Enforced Admission Gate

## Objective
Activate hard enforcement on the complete verdict, making `CI / Verdict` the authoritative gating check that blocks invalid, stale, or incomplete PRs.

## Steps

1. **Enforcement Modes in `ci_verdict.py`**:
   - Add `--enforce` and `--require-integration-ready` flags.
   - If in enforcing mode:
     - Return code `1` immediately if any plan unit is blocked, incomplete, or missing evidence.
     - Return code `1` if the candidate base commit does not match the live target base tip (`stale base`).
     - Return code `1` if draft status is true but integration readiness is claimed.

2. **Workflow Wiring**:
   In `.github/workflows/ci.yml`:
   ```bash
   MODE_FLAG="--enforce"
   EXTRA_FLAGS="--require-integration-ready"
   if [ "$IS_DRAFT" = "true" ]; then
     MODE_FLAG="--shadow"
     EXTRA_FLAGS=""
   fi

   python3 scripts/ci_verdict.py evaluate \
     --plan /tmp/ci_plan.json \
     --needs-json /tmp/needs.json \
     --head-sha "$GITHUB_HEAD_SHA" \
     --base-sha "$GITHUB_BASE_SHA" \
     --run-id "${{ github.run_id }}" \
     --output /tmp/ci_verdict.json \
     --summary-file "$GITHUB_STEP_SUMMARY" \
     $MODE_FLAG $EXTRA_FLAGS
   ```

3. **Ruleset Migration & Transition Protocol**:
   - Add `CI / Verdict` as a required status check in the repository ruleset.
   - Validate that existing legacy required check names are retained during transition or superseded simultaneously.
   - Prove that legitimate ready PRs pass and merge while compromised or incomplete PRs block.

## Testing & Negative Scenarios
- `test_ci_enforcement_gate.py` proving:
  - Missing, failed, or stale verdict exits 1 and blocks admission.
  - Advancing target base SHA without re-running CI rejects previous verdict.
  - Draft PR cannot claim integration readiness.
  - Valid ready PR passes and exits 0.

## Completion Criteria
- Enforced verdict gate operational in CI.
- Ruleset transition documented with zero unprotected interval.
