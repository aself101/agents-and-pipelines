---
name: alien-frame-analyst
description: Generates a coherent alternative frame for the problem an artifact solves — one that shares none of the artifact's assumptions. Not a competing solution but a competing understanding of the problem. Decision FRAME_AWARE/FRAME_LOCKED.
---

# Alien Frame Analyst v1
Generates a coherent alternative frame for the problem an artifact solves — one that shares none of the artifact's assumptions. Not a competing solution but a competing understanding of the problem. Decision FRAME_AWARE/FRAME_LOCKED.

## What's New in v1

| Feature | Description |
|---------|-------------|
| **Calibration Examples** | Reference scenarios for consistent scoring |
| **Failure Code Examples** | Worked examples mapping issues to taxonomy codes |
| **Token Budget** | Output length guidance |
| **Display IDs** | Auto-fail conditions have numbered IDs |

## Arguments

**Usage:** `/agents:alien-frame-analyst <directory>`

**Examples:**
- `/agents:alien-frame-analyst udl/adl/v3/code-validator.agent.yaml`
- `/agents:alien-frame-analyst docs/architecture.md`
- `/agents:alien-frame-analyst specs/api-spec.md`

**Target Directory:** $ARGUMENTS


---

## Pre-Flight

```bash
echo "Generating alien frame for $ARGUMENTS..."
echo "========================================"
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
[ -e "$ARGUMENTS" ] && echo "✓ $ARGUMENTS exists" || echo "Target file or directory does not exist"
```


---

## Agent Invocation

Run the Alien Frame Analyst agent on the validated target directory:

**Agent:** alien-frame-analyst-agent.md
**Model:** Opus
**Target:** $ARGUMENTS


---

## Auto-Fail Conditions

Critical issues that trigger immediate FAIL regardless of score:

| ID | Condition |
|----|-----------|
| **AF-001** | Inversion disguised as alien frame |
| **AF-002** | Alternative solution instead of alternative frame |
| **AF-003** | Alien frame not verified for coherence |
| **AF-004** | Generic philosophical alternative instead of specific alien frame |

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

FRAME_AWARE — artifact recognizes its framing as a choice

```bash
exit 0
```

### On Failure

FRAME_LOCKED — artifact treats its frame as reality

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
| `definition_name` | `alien-frame-analyst` | From CDL interface |
| `definition_version` | `1.0.2` | From CDL interface |

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
| `buffer.tokens.total_effective_tokens` | `total_effective_tokens` | Effective total |
| `json.categories[].findings[].issues[]` | `recommendations[]` | Flatten nested structure |
| `json.analysis.records[]` | `analysis_records[]` | Structured analysis records (v1.4.0) |
| `json.analysis.system_metrics` | `analysis_summary.system_metrics` | Agent-type-specific metrics |
| `json.analysis.category_scores[]` | `analysis_summary.category_scores[]` | Category score breakdown |
| `json.analysis.epistemic_assessment` | `analysis_summary.epistemic_assessment` | Failure signature risk ratings |
| `json.analysis.audit_implications[]` | `analysis_summary.audit_implications[]` | Trajectory projections |

**Note:** `json` = agent's JSON OUTPUT, `buffer` = `agent-metrics buffer list -f tracker`
**Note:** `analysis_records` and `analysis_summary` are optional (v1.4.0). Omit if agent output has no `analysis` section.

---

## Source

**CDL Schema:** `udl/definition-languages/cdl-schema-v1_3_0.json`
**CDL Source:** `/Users/aself/uluops/uluops-agent-workflows/udl/cdl/v1/alien-frame-analyst.command.yaml`
**Agent:** `agents/alien-frame-analyst-agent.md`

---
*Generated from CDL v2.0.0 | Command: alien-frame-analyst v1.0.2*
