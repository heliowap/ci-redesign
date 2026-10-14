# Phase 8: Immutable Release Bundles & Pull-by-Digest

## Objective
Eliminate production host-side image builds and race conditions by packaging verified multi-platform container digests into an authenticated, immutable release bundle consumed strictly by digest.

## Steps

1. **Model the Release Bundle (`scripts/ci_release_bundle.py`)**:
   The release bundle schema binds:
   - `source_commit_sha` & `source_tree_sha`.
   - `platform_digests`: map of service names to platform pins (`ghcr.io/org/repo@sha256:...` for `linux/amd64` and `linux/arm64`).
   - `configuration_identities`: sha256 of all root and service compose files.
   - `migration_manifest_identity`: sha256 of all schema migration revisions.
   - `global_verification_obligations`: proof that image integrity, contracts, and smoke tests passed.
   - `provenance`: builder ID, authentic App ID (15368), run ID, run attempt, and isolation flags.

2. **Security & Authentication Boundaries (Codex Finding Lesson)**:
   Because bundle JSON is publisher-supplied data, the admission and deployment engines must NOT trust unsigned bundles:
   - **Image Subject Allowlist**: Every platform digest must match an allowlisted repository prefix (e.g., `ghcr.io/my-org/`). Rejection occurs if any digest references an unknown or attacker registry.
   - **Detached Publisher Attestation**: When the bundle is built in the trusted CI workflow, generate `<bundle>.json.attestation` using a secure HMAC key (`RELEASE_BUNDLE_ATTESTATION_KEY`) or cryptographic signing tool (cosign/sigstore).
   - **Fail-Closed Verification**: The deployment runner (`sync-main.sh` or k8s deployer) must verify the attestation before reading any digest. Without the attestation key, admission fails closed with exit code 1.

3. **Unchanged Service Digest Reuse**:
   - Compare service build inputs (Dockerfile, context directory, package lockfiles, base image pins) against the prior release bundle.
   - If a service's inputs are 100% identical, reuse its existing platform digests in the new bundle.
   - Force complete rebuild if `--force-rebuild` is passed or if shared root inputs changed.

4. **Pull-by-Digest Release Deployment**:
   In deployment scripts:
   - Forbid `docker compose build` on release deployments.
   - Validate bundle via `python3 scripts/ci_release_bundle.py verify --bundle <bundle.json>`.
   - Pull container images strictly by immutable digest.
   - Bind migration receipts to target commit and migration manifest hash. An interrupted or failed installation must never advance the `.last-deployed` marker.

## Completion Criteria
- Bundle builder, verifier, and admission commands implemented and tested.
- Negative tests reject forged attacker digests, missing attestations, tampered configurations, and invalid migration receipts.
- Deployment scripts execute pull-by-digest with no host-side compilers or build tools needed.
