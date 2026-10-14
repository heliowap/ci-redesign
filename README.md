# CI Redesign Agent Skill (`/ci-redesign`)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Agent Skill](https://img.shields.io/badge/Agent%20Skill-User--Invoked-purple.svg)](SKILL.md)
[![Platform: Cross-Repo](https://img.shields.io/badge/Platform-Cross--Repo-success.svg)](SKILL.md)

An agentic, contract-first engineering skill for Claude Code, Antigravity, OpenCode, and Cursor that guides AI agents to redesign, optimize, and secure Continuous Integration (CI) pipelines across any codebase.

Transforms monolithic, flaky, and unprovable pipelines into targeted, verifiable, and immutable delivery engines with **guaranteed $\ge 30\%$ reductions in aggregate runner minutes and p95 candidate latency**.

---

## 🏛️ Core Architectural Pillars

1. **Measurement Before Mutation**: Never refactor CI on gut feeling. Starts with an automated baseline of p95 wall-clock latency, total runner minutes, and failure rates across a matched cohort of representative commits.
2. **Authority & Governance Feasibility**: Probes repository security boundaries, rulesets, and Actions App permissions before designing gate checks.
3. **Verification Units Over Jobs**: Check obligations are modeled as logical responsibilities with declared inputs and recipes—never tied to fragile YAML job names.
4. **Pure Complete Verdict Engine**: Non-applicability comes strictly from an evaluated verification plan, never from skipped or ignored workflow steps.
5. **Cryptographic Proof Reuse**: Unchanged modules reuse verified evidence from previous runs within a PR or across PRs, with immediate invalidation upon dependency or code mutation.
6. **Immutable Release Bundles & Pull-by-Digest**: Production deployment strictly pulls pre-verified container digests authorized by detached HMAC/cosign attestations and image subject allowlists; host-side compilers and builds on production are strictly forbidden.

---

## 🔄 The 10 Phases of CI Redesign

The redesign executes as a dependency-ordered task graph:

| Phase | Title | Focus Area | Deliverable |
|---|---|---|---|
| **[P0](phases/P0-baseline-metrics.md)** | **Baseline Metrics** | Measure p95 latency, aggregate runner minutes, and matched cohort. | `ci-baseline.json` & evaluation harness |
| **[P1](phases/P1-authority-probe.md)** | **Authority Probe** | Inspect rulesets, required checks, and publisher token permissions. | `ci-authority-feasibility.md` |
| **[P2](phases/P2-selection-lifecycle.md)** | **Targeted Selection** | Pure diff-to-unit dependency mapping & candidate-scoped concurrency. | `ci_policy.py`, `ci_plan.py` |
| **[P3](phases/P3-reproducible-runtime-cache.md)** | **Reproducible Cache** | Zero-venv AST structural gates & reproducible runtime caching. | Isolated environments & deduplicated invariant checks |
| **[P4](phases/P4-pure-verdict-engine.md)** | **Pure Verdict Engine** | Complete plan evaluation over bound evidence (`passed \| reused \| n/a \| blocked`). | `ci_verdict.py` (Shadow observation) |
| **[P5](phases/P5-enforced-admission-gate.md)** | **Enforced Gate** | Enforced candidate gate requiring complete obligations & fresh base. | Enforced `CI / Verdict` ruleset gate |
| **[P6](phases/P6-proof-reuse-retry-sizing.md)** | **Proof Reuse & Retry** | Same/cross-PR proof key reuse, single infra failure retry, runner sizing. | `ci_proof_key.py`, `ci_retry.py`, `ci_sizing.py` |
| **[P7](phases/P7-trigger-ownership.md)** | **Trigger Ownership** | Risk tier inventory, bounded schedules, and single-responder AI bots. | `workflow-triggers.md` & clean trigger scope |
| **[P8](phases/P8-immutable-release-bundle.md)** | **Immutable Release Bundle** | Multi-platform digests, HMAC attestation, image allowlist, pull-by-digest. | `ci_release_bundle.py` & deploy cutover |
| **[P9](phases/P9-efficiency-demonstration.md)** | **Efficiency Proof** | Demonstrated $\ge 30\%$ reduction on matched cohort, legacy retirement. | `ci-efficiency-report.md` & rollback runbook |

---

## 📐 Formal Contracts & Schemas

- **[Verdict Schema](contracts/verdict-schema.md)**: Formal JSON schema defining `CompleteVerdict`, candidate identity (`head_sha`, `base_sha`), stage, and machine-readable `UnitVerdict` states.
- **[Release Bundle Schema](contracts/release-bundle-schema.md)**: Formal JSON schema defining multi-platform container digests, detached publisher HMAC attestation, config identities, and synthetic migration manifests.

---

## 🛠️ Installation

### Global User Skill (Recommended)

Clone directly into your AI assistant's skills directory:

#### Google Antigravity / Gemini CLI:
```bash
git clone https://github.com/heliowap/ci-redesign.git ~/.gemini/config/skills/ci-redesign
```

#### Cursor:
```bash
git clone https://github.com/heliowap/ci-redesign.git ~/.cursor/skills/ci-redesign
```

#### Repository-Local Skill:
```bash
git clone https://github.com/heliowap/ci-redesign.git .agents/skills/ci-redesign
```

---

## 🚀 How to Run

1. Open an agent session in your target repository.
2. Type:
   ```text
   /ci-redesign
   ```
3. The skill guides the agent through:
   - **Phase 0 & 1**: Automatic baseline extraction and ruleset inspection.
   - **`/grill-with-docs`**: Architectural interview to align on test suites, registry namespaces, and target deployment architecture.
   - **`/to-spec` & `/to-tickets`**: Translating requirements into tracer-bullet tickets.
   - **`/implement-spec`**: TDD implementation via parallel subagents in isolated git worktrees.
   - **Phase 9**: Measuring outcome against baseline, proving $\ge 30\%$ latency and runner reduction, and validating against live PR execution.

---

## 📄 License

MIT © [Helio WAP](https://github.com/heliowap)
