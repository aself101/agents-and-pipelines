---
name: chaos-forecaster
description: Project a system's resilience trajectory. Consumes upstream chaos-family artifacts (or discovers read-only) and projects luck-exhaustion, masking-collapse, rehearsal-decay, and fragility-accumulation trajectories — anchored to present-state evidence, condition-triggered (never dated), each with an early-warning signal and cheapest-now lever. Strictly read-only.
---

# Chaos Forecaster
Project a system's resilience trajectory. Consumes upstream chaos-family artifacts (or discovers read-only) and projects luck-exhaustion, masking-collapse, rehearsal-decay, and fragility-accumulation trajectories — anchored to present-state evidence, condition-triggered (never dated), each with an early-warning signal and cheapest-now lever. Strictly read-only.

## Arguments

**Usage:** `/agents:chaos-forecaster <directory>`

**Examples:**
- `/agents:chaos-forecaster .`
- `/agents:chaos-forecaster ./src`
- `/agents:chaos-forecaster ./services`

**Target Directory:** $ARGUMENTS


---

## Pre-Flight

```bash
echo "Running chaos trajectory projection on $ARGUMENTS..."
echo "===================================================="
```

Verify the target directory exists:

```bash
test -d "$ARGUMENTS" && echo "✓ Directory exists: $ARGUMENTS" || echo "ERROR: Directory '$ARGUMENTS' not found"
```

Enter and confirm location:

```bash
cd "$ARGUMENTS" && pwd
```

Check path exists:

```bash
[ -e "$ARGUMENTS" ] && echo "✓ $ARGUMENTS exists" || echo "Target directory does not exist"
```


---

## Agent Invocation

Run the Chaos Forecaster agent on the validated target directory:

**Agent:** chaos-forecaster-agent.md
**Model:** Opus
**Target:** $ARGUMENTS
**Prompt tag:** Begin the agent prompt with `[agent:chaos-forecaster]` — the agent-metrics hook resolves agent names only from this tag; omitting it captures the agent's metrics nameless.


---

## Auto-Fail Conditions

Critical issues that trigger immediate FAIL regardless of score:

| ID | Condition |
|----|-----------|
| **AF-001** | Forecaster injected a fault or mutated state |
| **AF-002** | Projection issued without a present-state anchor |
| **AF-003** | Calendar-dated prediction without stated mechanism |

---

## Decision Thresholds

| Score | Decision | Meaning |
|-------|----------|---------|
| **>=70** | ✅ PASS | Validation passed, proceed to next phase |
| **<70** | ❌ FAIL | Validation failed, fix issues before proceeding |

**Note:** Any critical issue triggers FAIL regardless of score.

---

## Post-Flight Actions

### On Success

Chaos trajectory projection complete. Review projections and watchlist above.

```bash
exit 0
```

### On Failure

Chaos trajectory projection encountered issues.

```bash
exit 1
```


---


## PERSIST TO TRACKER (Required)

> **IMPORTANT:** Save to tracker IMMEDIATELY after agent completes, BEFORE presenting the summary to the user. The workflow is not complete until results are persisted.
**1. Get token metrics from buffer:**
```bash
agent-metrics buffer list --since 5m -f tracker
```

**2. Save to tracker (DO THIS FIRST):**

mcp__uluops-tracker__save_run

**3. Verify saved:** Compare `json.summary.total_issues` with saved count.

**4. THEN present summary to user.**

### Field Mappings

**Definition identity (REQUIRED for execution tracking):**
| Tracker Field | Value | Notes |
|---------------|-------|-------|
| `definition_type` | `command` | From CDL interface |
| `definition_name` | `chaos-forecaster` | From CDL interface |
| `definition_version` | `1.0.0` | From CDL interface |

**From JSON OUTPUT to Tracker:**
| Source | Tracker Field | Notes |
|--------|---------------|-------|
| `json.result.score` | `agents[].score` | Total score |
| `json.result.decision` | `agents[].decision` | PASS/FAIL |
| `buffer.model` | `validators[].model` | From agent-metrics buffer |
| `buffer.tokens.input_tokens` | `input_tokens` | Raw input tokens |
| `buffer.tokens.output_tokens` | `output_tokens` | Output tokens |
| `buffer.tokens.cache_creation_tokens` | `cache_creation_tokens` | Cache creation |
| `buffer.tokens.cache_read_tokens` | `cache_read_tokens` | Cache reads |
| `buffer.tokens.cached_input_tokens` | `cached_input_tokens` | Cached input (subtracted in total_effective) |
| `buffer.tokens.reasoning_output_tokens` | `reasoning_output_tokens` | Reasoning — subset of gross output, not added |
| `buffer.tokens.thinking_tokens` | `thinking_tokens` | Thinking — subset of gross output, not added |
| `buffer.tokens.tool_tokens` | `tool_tokens` | Tool — subset of gross output, not added |
| `buffer.tokens.total_effective_tokens` | `total_effective_tokens` | Effective total |
| `buffer.harness` | `harness` | Producing CLI/runtime (claude-code, codex, …) |
| `buffer.duration_ms` | `duration_ms` | Execution duration |
| `buffer.agent_id` | `agent_id` | Provenance join key to buffer entry + transcript |
| `json.categories[].findings[].issues[]` | `recommendations[]` | Flatten nested structure |
| `json.analysis.records[]` | `analysis_records[]` | Structured analysis records (v1.4.0) |
| `json.analysis.system_metrics` | `analysis_summary.system_metrics` | Agent-type-specific metrics |
| `json.analysis.category_scores[]` | `analysis_summary.category_scores[]` | Category score breakdown |
| `json.analysis.epistemic_assessment` | `analysis_summary.epistemic_assessment` | Failure signature risk ratings |
| `json.analysis.audit_implications[]` | `analysis_summary.audit_implications[]` | Trajectory projections |

**Note:** `json` = agent's JSON OUTPUT, `buffer` = `agent-metrics buffer list -f tracker`. Splice each buffer entry verbatim into `agents[]` — every field carries through as-is, including `duration_ms` and `agent_id`.
**Note:** `analysis_records` and `analysis_summary` are optional (v1.4.0). Omit if agent output has no `analysis` section.

---

## Source

**CDL Schema:** `udl/definition-languages/cdl-schema-v1_3_0.json`
**CDL Source:** `/Users/aself/uluops/uluops-agent-workflows/udl/cdl/v1/chaos-forecaster.command.yaml`
**Agent:** `agents/chaos-forecaster-agent.md`

---
*Generated from CDL v2.0.0 | Command: chaos-forecaster v1.0.0*
