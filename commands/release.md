---
name: release
description: Final release gate for packages and CLI tools. Validates version consistency, CLI --version, package.json, and docs. Detects semantic-release CI/CD vs manual publishing.
---

# Release Readiness v1
Final release gate for packages and CLI tools. Validates version consistency, CLI --version, package.json, and docs. Detects semantic-release CI/CD vs manual publishing.

## What's New in v1

| Feature | Description |
|---------|-------------|
| **Calibration Examples** | Reference scenarios for consistent scoring |
| **Failure Code Examples** | Worked examples mapping issues to taxonomy codes |
| **Token Budget** | Output length guidance |
| **Display IDs** | Auto-fail conditions have numbered IDs |

## Arguments

**Usage:** `/agents:release <directory>`

**Examples:**
- `/agents:release ./packages/sdk`
- `/agents:release .`
- `/agents:release ./lib`

**Target Directory:** $ARGUMENTS


---

## Pre-Flight

```bash
echo "Running release readiness check on $ARGUMENTS..."
echo "================================================"
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

Run the Release Readiness agent on the validated target directory:

**Agent:** release-readiness-agent.md
**Model:** Sonnet
**Target:** $ARGUMENTS


---

## Auto-Fail Conditions

Critical issues that trigger immediate FAIL regardless of score:

| ID | Condition |
|----|-----------|
| **** | CLI --version does not match package.json version |
| **** | Missing CHANGELOG entry for current version |
| **** | Secrets or API keys in codebase |
| **** | README.md is missing |
| **** | Build artifacts stale or missing |
| **** | console.log in production paths (for libraries) |

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

Release readiness check passed with score >= 80

```bash
exit 0
```

### On Failure

Release readiness check failed. Review issues above.

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
| `definition_name` | `release` | From CDL interface |
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
**CDL Source:** `/Users/aself/uluops/uluops-agent-workflows/udl/cdl/v1/release.command.yaml`
**Agent:** `agents/release-readiness-agent.md`
