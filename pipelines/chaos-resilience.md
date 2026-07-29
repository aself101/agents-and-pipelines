---
name: chaos-resilience
description: Full four-role resilience assessment. Explorer surveys the failure surface and produces the experiment agenda (read-only); Validator executes it with live fault injection; Analyst attributes observed resilience to what actually carries it (ENGINEERED/COMPENSATED/FORTUITOUS); Forecaster projects luck-exhaustion, masking-collapse, and rehearsal-decay trajectories with the rehearsal-refresh watchlist; Synthesis integrates. Requires a running, healthy, non-production target.
tools: Read, Grep, Glob, Bash
model: opus
---

# Chaos Resilience Assessment

> Full four-role resilience assessment. Explorer surveys the failure surface and produces the experiment agenda (read-only); Validator executes it with live fault injection; Analyst attributes observed resilience to what actually carries it (ENGINEERED/COMPENSATED/FORTUITOUS); Forecaster projects luck-exhaustion, masking-collapse, and rehearsal-decay trajectories with the rehearsal-refresh watchlist; Synthesis integrates. Requires a running, healthy, non-production target.

| Field | Value |
|-------|-------|
| Name | `chaos-resilience` |
| Version | 1.0.0 |
| Domain | software |
| Subdomain | resilience |
| Tags | `chaos`, `resilience`, `behavioral`, `fault-injection`, `causal-attribution`, `trajectory`, `sequential` |

## Triggers

- **Manual**
  - Parameters:
    - `target`: string

## Stage Dependency Graph

```
explore (Failure-Surface Survey)
  -> validate (Chaos Experiment Execution)
    -> diagnose (Resilience Diagnosis)
      -> forecast (Resilience Trajectory Projection)
        -> synthesis (Cross-Role Synthesis)
```

## Stages

**Agent launch protocol:** Launch each stage agent via the Agent tool with `subagent_type` set to the agent ref, and begin the prompt with the explicit tag `[agent:<agent-ref>]` (lowercase kebab-case). The agent-metrics hook resolves agent names only from this tag — omitting it captures the agent's metrics nameless.

### Stage 1: Failure-Surface Survey

- **ID:** `explore`

**Agents:**
- `chaos-explorer`

**Gate:**
- Threshold: 0
- On failure: warn

### Stage 2: Chaos Experiment Execution

- **ID:** `validate`
- **Depends on:** `explore`

**Agents:**
- `chaos-validator`

**Gate:**
- Threshold: 75
- On failure: warn

### Stage 3: Resilience Diagnosis

- **ID:** `diagnose`
- **Depends on:** `validate`

**Agents:**
- `chaos-analyst`

**Gate:**
- Threshold: 70
- On failure: warn

### Stage 4: Resilience Trajectory Projection

- **ID:** `forecast`
- **Depends on:** `diagnose`

**Agents:**
- `chaos-forecaster`

**Gate:**
- Threshold: 75
- On failure: warn

### Stage 5: Cross-Role Synthesis

- **ID:** `synthesis`
- **Depends on:** `forecast`

**Agents:**
- `workflow-synthesis`

**Gate:**
- Threshold: 0
- On failure: warn

## Postflight

### Tracker Persistence

After all stages complete, save results to the tracker using `save_run`:

- **definition_type:** `pipeline`
- **definition_name:** `chaos-resilience`
- **definition_version:** `1.0.0`
- **workflow_type:** `resilience-assessment`
- **project:** `$ARGUMENTS` (the target project name)
- **agents:** Collect each agent result with name, score, decision, and summary. Splice the `-f tracker` output verbatim for the metrics fields (tokens, model, harness, duration_ms, agent_id) — do not hand-restructure or copy a subset
- **recommendations:** Collect ALL issues from ALL stages into a single recommendations array with agent, title, priority, severity, failure_code, file_path, line_number, description, and type
- **summary:** `{ all_gates_passed: <true if all abort-gates passed>, average_score: <mean of all agent scores> }`

This MUST be a single bulk call — do NOT create individual issues. The `save_run` tool auto-increments the run number and detects regressions from prior runs.

**Token Metrics:**

Get token metrics from the agent-metrics buffer before saving:
```bash
agent-metrics buffer list --since 60m -f tracker
```

Splice each entry of the `-f tracker` output verbatim into `agents[]` — every field, including `duration_ms` and `agent_id` (the provenance join key to the buffer entry and transcript). Do not hand-restructure: `input_tokens`, `output_tokens`, `cache_creation_tokens`, `cache_read_tokens`, `cached_input_tokens`, `reasoning_output_tokens`, `thinking_tokens`, `tool_tokens`, `total_effective_tokens`, `harness`, `model`, `duration_ms`, and `agent_id` all carry through as-is.

**Field Mappings:**

| Source | Tracker Field | Notes |
|--------|---------------|-------|
| `stage.gate.score` | `agents[].score` | Total score |
| `stage.gate.decision` | `agents[].decision` | Agent decision |
| `agent-metrics` | `agents[].model` | Model identifier |
| `stage.output.issues` | `recommendations[]` | Flatten nested structure |

**Verification:** After saving, compare `json.summary.total_issues` with the saved count. Alert if mismatch.

### On Success

- **ENGINEERED — Full chaos resilience assessment complete: surface mapped, behavior measured, attribution rendered, trajectory projected.**

### On Failure

- **ERODING — Resilience assessment surfaced material findings: review the attribution map, luck register, and trajectory watchlist.**

---
*Generated from PDL v1.4.0 | Pipeline: chaos-resilience v1.0.0*
