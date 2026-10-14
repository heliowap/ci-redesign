# Phase 9: Efficiency Proof & Legacy Retirement

## Objective
Demonstrate quantifiable efficiency improvements on the matched cohort established in Phase 0, retire temporary legacy pipelines, and publish the final report and recovery runbook.

## Steps

1. **Evaluate Matched Comparison Cohort**:
   Run `scripts/ci_efficiency_demonstration.py` across the cohort of representative commits defined in Phase 0:
   - Compute aggregate runner minutes before vs. after.
   - Compute p95 wall-clock latency before vs. after.
   - Separate execution categories: preparation, test execution, caching, and release bundle publishing.

2. **Verify Performance Acceptance Targets**:
   Verify against the target criteria:
   - $\ge 30\%$ reduction in aggregate runner minutes across the matched cohort.
   - $\ge 30\%$ reduction in p95 wall-clock candidate latency.
   - Zero decrease in automated regression detection (full suite membership accounted for).

3. **Publish Efficiency Report**:
   Generate `docs/testing/ci-efficiency-report.md` containing:
   - Summary table with exact percentages, run counts, and confidence intervals.
   - Breakdown by change category (frontend, backend, shared contracts, docs).
   - Evidence of avoided builds and reused proofs.

4. **Retire Replaced & Duplicate Workflows**:
   - Safely remove duplicate workflows and temporary comparison jobs.
   - Verify that all active branch protection rulesets and status check names match the final pipeline.

5. **Live PR Validation & Rollback Protocol**:
   - **Live Validation**: Ensure at least one real pull request undergoes full candidate execution and passes the enforced `CI / Verdict` check on GitHub Actions before declaring completion.
   - **Rollback Runbook**: Document the emergency rollback procedure to restore conservative full execution without leaving an unprotected window.

## Completion Criteria
- Measured efficiency gains meet or exceed the 30% reduction targets.
- Final report published in repository documentation.
- Legacy workflows retired with no dangling required status checks.
- Live PR validation successfully confirmed on GitHub Actions.
