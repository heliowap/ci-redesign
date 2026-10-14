# Phase 2: Targeted Selection & Scoped Lifecycle

## Objective
Build a pure dependency selection engine that maps candidate changes to strictly necessary verification units, and scope concurrency cancellation to candidate identity.

## Steps

1. **Model Policy and Verification Units**:
   Define `scripts/ci_policy.py`:
   - Map files and directory globs to logical **Verification Units** (not jobs).
   - Distinguish frontend, backend, database migrations, shared contracts, documentation, and CI infrastructure.
   - Enforce cross-area awareness: changes to shared schemas, API specs, or auth tokens must trigger all dependent consumer units.
   - Conservative fallback: unknown paths, merge-base failures, or root config alterations select all units.

2. **Generate the Verification Plan**:
   Define `scripts/ci_plan.py`:
   - Inputs: `--base`, `--head`, `--stage` (`fast` or `premerge`), and `--draft` flag.
   - Output: JSON verification plan listing required units, selection rationale (`affected_paths`), and candidate identity (`head_sha`, `base_sha`).

3. **Lifecycle & Concurrency Grouping**:
   In `.github/workflows/ci.yml`:
   - Set candidate-scoped concurrency:
     ```yaml
     concurrency:
       group: ci-candidate-${{ github.repository }}-${{ github.event.pull_request.number || github.ref }}
       cancel-in-progress: true
     ```
   - Differentiate Draft vs Ready PR:
     - Draft PR: triggers fast feedback tier only.
     - Ready PR: triggers full applicable verification.

## Testing & Negative Scenarios
- Unit tests proving:
  - Frontend-only edit selects zero backend/database units.
  - Shared contract edit selects both frontend and backend units.
  - Unmapped file edit selects all units.
  - Draft candidate generates draft stage plan.
  - Rapid push cancels previous attempt without impacting other PR runs.

## Completion Criteria
- `scripts/ci_plan.py` evaluates diffs in < 1 second.
- Concurrency group is candidate-scoped.
- Comprehensive unit test suite covers positive and negative selection scenarios.
