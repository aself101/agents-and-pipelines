---
name: evolution-analyst
description: Analyzes historical validation data to identify improvement patterns, regression triggers, and convergence velocity across the agent ecosystem. Uses RAH MCP tools for statistical analysis and uluops-tracker for raw data access.
---

# Evolution Analyst
Analyzes historical validation data across the entire ecosystem to identify patterns that correlate with score improvements. Generates data-driven recommendations for agent definition updates. This is the recursive appreciation closed-loop agent — it transforms accumulated validation history into actionable optimization insights.

## Arguments

**Usage:** `/agents:evolution-analyst [target]`

**Default target:** `agents/v3/` (rendered agent runtime definitions)

**Examples:**
- `/agents:evolution-analyst` — Analyze the full ecosystem (default: agents/v3/)
- `/agents:evolution-analyst agents/v3/code-validator-agent.md` — Focus on one agent
- `/agents:evolution-analyst agents/v3/ --time-range 90d` — Analyze last 90 days

**Target:** ${ARGUMENTS:-agents/v3/}

---

## Pre-Flight

Verify RAH MCP tools are available:

```bash
echo "Checking RAH MCP server connectivity..."
```

Verify tracker MCP tools are available:

```bash
echo "Checking tracker MCP connectivity..."
```

---

## Agent Invocation

Run the Evolution Analyst agent on the target ecosystem:

**Agent:** evolution-analyst-agent.md
**Model:** Opus
**Target:** $ARGUMENTS

### Phase 1: Data Collection
- `mcp__rah__fetch_snapshot` — Load and enrich all tracker data
- `mcp__uluops-tracker__list_projects` — Understand ecosystem scope

### Phase 2: Pattern Analysis
- `mcp__rah__convergence` — Score trajectory analysis
- `mcp__rah__severity_gradient` — FP rate patterns by severity
- `mcp__rah__resolution_diagnostics` — Resolution rate patterns

### Phase 3: Causal Analysis
- `mcp__uluops-tracker__diff_runs` — Change attribution between runs
- `mcp__uluops-tracker__get_run_details` — Specific run inspection
- `mcp__rah__get_agent_metadata` — Agent definition context

### Phase 4: Recommendation Generation
- `mcp__rah__spearman_test` / `mcp__rah__linear_regression` — Ad-hoc correlations
- Agent synthesizes recommendations from collected evidence

---

## Auto-Fail Conditions

Critical issues that trigger immediate FAIL regardless of score:

| ID | Condition |
|----|-----------|
| **AF-001** | No validation history available |
| **AF-002** | Required services unavailable (tracker or RAH MCP) |
| **AF-003** | No version history for agents (all on v1.0.0) |

---

## Decision Thresholds

| Score | Decision | Meaning |
|-------|----------|---------|
| **>=80** | ACTIONABLE | High-confidence recommendations ready for implementation |
| **60-79** | EXPLORATORY | Patterns detected, more data needed for confident recommendations |
| **<60** | INSUFFICIENT | Not enough validation history for reliable analysis |

**Note:** Any critical issue triggers INSUFFICIENT regardless of score.

---

## Post-Flight Actions

### On Success

ACTIONABLE — Recommendations generated with historical evidence backing

```bash
exit 0
```

### On Failure

INSUFFICIENT — Build more validation history before evolution analysis

```bash
exit 1
```

---

## PERSIST TO TRACKER (Required)

> **IMPORTANT:** Save to tracker IMMEDIATELY after agent completes, BEFORE presenting the summary to the user.

**1. Save to tracker:**

mcp__uluops-tracker__save_run

**2. Verify saved:** Compare recommendation count with saved count.

**3. THEN present summary to user.**

### Field Mappings

**From JSON OUTPUT to Tracker:**
| Source | Tracker Field | Notes |
|--------|---------------|-------|
| `json.result.score` | `validators[].score` | Total score |
| `json.result.decision` | `validators[].status` | ACTIONABLE/EXPLORATORY/INSUFFICIENT |
| `json.categories[].findings[].issues[]` | `recommendations[]` | Flatten nested structure |

---

## Source

**Agent:** `agents/v3/evolution-analyst-agent.md`
**ADL Schema:** `udl/adl/v3/evolution-analyst.agent.yaml`
