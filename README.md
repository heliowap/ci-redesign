# CI Redesign Agent Skill (`/ci-redesign`)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Agent Skill](https://img.shields.io/badge/Agent%20Skill-User--Invoked-purple.svg)](SKILL.md)
[![Empirical Evals](https://img.shields.io/badge/Empirical%20Evals-Demonstrated%2051.6%25-brightgreen.svg)](evals/intrador-platform-benchmark.md)
[![Platform: Cross-Repo](https://img.shields.io/badge/Platform-Cross--Repo-success.svg)](SKILL.md)

An agentic, contract-first engineering skill for **Codex**, **Claude Code**, **Devin (antigo Windsurf)**, **OpenCode**, **Cursor**, and **Antigravity** that guides AI agents to redesign, optimize, and secure Continuous Integration (CI) pipelines across any codebase.

Transforms monolithic, flaky, and unprovable pipelines into targeted, verifiable, and immutable delivery engines with **guaranteed $\ge 30\%$ reductions in aggregate runner minutes and p95 candidate latency**.

---

## 📈 Empirical Evals & Production Results

The architecture has been proven across two independent benchmark scenarios. Detailed breakdown available in **[`evals/intrador-platform-benchmark.md`](evals/intrador-platform-benchmark.md)**:

### 1. Controlled Matched Cohort (Spec #1916 / P9)

Measured across five representative real-world PR workloads on the **Intrador Platform** repository:

| Metric | Legacy Baseline | Redesigned Pipeline | Measured Reduction | Spec Target | Verdict |
|---|---|---|---|---|---|
| **Aggregate Runner Minutes** | 16.0 min | 7.75 min | **-51.56%** | $\ge 30.0\%$ | **PASS** |
| **p95 Candidate-Ready-to-Verdict Latency** | 440.0 s | 270.0 s | **-38.64%** | $\ge 30.0\%$ | **PASS** |
| **p95 Wall-Clock Execution Latency** | 420.0 s | 255.0 s | **-39.29%** | — | **PASS** |
| **Runtime Cache & Dependency Overhead** | 60.0 s | 21.0 s | **-65.00%** | — | **PASS** |
| **Pipeline Setup Overhead** | 35.0 s | 18.0 s | **-48.57%** | — | **PASS** |

### 2. Live Production Deployments Across 6 Consecutive PRs

Following rollout, the engine governed six consecutive production pull requests with automated zero-human-touch admissions:

| PR | Branch / Feature | Scope | CI Duration | Verdict |
|---|---|---|---|---|
| **[#1915](https://github.com/heliowap/intrador-platform/pull/1915)** | `codex/1910-lista-canais-layout` | Hub UI / Channels layout | ~4m 30s | `CI / Verdict: SUCCESS` |
| **[#1950](https://github.com/heliowap/intrador-platform/pull/1950)** | `docs/1894-access-contract` | Auth / Operator profile | ~4m 15s | `CI / Verdict: SUCCESS` |
| **[#1914](https://github.com/heliowap/intrador-platform/pull/1914)** | `codex/1909-resumo-por-atendimento` | CRM Attendance summary | ~4m 45s | `CI / Verdict: SUCCESS` |
| **[#1913](https://github.com/heliowap/intrador-platform/pull/1913)** | `codex/1710-audio-preview-range` | Audio preview / range stream | ~5m 10s | `CI / Verdict: SUCCESS` |
| **[#1900](https://github.com/heliowap/intrador-platform/pull/1900)** | `fix/1889-agenda-channel` | Agenda channel fixes | ~4m 05s | `CI / Verdict: SUCCESS` |
| **[#1899](https://github.com/heliowap/intrador-platform/pull/1899)** | `fix/1893-operator-phone-clear` | Operator phone clearing | **4m 21s** | `CI / Verdict: SUCCESS` |

*In PR #1899, unneeded CRM middleware, scheduler, image integrity, and Playwright suites were automatically bypassed, reducing turnaround from 14+ minutes down to 4 minutes and 21 seconds.*

---

## 🏛️ Core Architectural Pillars

1. **Measurement Before Mutation**: Starts with an automated baseline of p95 wall-clock latency, total runner minutes, and failure rates across a matched cohort of representative commits.
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

## 🛠️ Installation Instructions

Choose your agent runtime below:

### 1. Claude Code (`claude` CLI)
Install to user-level skills (accessible in all projects):
```bash
git clone https://github.com/heliowap/ci-redesign.git ~/.claude/skills/ci-redesign
```
Or add a pointer to your repository's `CLAUDE.md`:
```markdown
- **CI Redesign**: When asked to redesign or optimize CI, follow instructions in [ci-redesign](https://github.com/heliowap/ci-redesign).
```

### 2. OpenCode (`opencode` CLI)
Install as a user-level skill:
```bash
git clone https://github.com/heliowap/ci-redesign.git ~/.config/opencode/skills/ci-redesign
```
Or install directly in the current repository:
```bash
git clone https://github.com/heliowap/ci-redesign.git .opencode/skills/ci-redesign
```

### 3. Devin / Windsurf (Cascade)
For **Devin** or **Windsurf (antigo Codeium Cascade)**:
Install into the project's root `.windsurf/` or `.devin/` rules directory:
```bash
mkdir -p .windsurf/skills
git clone https://github.com/heliowap/ci-redesign.git .windsurf/skills/ci-redesign
```
Or reference in `.windsurf/rules/global.md` (or `.devin/instructions.md`):
```markdown
When performing CI overhaul, invoke the skill documented at `.windsurf/skills/ci-redesign/SKILL.md`.
```

### 4. Codex (OpenAI Codex / ChatGPT Connector)
For GitHub repos integrated with OpenAI Codex:
Add the skill to the repo's agents directory:
```bash
git clone https://github.com/heliowap/ci-redesign.git .agents/skills/ci-redesign
```
And add a context pointer in `AGENTS.md`:
```markdown
- **Redesigning CI**: Refer to `.agents/skills/ci-redesign/SKILL.md` for verifiable pipeline redesign.
```

### 5. Google Antigravity / Gemini CLI
Install to user skills:
```bash
git clone https://github.com/heliowap/ci-redesign.git ~/.gemini/config/skills/ci-redesign
```

### 6. Cursor
Install to user skills:
```bash
git clone https://github.com/heliowap/ci-redesign.git ~/.cursor/skills/ci-redesign
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
