# Phase 6: Proof Reuse, Bounded Retry & Runner Sizing

## Objective
Reduce aggregate CI duration and eliminate flaky reruns through cryptographic proof reuse, single-retry infrastructure resilience, and measurement-backed runner sizing.

## Steps

1. **Same-PR Proof Reuse (`scripts/ci_proof_key.py`)**:
   - Compute a deterministic **Proof Key** for each unit based on:
     - Relevant input file hashes (git tree digests of the unit's source root).
     - Dependency manifest hashes.
     - Execution recipe and environment identity.
   - If a new commit to the PR does not touch a unit's inputs, reuse the verified evidence from the previous run without re-executing.
   - Failure visibility: A later rerun that fails revokes earlier success. A unit never claims reuse if its latest attempt failed.

2. **Cross-PR & Release Reuse Boundary**:
   - Allow reuse across PRs and into release candidates only when:
     - Originating run authenticated by authentic GitHub Actions App ID (15368).
     - Input digests, policy version, and recipe match identically.
   - Ordinary PR dependency cache is NEVER trusted as cross-PR proof storage.
   - Fallback: `--disable-reuse` executes all applicable checks cleanly.

3. **Single Bounded Infrastructure Retry (`scripts/ci_retry.py`)**:
   - Positively classify transient infrastructure failures:
     - Runner disconnection / agent lost communication.
     - Docker daemon socket connection timeout.
     - Container registry 502/503/504 HTTP errors.
     - Runner disk space exhaustion.
   - Hard Rule: Assertion failures, test errors, typecheck/lint errors, and unknown exceptions NEVER retry.
   - Allow at most one automatic retry per unit/candidate (`max_retries = 1`).

4. **Matched Runner Sizing & Sharding (`scripts/ci_sizing.py`)**:
   - Measure test suite wall-clock latency vs. aggregate runner minutes across candidate runner sizes (2-core vs 4-core vs 8-core) and shard counts (1, 2, 3, 4).
   - Recommend sizing only if it reduces wall-clock latency without inflating total billable runner minutes by more than 10%.

## Completion Criteria
- Unit tests cover proof key generation and invalidation.
- Infrastructure retry fires only on classified transient errors; test errors fail immediately.
- Runner sizing decisions documented with measured before/after numbers.
