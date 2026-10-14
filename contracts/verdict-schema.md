# Contract: Complete CI Verdict Schema

This contract defines the data structures exchanged between the selection engine, workflow jobs, and the verdict evaluation engine.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "CompleteVerdict",
  "type": "object",
  "required": [
    "schema_version",
    "verdict_id",
    "candidate",
    "stage",
    "overall_status",
    "units",
    "evaluated_at"
  ],
  "properties": {
    "schema_version": { "type": "string", "const": "1.0.0" },
    "verdict_id": { "type": "string" },
    "candidate": {
      "type": "object",
      "required": ["head_sha", "base_sha", "base_ref"],
      "properties": {
        "head_sha": { "type": "string", "pattern": "^[0-9a-f]{40}$" },
        "base_sha": { "type": "string", "pattern": "^[0-9a-f]{40}$" },
        "base_ref": { "type": "string" },
        "pr_number": { "type": ["integer", "null"] },
        "is_draft": { "type": "boolean" }
      }
    },
    "stage": { "type": "string", "enum": ["fast", "premerge"] },
    "overall_status": {
      "type": "string",
      "enum": ["passed", "blocked"]
    },
    "units": {
      "type": "object",
      "additionalProperties": {
        "type": "object",
        "required": ["status", "reason"],
        "properties": {
          "status": {
            "type": "string",
            "enum": ["passed", "reused", "not_applicable", "blocked"]
          },
          "reason": { "type": "string" },
          "evidence": { "type": ["object", "null"] }
        }
      }
    },
    "summary_markdown": { "type": "string" },
    "evaluated_at": { "type": "string", "format": "date-time" }
  }
}
```

## Status Semantics
- `passed`: The unit was selected in the plan and genuine, authentic execution evidence proved success.
- `reused`: The unit's inputs, dependencies, and recipes were proven identical to a previously verified origin.
- `not_applicable`: The unit was explicitly omitted by the authoritative planning engine because candidate diff did not touch its affected paths.
- `blocked`: Execution failed, was cancelled, was missing required matrix shards, or was stale.
