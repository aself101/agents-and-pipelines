---
name: pre-implementation
description: Validates proposed design and architecture BEFORE implementation begins. Runs architect review plus optional docs validation. Blocks implementation if design has critical flaws.
tools: Read, Grep, Glob, Bash
model: opus
---

# Pre-Implementation

> Validates proposed design and architecture BEFORE implementation begins. Runs architect review plus optional docs validation. Blocks implementation if design has critical flaws.

| Field | Value |
|-------|-------|
| Name | `pre-implementation` |
| Version | 1.0.1 |
| Domain | software |
| Subdomain | design |
| Tags | `pre-implementation`, `design`, `architecture`, `validation` |

## Triggers

- **Manual**
  - Parameters:
    - `target`: string

## Stage Dependency Graph

```
preflight (Pre-Flight Detection) [parallel]
  -> review (Architecture & Docs Review) [parallel]
    -> persist (Persist Results)
```

## Stages

### Stage 1: Pre-Flight Detection

- **ID:** `preflight`
- **Execution:** parallel

**Steps:**

1. **Detect design docs** [continue-on-error]
   ```bash
   find . \( -iname "*design*" -o -iname "*spec*" -o -iname "*prd*" -o -iname "*plan*" -o -name "README.md" \) ! -path "*/node_modules/*" ! -path "*/dist/*" ! -path "*/.venv/*" -print -quit 2>/dev/null | grep -q . && echo "DETECTED" || echo "NOT_DETECTED"
   ```

**Gate:**
- On failure: warn

### Stage 2: Architecture & Docs Review

- **ID:** `review`
- **Depends on:** `preflight`
- **Execution:** parallel

**Agents:**
- `pre-implementation-architect`
- `docs-validator` (if `stages.preflight.steps['Detect design docs'].output == 'DETECTED'`)

**Gate:**
- Threshold: 75
- Aggregate: min
- On failure: abort

### Stage 3: Persist Results

- **ID:** `persist`
- **Depends on:** `review`

**Agents:**
- `workflow-synthesis`

**Gate:**
- Threshold: 0
- On failure: warn

## Postflight

### Tracker Persistence

After all stages complete, save results to the tracker using `save_run`:

- **definition_type:** `pipeline`
- **definition_name:** `pre-implementation`
- **definition_version:** `1.0.1`
- **workflow_type:** `pre-implementation`
- **project:** `$ARGUMENTS` (the target project name)
- **validators:** Collect each validator/agent result with name, score, status, model, and token usage
- **recommendations:** Collect ALL issues from ALL stages into a single recommendations array with validator, title, priority, severity, failure_code, file_path, line_number, description, and type
- **summary:** `{ all_gates_passed: <true if all abort-gates passed>, average_score: <mean of all validator scores> }`

This MUST be a single bulk call — do NOT create individual issues. The `save_run` tool auto-increments the run number and detects regressions from prior runs.

**Token Metrics:**

Get token metrics from the agent-metrics buffer before saving:
```bash
agent-metrics buffer list --since 15m -f tracker
```

Map buffer fields to tracker: `input_tokens`, `output_tokens`, `cache_creation_tokens`, `cache_read_tokens`, `total_effective_tokens`, and `model`.

**Field Mappings:**

| Source | Tracker Field | Notes |
|--------|---------------|-------|
| `stage.gate.score` | `agents[].score` | Total score |
| `stage.gate.decision` | `agents[].decision` | Agent decision |
| `agent-metrics` | `agents[].model` | Model identifier |
| `stage.output.issues` | `recommendations[]` | Flatten nested structure |

**Verification:** After saving, compare `json.summary.total_issues` with the saved count. Alert if mismatch.

### On Success

- **PROCEED — Design approved. Ready for implementation.**

### On Failure

- **REVISE — Critical design flaws. Refine before implementing.**

---
*Generated from PDL v1.0.1 | Pipeline: pre-implementation*
