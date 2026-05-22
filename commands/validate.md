---
name: validate
description: Validates code quality, standards compliance, and test coverage. Use after each implementation phase.
---

# Code Validator v1
Validates code quality, standards compliance, and test coverage. Use after each implementation phase.

## What's New in v1

| Feature | Description |
|---------|-------------|
| **Calibration Examples** | Reference scenarios for consistent scoring |
| **Failure Code Examples** | Worked examples mapping issues to taxonomy codes |
| **Token Budget** | Output length guidance |
| **Display IDs** | Auto-fail conditions have numbered IDs |

## Arguments

**Usage:** `/agents:validate <directory>`

**Examples:**
- `/agents:validate ./services/auth-service`
- `/agents:validate ./packages/api`
- `/agents:validate .`

**Target Directory:** $ARGUMENTS


---

## Pre-Flight

```bash
echo "Running code validation on $ARGUMENTS..."
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

Run the Code Validator agent on the validated target directory:

**Agent:** code-validator-agent.md
**Model:** Sonnet
**Target:** $ARGUMENTS


---

## Auto-Fail Conditions

Critical issues that trigger immediate FAIL regardless of score:

| ID | Condition |
|----|-----------|
| **AF-001** | Security vulnerabilities detected |
| **AF-002** | Missing error handling in critical paths |
| **AF-003** | Code does not function |
| **AF-004** | Missing tests for core functionality |
| **AF-005** | Breaking changes without migration path |

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

Validation passed with score >= 70

```bash
exit 0
```

### On Failure

Validation failed. Review issues above.

```bash
exit 1
```


---


## PERSIST TO TRACKER (Required)

> **IMPORTANT:** Save to tracker IMMEDIATELY after agent completes, BEFORE presenting the summary to the user. The workflow is not complete until results are persisted.
**1. Save to tracker (REQUIRED):**

```bash
agent-metrics buffer list --since 5m -f tracker
```

mcp__uluops-tracker__save_run

**2. Verify saved:** Compare `json.summary.total_issues` with saved count.

**3. THEN present summary to user.**


### Field Mappings

**Definition identity (REQUIRED for execution tracking):**
| Tracker Field | Value | Notes |
|---------------|-------|-------|
| `definition_type` | `command` | From CDL interface |
| `definition_name` | `validate` | From CDL interface |
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
**CDL Source:** `/Users/aself/uluops/uluops-agent-workflows/udl/cdl/v1/validate.command.yaml`
**Agent:** `agents/code-validator-agent.md`
