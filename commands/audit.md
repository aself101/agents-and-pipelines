---
name: audit
description: Deep runtime correctness audit. Catches async bugs, null dereferences, silent failures, and edge cases that pass all other validators. Use as FINAL gate before ship.
---

# Code Auditor v1
Deep runtime correctness audit. Catches async bugs, null dereferences, silent failures, and edge cases that pass all other validators. Use as FINAL gate before ship.

## What's New in v1

| Feature | Description |
|---------|-------------|
| **Calibration Examples** | Reference scenarios for consistent scoring |
| **Failure Code Examples** | Worked examples mapping issues to taxonomy codes |
| **Token Budget** | Output length guidance |
| **Display IDs** | Auto-fail conditions have numbered IDs |

## Arguments

**Usage:** `/agents:audit <directory>`

**Examples:**
- `/agents:audit ./src`
- `/agents:audit ./packages/core`
- `/agents:audit .`

**Target Directory:** $ARGUMENTS


---

## Pre-Flight

```bash
echo "Running deep code audit on $ARGUMENTS..."
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
[ -e "$ARGUMENTS" ] && echo "✓ $ARGUMENTS exists" || echo "Target directory does not exist"
```


---

## Agent Invocation

Run the Code Auditor agent on the validated target directory:

**Agent:** code-auditor-agent.md
**Model:** Opus
**Target:** $ARGUMENTS


---

## Auto-Fail Conditions

Critical issues that trigger immediate FAIL regardless of score:

| ID | Condition |
|----|-----------|
| **AF-001** | Unhandled promise rejection in production path |
| **AF-002** | Empty catch block in error-critical code |
| **AF-003** | .find() result used without null check |
| **AF-004** | JSON.parse on external data without try/catch |
| **AF-005** | Fire-and-forget async that could lose user data |
| **AF-006** | Silent failure that corrupts state |

---

## Decision Thresholds

| Score | Decision | Meaning |
|-------|----------|---------|
| **>=80** | ✅ PASS | Validation passed, proceed to next phase |
| **<80** | ❌ FAIL | Validation failed, fix issues before proceeding |

**Note:** Any critical issue triggers FAIL regardless of score.

---

## Post-Flight Actions

### On Success

Code audit passed with score >= 80

```bash
exit 0
```

### On Failure

Code audit found issues. Review above before shipping.

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
| `definition_name` | `audit` | From CDL interface |
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
**CDL Source:** `/Users/aself/uluops/uluops-agent-workflows/udl/cdl/v1/audit.command.yaml`
**Agent:** `agents/code-auditor-agent.md`
