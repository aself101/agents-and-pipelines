---
name: ship
description: Final ship gate. Runs 7 core phases (Validate, Type Safety, Test Architect, Code Auditor, Public Interface, Anxiety Reader, Security) plus conditional API Contract and Release Readiness. Persists all recommendations to tracker.
tools: Read, Grep, Glob, Bash
model: opus
---

# Ship Pipeline

> Final ship gate. Runs 7 core phases (Validate, Type Safety, Test Architect, Code Auditor, Public Interface, Anxiety Reader, Security) plus conditional API Contract and Release Readiness. Persists all recommendations to tracker.

| Field | Value |
|-------|-------|
| Name | `ship` |
| Version | 1.1.1 |
| Domain | software |
| Subdomain | release |
| Tags | `ship`, `validation`, `release`, `ci-cd` |

## Triggers

- **Manual**
  - Parameters:
    - `target`: string

## Stage Dependency Graph

```
preflight (Pre-Flight Detection) [parallel]
  -> validate (Code Validation)
    -> parallel-validation (Parallel Validation) [parallel]
      -> code-auditor (Runtime Correctness Audit)
        -> anxiety-reader (Resilience Anxiety Reading)
          -> security (Security Audit)
            -> final-checks (Final Checks) [parallel]
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
1. **Detect API routes** [continue-on-error]
   ```bash
   grep -rqE --include="*.ts" --include="*.js" --exclude-dir=node_modules 'router\.|app\.get|app\.post|app\.put|app\.delete' . 2>/dev/null && echo "DETECTED" || echo "NOT_DETECTED"
   ```
1. **Detect private package** [continue-on-error]
   ```bash
   node -e "try{const p=require('./package.json');console.log(p.private===true?'DETECTED':'NOT_DETECTED')}catch(e){console.log('NOT_DETECTED')}"
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

### Stage 3: Parallel Validation

- **ID:** `parallel-validation`
- **Depends on:** `validate`
- **Execution:** parallel

**Agents:**
- `type-safety-validator` (if `stages.preflight.steps['Detect TypeScript'].output == 'DETECTED'`)
- `test-architect`
- `public-interface-validator`

**Gate:**
- Threshold: 70
- Aggregate: min
- On failure: abort

### Stage 4: Runtime Correctness Audit

- **ID:** `code-auditor`
- **Depends on:** `parallel-validation`

**Agents:**
- `code-auditor`

**Gate:**
- Threshold: 80
- On failure: abort

### Stage 5: Resilience Anxiety Reading

- **ID:** `anxiety-reader`
- **Depends on:** `code-auditor`

**Agents:**
- `anxiety-reader`

**Gate:**
- Threshold: 70
- On failure: abort

### Stage 6: Security Audit

- **ID:** `security`
- **Depends on:** `anxiety-reader`

**Agents:**
- `security-analyst`

**Gate:**
- Threshold: 85
- On failure: abort

### Stage 7: Final Checks

- **ID:** `final-checks`
- **Depends on:** `security`
- **Execution:** parallel

**Agents:**
- `api-contract-validator` (if `stages.preflight.steps['Detect API routes'].output == 'DETECTED'`)
- `release-readiness` (if `stages.preflight.steps['Detect private package'].output == 'NOT_DETECTED'`)

**Gate:**
- Threshold: 80
- Aggregate: min
- On failure: abort

## Postflight

### Tracker Persistence

After all stages complete, save results to the tracker using `save_run`:

- **definition_type:** `pipeline`
- **definition_name:** `ship`
- **definition_version:** `1.1.1`
- **workflow_type:** `ship`
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

- **SHIP IT — All gates passed. Results persisted.**

### On Failure

- **NOT READY — Blocking issues found. Fix and re-run.**

---
*Generated from PDL v1.1.1 | Pipeline: ship*
