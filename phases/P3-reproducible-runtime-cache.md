# Phase 3: Reproducible Runtime & Invariant Deduplication

## Objective
Accelerate execution without sacrificing detection by separating structural checks from runtime checks, caching only applicable runtimes, and deduplicating invariant repository scans.

## Steps

1. **Isolate Structural AST Checks**:
   - Extract repository structural invariants (e.g. tree contracts, import boundaries, doc pointer checks) into zero-dependency or lightweight tools using the Python standard library AST.
   - Run structural gates before any heavy dependency installation or environment preparation.

2. **Subset Runtime Provisioning**:
   - Ensure jobs only install runtimes and dependencies required by the selected units (e.g. frontend PR does not install Python DB drivers; backend PR does not download Playwright browsers).
   - Use dynamic runner scripts (e.g. `scripts/prepare_import_targets.py --roots ...`) driven by the selection plan.

3. **Reproducible Runtime Cache Identity**:
   - Calculate cache keys strictly from resolved lockfiles, interpreter version, OS/architecture, and build recipe:
     ```yaml
     key: runtime-${{ runner.os }}-${{ runner.arch }}-py312-${{ hashFiles('packages/**/requirements*.txt') }}
     ```
   - Unrelated commits (e.g. documentation edits, comments, doc-map changes) must NOT bust package caches.

4. **Deduplicate Invariant Scans**:
   - Live repository scans (e.g. pointer checkers, glossary citations, contract linters) run once in a dedicated step.
   - Bulk test runners (`pytest scripts/tests/ -n auto` or `vitest`) must ignore those live scan files to prevent quadratic or redundant scans.

## Completion Criteria
- Structural gates run and fail closed without full virtual environments.
- Package cache keys are immune to documentation changes.
- Live repository invariant checks run exactly once per workflow execution.
- Cold, warm, and missing caches yield identical test pass/fail results.
