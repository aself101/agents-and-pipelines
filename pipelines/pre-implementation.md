---
name: pre-implementation
description: Validates proposed design and architecture BEFORE implementation begins. Runs architect review plus optional docs validation. Blocks implementation if design has critical flaws.
tools: Read, Grep, Glob, Bash
model: opus
---

# Pre-Implementation

> Validates proposed design and architecture BEFORE implementation begins. Runs architect review plus optional docs validation. Blocks implementation if design has critical flaws.

Duration: 3-10 minutes
**Arguments**: `target` (required)
## Pre-Flight Detection

- **design_docs_detected**: `test -n "$(find . \( -name '*design*' -o -name '*spec*' -o -name '*prd*' -o -name '*plan*' -o -name 'README.md' \) ! -path '*/node_modules/*' ! -path '*/dist/*' ! -path '*/.venv/*' -print -quit 2>/dev/null)"`
## Execution

Ask user: **Sequential** (stop on first failure) or **Parallel** (run groups concurrently)?
```
Group 1 (parallel): architect + docs-validator
Group 2 (sequential): assumption-review
Group 3 (always): persist
```
## Phases

| # | Agent | Threshold | Gate | Condition |
|---|-------|-----------|------|-----------|
| 1 |  | threshold >= 75, on fail: stop | stop | — |
| 2 |  | threshold >= 75, on fail: warn | stop | context.has_design_docs |
| 3 |  | threshold >= 70, on fail: warn | stop | — |
| 4 | workflow-synthesis@latest | threshold >= 0, on fail: warn | stop | — |

**Architecture Review**: Architectural fit with existing codebase; Design quality and complexity; Scope bounds (LOC, files, dependencies); Completeness of error handling and data flow
**Documentation Validation**: Design doc completeness; API contract clarity; Data flow documentation
**Epistemic Assumption Review** (after architect): Implicit assumptions in proposed design; Overclaiming of capabilities or guarantees; Reasoning quality behind architectural decisions
**Persist Results**: Synthesize findings and persist to tracker
## Scoring

**Method**: weighted_average
 — architect: 55%, assumption-review: 20%, docs-validator: 25%
## Results Submission

Write markdown report to: `{{ target_path }}/{{ report_file }}`
Save ALL findings to tracker via `mcp_uluops-tracker_save_run` with project=`{{ target_name }}`, workflow_type=`pre-implementation`, definition_type=`workflow`, definition_name=`pre-implementation`, definition_version=`2.0.1`. Include validators array (name, score, status, model) and recommendations array (validator, title, priority, severity, description, file_path, line_number). Each file:line reference becomes a separate recommendation. Priority: blocking=critical, warnings=suggested, post-ship=backlog.
After saving, query tracker and compare counts. Mismatches from cross-phase deduplication are expected — warn only, do not re-attempt.
