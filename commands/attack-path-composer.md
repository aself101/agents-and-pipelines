---
name: attack-path-composer
description: Compose the findings of upstream security agents into end-to-end attack chains. Validates each transition's exploitability in sequence, scores composed severity, annotates detection and containment gaps per stage, and identifies the single highest-leverage remediation. Decision - ISOLATED/CHAINABLE/CRITICAL_PATH.
---

# Attack Path Composer
Compose the findings of upstream security agents into end-to-end attack chains. Validates each transition's exploitability in sequence, scores composed severity, annotates detection and containment gaps per stage, and identifies the single highest-leverage remediation. Decision - ISOLATED/CHAINABLE/CRITICAL_PATH.

## Arguments

**Usage:** `/agents:attack-path-composer <directory>`

**Examples:**
- `/agents:attack-path-composer ./reports`
- `/agents:attack-path-composer ./security-findings.json`
- `/agents:attack-path-composer .`

**Target Directory:** $ARGUMENTS


---

## Pre-Flight

```bash
echo "Composing attack paths from findings in $ARGUMENTS..."
echo "====================================================="
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
[ -e "$ARGUMENTS" ] && echo "✓ $ARGUMENTS exists" || echo "Findings path does not exist"
```


---

## Agent Invocation

Run the Attack Path Composer agent on the validated target directory:

**Agent:** attack-path-composer-agent.md
**Model:** Opus
**Target:** $ARGUMENTS


---

## Auto-Fail Conditions

Critical issues that trigger immediate FAIL regardless of score:

| ID | Condition |
|----|-----------|
| **AF-001** | Critical attack path confirmed |
| **AF-002** | Critical path through a detection blind spot |
| **AF-003** | Critical path terminating in an unbounded blast radius |

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

Attack-path composition complete. Review the critical-path register and highest-leverage remediation above.

```bash
exit 0
```

### On Failure

Attack-path composition encountered issues.

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
| `definition_name` | `attack-path-composer` | From CDL interface |
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
**CDL Source:** `/Users/aself/uluops/uluops-agent-workflows/udl/cdl/v1/attack-path-composer.command.yaml`
**Agent:** `agents/attack-path-composer-agent.md`

---
*Generated from CDL v2.0.0 | Command: attack-path-composer v1.0.0*
