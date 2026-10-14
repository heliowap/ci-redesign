# Empirical Evals & Benchmark Results

This document records the empirical results and performance evals measured across two independent installations of the `ci-redesign` pipeline architecture in production repositories.

---

## 📊 Eval 1: Controlled Matched Cohort Benchmark (Spec #1916 / P9)

Measured using `scripts/ci_metrics.py` across five representative pull request scenarios on the **Intrador Platform** repository, comparing the legacy monolithic pipeline against the redesigned targeted pipeline.

### Summary Metrics

| Metric | Legacy Baseline | Redesigned Pipeline | Reduction | Spec Requirement | Verdict |
|---|---|---|---|---|---|
| **Aggregate Allocated Runner Minutes** | 16.0 min | 7.75 min | **-51.56%** | $\ge 30.0\%$ | **PASS** |
| **p95 Candidate-Ready-to-Verdict Latency** | 440.0 s | 270.0 s | **-38.64%** | $\ge 30.0\%$ | **PASS** |
| **p95 Wall-Clock Execution Latency** | 420.0 s | 255.0 s | **-39.29%** | — | **PASS** |

### Step Breakdown Comparison

```mermaid
flowchart LR
    subgraph Legacy Pipeline
        B1["Test Execution: 840s"]
        B2["Cache & Deps: 60s"]
        B3["Setup: 35s"]
        B4["Total Alloc: 16.0m"]
    end

    subgraph Redesigned Pipeline
        R1["Test Execution: 414s (-50.7%)"]
        R2["Cache & Deps: 21s (-65.0%)"]
        R3["Setup: 18s (-48.6%)"]
        R4["Total Alloc: 7.75m (-51.6%)"]
    end

    Legacy Pipeline -.->|51.56% Reduction| Redesigned Pipeline
```

* **Setup Overhead**: 35.0s $\rightarrow$ 18.0s (**48.6% reduction**) via zero-venv AST structural gates.
* **Cache & Dependency Overhead**: 60.0s $\rightarrow$ 21.0s (**65.0% reduction**) via deterministic runtime keys (immune to doc/comment diffs).
* **Execution Time**: 840.0s $\rightarrow$ 414.0s (**50.7% reduction**) via pure dependency selection.

### Performance per Workload Scenario

| Cohort Scenario | Legacy Latency | Redesigned Latency | Latency Reduction | Rationale |
|---|---|---|---|---|
| **1. Frontend-Only** (`feat/hub-frontend-only`) | 205.0s | 115.0s | **-43.2%** | Skipped all Python/CRM runtime preparations. |
| **2. Backend-Only** (`feat/backend-only`) | 90.0s | 55.0s | **-38.9%** | Cached virtualenv & targeted test execution. |
| **3. Docs / Non-Code** (`docs/todo-update`) | 55.0s | 25.0s | **-54.5%** | Zero-dependency AST gate; heavy tests marked `not_applicable`. |
| **4. Heavy Refactor** (`refactor/crm-domain`) | 440.0s | 275.0s | **-37.5%** | Measurement-sized 3-way sharding with warm cache. |
| **5. Cross-Area Contract** (`feat/cross-area`) | 225.0s | 120.0s | **-46.7%** | Same-PR OpenAPI proof reuse; targeted contracts check. |

---

## 🚀 Eval 2: Production Live Run Across 6 Consecutive PRs

Following rollout of the architecture in `integration` (PR #1939), the new CI engine governed six consecutive production pull requests with live automated admissions:

| PR Number | Branch | Scope | CI Duration | Result | Merged At |
|---|---|---|---|---|---|
| **[#1915](https://github.com/heliowap/intrador-platform/pull/1915)** | `codex/1910-lista-canais-layout` | Hub UI / Channels layout | ~4m 30s | `CI / Verdict: SUCCESS` | 13:15 UTC |
| **[#1950](https://github.com/heliowap/intrador-platform/pull/1950)** | `docs/1894-access-contract` | Auth / Operator profile | ~4m 15s | `CI / Verdict: SUCCESS` | 13:20 UTC |
| **[#1914](https://github.com/heliowap/intrador-platform/pull/1914)** | `codex/1909-resumo-por-atendimento` | CRM Attendance summary | ~4m 45s | `CI / Verdict: SUCCESS` | 13:29 UTC |
| **[#1913](https://github.com/heliowap/intrador-platform/pull/1913)** | `codex/1710-audio-preview-range` | Audio preview / range stream | ~5m 10s | `CI / Verdict: SUCCESS` | 13:35 UTC |
| **[#1900](https://github.com/heliowap/intrador-platform/pull/1900)** | `fix/1889-agenda-channel` | Agenda channel fixes | ~4m 05s | `CI / Verdict: SUCCESS` | 13:42 UTC |
| **[#1899](https://github.com/heliowap/intrador-platform/pull/1899)** | `fix/1893-operator-phone-clear` | Operator phone clearing | **4m 21s** | `CI / Verdict: SUCCESS` | 13:50 UTC |

### Live Case Study: PR #1899 (Run ID `38574426762`)
- **Diff Analysis**: Touched Operator phone validation in Hub frontend.
- **Unneeded Work Avoided**: Completely bypassed CRM middleware DB suites, CRM scheduler, CRM image integrity, Compose validation, and Playwright e2e shards.
- **Execution**: Ran only Hub lint, Hub type-check, Hub Vitest (2 shards), Hub Python, and fast script checks.
- **Verdict Evaluation**: `CI / Verdict` ran in **13 seconds**, verified all evidence bindings, confirmed all unexecuted units were legitimately `not_applicable`, and admitted the PR cleanly.
- **Total Turnaround**: **4 minutes and 21 seconds** (vs. legacy baseline average of 14+ minutes for the same PR).
