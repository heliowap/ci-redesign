# Phase 1: Authority Probe & Feasibility

## Objective
Discover repository security boundaries, active branch protections, rulesets, and runner permissions to ensure the planned verdict gate is enforceable.

## Steps

1. **Probe Branch Protections & Rulesets**:
   Run CLI commands to inspect repository governance:
   ```bash
   gh api repos/{owner}/{repo}/rulesets
   gh api repos/{owner}/{repo}/branches/{base}/protection
   ```
   Check:
   - Are rulesets active, evaluate, or disabled?
   - What status checks are currently required (`required_status_checks`)?
   - Are names matching exact workflow job strings or status contexts?

2. **Probe Publisher & Runner Authority**:
   - Inspect GitHub Actions token permissions (`GITHUB_TOKEN` vs custom App ID).
   - Verify whether GitHub Actions App ID 15368 is recognized.
   - Confirm that candidate branch PRs cannot tamper with publisher secrets or publish arbitrary container images.

3. **Produce Feasibility Report**:
   Document findings in `docs/ci-authority-feasibility.md`:
   - Can a single unified status check (`CI / Verdict`) replace multiple legacy required checks?
   - Are draft PRs supported by branch rules (e.g., bypass or non-blocking)?
   - What manual approvals or human actions are strictly required to activate enforcement?

## Completion Criteria
- Rulesets and branch protection status documented for all primary target branches.
- Authority feasibility verified: publisher identity separation proved.
- Plan documented for shadow observation (Phase 4) transitioning to hard gate enforcement (Phase 5).
