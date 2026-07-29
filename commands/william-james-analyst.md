---
name: william-james-analyst
description: Performs Jamesian cash-value analysis on any artifact. Tests distinctions for concrete lived difference, applies the Lived-Action Test, distributes verdicts across populations, and issues LIVING/DEAD verdicts with temporal scope. Decision LIVING/DEAD.
---

# William James Analyst
Performs Jamesian cash-value analysis on any artifact. Tests distinctions for concrete lived difference, applies the Lived-Action Test, distributes verdicts across populations, and issues LIVING/DEAD verdicts with temporal scope. Decision LIVING/DEAD.

## Arguments

**Usage:** `/agents:william-james-analyst <directory>`

**Examples:**
- `/agents:william-james-analyst udl/adl/v3/code-validator.agent.yaml`
- `/agents:william-james-analyst docs/architecture.md`
- `/agents:william-james-analyst specs/api-spec.md`
- `/agents:william-james-analyst src/`

**Target Directory:** $ARGUMENTS


---

## Pre-Flight

```bash
echo "Running Jamesian cash-value analysis on $ARGUMENTS..."
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
[ -e "$ARGUMENTS" ] && echo "✓ $ARGUMENTS exists" || echo "Target file or directory does not exist"
```


---

## Agent Invocation

Run the William James Analyst agent on the validated target directory:

**Agent:** william-james-analyst-agent.md
**Model:** Opus
**Target:** $ARGUMENTS


---

## Auto-Fail Conditions

Critical issues that trigger immediate FAIL regardless of score:

| ID | Condition |
|----|-----------|
| **AF-001** | Verdicts issued without specifying the affected population |
| **AF-002** | Cash-value tested as operational consequence rather than lived difference |
| **AF-003** | No distinction inventory — analysis skips to verdicts |
| **AF-004** | No Lived-Action Test — verdicts based on expectation alone |

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

Artifact LIVING — significant distinctions produce lived difference for affected populations

```bash
exit 0
```

### On Failure

Artifact DEAD — significant distinctions persist as inert abstractions without lived consequence

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
| `definition_name` | `william-james-analyst` | From CDL interface |
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
**CDL Source:** `/Users/aself/uluops/uluops-agent-workflows/udl/cdl/v1/william-james-analyst.command.yaml`
**Agent:** `agents/william-james-analyst-agent.md`
