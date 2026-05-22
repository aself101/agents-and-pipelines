---
name: post-implementation
description: Iterative validation pipeline for post-implementation phases. Includes code quality, type safety, MCP protocol, test architecture, optimization, and public interface validation with conditional frontend support.
tools: Read, Grep, Glob, Bash
model: opus
---

# Post-Implementation Validation Pipeline

> Iterative validation pipeline for post-implementation phases. Includes code quality, type safety, MCP protocol, test architecture, optimization, and public interface validation with conditional frontend support.

| Field | Value |
|-------|-------|
| Name | `post-implementation` |
| Version | 1.0.1 |
| Domain | software |
| Subdomain | validation |
| Tags | `validation`, `post-implementation`, `quality`, `ci-cd` |

## Triggers

- **Manual**
  - Parameters:
    - `target`: string
    - `frontend`: boolean (default: false)

## Environment

### Variables

| Name | Value | Scoped To |
|------|-------|-----------|
| `NODE_ENV` | `test` | all stages |

## Stage Dependency Graph

```
preflight (Pre-Flight Detection) [parallel]
  -> validate (Code Validation)
    -> secondary-validation (Secondary Validation) [parallel]
      -> optimize (Code Optimization)
        -> polish (Public Interface & Frontend) [parallel]
```

## Stages

### Stage 1: Pre-Flight Detection

- **ID:** `preflight`
- **Execution:** parallel

**Steps:**

1. **Detect TypeScript** [continue-on-error]
   ```bash
   test -f tsconfig.json && echo "DETECTED" || echo "NOT_DETECTED"
   ```
1. **Detect MCP (Python)** [continue-on-error]
   ```bash
   grep -rqE --include="*.py" --exclude-dir=node_modules --exclude-dir=dist --exclude-dir=__pycache__ --exclude-dir=.venv "from mcp|import mcp|FastMCP|@mcp\." . 2>/dev/null && echo "DETECTED" || echo "NOT_DETECTED"
   ```
1. **Detect MCP (TypeScript)** [continue-on-error]
   ```bash
   grep -rqE --include="*.ts" --include="*.js" --exclude-dir=node_modules --exclude-dir=dist "@modelcontextprotocol/sdk|McpServer" . 2>/dev/null && echo "DETECTED" || echo "NOT_DETECTED"
   ```
1. **Detect MCP (dependency)** [continue-on-error]
   ```bash
   grep -rqE --include=package.json --include=pyproject.toml --include=requirements.txt --exclude-dir=node_modules --exclude-dir=dist "@modelcontextprotocol|\"mcp\":" . 2>/dev/null && echo "DETECTED" || echo "NOT_DETECTED"
   ```
1. **Detect frontend** [continue-on-error]
   ```bash
   test -n "$(find . \( -name "*.tsx" -o -name "*.jsx" \) ! -path "*/node_modules/*" ! -path "*/dist/*" -print -quit 2>/dev/null)" && echo "DETECTED" || echo "NOT_DETECTED"
   ```

**Gate:**
- On failure: warn

### Stage 2: Code Validation

- **ID:** `validate`
- **Depends on:** `preflight`

**Agents:**
- `code-validator`

**Gate:**
- Threshold: 70
- On failure: abort

### Stage 3: Secondary Validation

- **ID:** `secondary-validation`
- **Depends on:** `validate`
- **Execution:** parallel

**Agents:**
- `type-safety-validator` (if `stages.preflight.steps['Detect TypeScript'].output == 'DETECTED'`)
- `mcp-validator` (if `stages.preflight.steps['Detect MCP (Python)'].output == 'DETECTED' || stages.preflight.steps['Detect MCP (TypeScript)'].output == 'DETECTED' || stages.preflight.steps['Detect MCP (dependency)'].output == 'DETECTED'`)
- `test-architect`

**Gate:**
- Threshold: 70
- Aggregate: min
- On failure: warn

### Stage 4: Code Optimization

- **ID:** `optimize`
- **Depends on:** `secondary-validation`

**Agents:**
- `code-optimizer`

**Gate:**
- Threshold: 75
- On failure: warn

### Stage 5: Public Interface & Frontend

- **ID:** `polish`
- **Depends on:** `optimize`
- **Execution:** parallel

**Agents:**
- `public-interface-validator`
- `frontend-validator` (if `params.frontend || stages.preflight.steps['Detect frontend'].output == 'DETECTED'`)

**Gate:**
- Threshold: 75
- Aggregate: min
- On failure: warn

## Postflight

### Tracker Persistence

After all stages complete, save results to the tracker using `save_run`:

- **definition_type:** `pipeline`
- **definition_name:** `post-implementation`
- **definition_version:** `1.0.1`
- **workflow_type:** `post-implementation`
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

- **All gates passed — ready for next implementation phase**

### On Failure

- **Gates failed — fix issues and re-run**

---
*Generated from PDL v1.0.1 | Pipeline: post-implementation*
