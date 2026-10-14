---
name: ci-redesign
description: "Redesign CI pipelines for targeted checks, verifiable evidence, and immutable releases based on measured baseline."
disable-model-invocation: true
---

# CI Redesign: Targeted Checks, Verifiable Evidence, and Immutable Releases

Execute a complete, reproducible CI overhaul across any codebase. Replaces monolithic, slow, and unprovable pipelines with a fast selection engine, reproducible runtime caching, pure complete verdicts, same/cross-PR proof reuse, and pull-by-digest release bundles.

## Core Philosophy

1. **Measurement before mutation**: Never refactor CI without a durable baseline dataset measuring wall-clock p95 latency, aggregate runner minutes, and failure modes across a matched commit cohort.
2. **Authority and feasibility first**: Probe repository settings, rulesets, and token permissions before designing gate checks.
3. **Units over jobs**: A verification unit is an obligation with declared inputs, dependencies, and recipes—not a job name in YAML.
4. **Pure verdicts over skipped jobs**: Non-applicability comes from an evaluated verification plan, never from skipped or ignored workflow steps.
5. **Authentic release bundles**: Release deployment consumes pre-verified platform digests with detached publisher attestations; never build on the release target host.

## Workflow Phases

The redesign runs in 10 sequential phases. Each phase has its own reference document, deliverables, and completion criteria:

1. [P0: Baseline Metrics & Dataset](phases/P0-baseline-metrics.md) — Collect recent workflow runs, compute p95 latency and aggregate runner minutes, and establish the matched comparison cohort.
2. [P1: Authority Probe & Feasibility](phases/P1-authority-probe.md) — Inspect repository permissions, branch protection, rulesets, and Actions App ID boundaries.
3. [P2: Targeted Selection & Scoped Lifecycle](phases/P2-selection-lifecycle.md) — Implement pure dependency/area selection, draft vs. ready PR execution modes, and candidate-scoped concurrency cancellation.
4. [P3: Reproducible Runtime & Invariant Deduplication](phases/P3-reproducible-runtime-cache.md) — Isolate structural AST checks from runtime checks, cache only applicable runtime environments, and eliminate duplicate repository-wide scans.
5. [P4: Pure Complete Verdict Engine](phases/P4-pure-verdict-engine.md) — Build the verifier evaluating complete plans and bound execution evidence (`passed | reused | not_applicable | blocked`).
6. [P5: Enforced Admission Gate](phases/P5-enforced-admission-gate.md) — Enforce the candidate-bound verdict as the single required merge gate, verifying head and base freshness.
7. [P6: Proof Reuse, Bounded Retry & Runner Sizing](phases/P6-proof-reuse-retry-sizing.md) — Enable cryptographic proof reuse (same-PR and cross-PR), single-attempt infrastructure failure retry, and measurement-backed runner sizing.
8. [P7: Trigger Ownership & Controlled Automation](phases/P7-trigger-ownership.md) — Establish trigger risk inventory, bound scheduled workflows, and enforce single-responder review bots.
9. [P8: Immutable Release Bundles & Pull-by-Digest](phases/P8-immutable-release-bundle.md) — Assemble release bundles with multi-platform image digests, publisher HMAC/cosign attestations, image registry allowlists, and synthetic migration receipts.
10. [P9: Efficiency Proof & Legacy Retirement](phases/P9-efficiency-demonstration.md) — Prove ≥30% reduction in runner minutes and p95 latency on the matched cohort, retire legacy workflows, and publish the rollback runbook.

## Step-by-Step Execution

### Step 1: Baseline and Alignment
1. Run [P0: Baseline Metrics](phases/P0-baseline-metrics.md) on the target repository to compute baseline p95 latency, runner minutes, and current failure rates.
2. Run [P1: Authority Probe](phases/P1-authority-probe.md) to inspect rulesets and token permissions.
3. Call the Skill tool with `/grill-with-docs` to align with the repository owner on:
   - Target branches (`integration`, `dev`, `main` or repo equivalents).
   - Core verification boundaries (monorepo areas, shared contracts, migrations).
   - Container registries and target deployment architectures (`linux/amd64`, `linux/arm64`).

### Step 2: Specification and Ticket Graph
1. Call the Skill tool with `/to-spec` providing the grilled decisions and baseline metrics. The spec must include:
   - Concrete verification seams (candidate admission, release bundle admission, measurement).
   - Negative test requirements (anti-forgery, stale base rejection, false non-applicability).
   - Agreed performance target (e.g., ≥30% reduction in p95 and aggregate runner minutes).
2. Call the Skill tool with `/to-tickets` to decompose the spec into a dependency-ordered task graph corresponding to P0–P9.

### Step 3: Implementation via Subagents
1. Call the Skill tool with `/implement-spec` targeting the generated issue tickets.
2. Implementers work in isolated git worktrees using TDD (red-green-refactor).
3. Mergers integrate each slice into the integration branch `ci/<spec-number>-ci-redesign`.

### Step 4: Verification and Code Review
1. Once all slices are integrated, call the Skill tool with `/code-review`.
2. Specifically review:
   - Security boundaries: publisher attestation, image subject allowlists, candidate code isolation.
   - Negative tests: forged evidence, spoofed runner app IDs, altered config/migration receipts.
   - Real PR validation: verify that the newly enforced verdict actually executes and passes on an active pull request.

### Step 5: Acceptance and Final Report
1. Run [P9: Efficiency Demonstration](phases/P9-efficiency-demonstration.md) to produce `docs/testing/ci-efficiency-report.md`.
2. Retire obsolete legacy workflows.
3. Present the efficiency report and merge the integration PR.
