# Phase 7: Trigger Ownership & Controlled Automation

## Objective
Audit and inventory all workflow triggers, eliminate unmanaged automation loops, and ensure scheduled jobs have explicit ownership and failure notifications.

## Steps

1. **Trigger Risk Inventory**:
   Inspect all `.github/workflows/*.yml` files. Every workflow must declare:
   - **Trigger type**: `pull_request`, `push`, `schedule`, `workflow_dispatch`, or `issue_comment`.
   - **Risk tier**: Read-only validation vs. credentialed publisher vs. release deployment.
   - **Owner**: Person or team responsible for maintenance and failure triage.
   - **Actionable outcome**: What happens when the job fails (blocks PR, creates issue, sends alert).

2. **Bound Scheduled Workflows**:
   - Limit `schedule` triggers to external dependencies (e.g., security vulnerability database sync, third-party API compatibility probes).
   - Require an explicit failure alerting step (e.g., Slack/webhook or GitHub Issue creation) so failing cron workflows do not fail silently.

3. **Single-Responder Review Bots**:
   - Ensure AI review bots (e.g. Codex, OpenCode, Claude) do NOT trigger automatically on every draft push or comment edit.
   - Restrict bot invocation to explicit command strings (e.g., `/oc review`, `/review`).
   - Guard against recursive execution: bot comments or automated status updates must never trigger new workflow runs.

4. **Retire Zombie & Redundant Workflows**:
   - Delete obsolete workflows replaced by the unified CI pipeline.
   - Ensure no workflow authorizes production deployment or database migration solely from a generic push event.

## Completion Criteria
- Trigger inventory documented in `docs/workflow-triggers.md`.
- No infinite bot loops or unowned cron workflows remain.
- All scheduled workflows have active failure notification steps.
