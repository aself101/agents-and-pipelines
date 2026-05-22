# Examples

The agents, commands and pipelines referenced in the blog post. These are the definitions used in the Automated Doubt development process.

## Structure

```
examples/
  agents/     # Agent definitions (the system prompts that define each agent's lens)
  commands/   # Command definitions (slash commands that invoke agents in Claude Code)
  pipelines/  # Pipeline definitions (multi-agent workflows that run agents in sequence)
```

## How to invoke

### Individual agents

Agents are invoked as slash commands in Claude Code:

```
/agents:assumption-excavator
/agents:code-validate
/agents:anxiety-reader
```

### Pipelines

Pipelines chain multiple agents together. Invoke the same way:

```
/pipelines:pre-implementation
/pipelines:post-implementation
/pipelines:ship
```

## Agents

| Agent | Description |
|-------|-------------|
| **Assumption Excavator** | Surfaces implicit assumptions buried in any artifact. Produces a ranked assumption inventory with fragility scores. |
| **Pre-Implementation Architect** | Reviews proposed designs before implementation begins. Validates architectural fit, design quality, scope appropriateness. |
| **Docs Validator** | Validates documentation completeness and quality across all documentation surfaces. |
| **Gap Analyst** | Identifies what a well-formed artifact of its type should contain but doesn't. Structural gaps and linkage gaps. |
| **Implied Completeness Detector** | Reads the shape of what an artifact contains and infers what is structurally absent. |
| **Ambiguity Mapper** | Finds terms and phrases used with multiple meanings within the same artifact. |
| **Code Validator** | Validates code quality after implementation. Checks structure, standards compliance, test coverage, and best practices. |
| **Type Safety Validator** | Validates TypeScript type safety beyond compilation. Catches `any` abuse, unsafe assertions, implicit type holes. |
| **Test Architect** | Validates test quality after code passes the validator. Ensures tests verify behavior not implementation. |
| **Code Optimizer** | Reviews code after validation passes. Proposes safe refactors for performance, structure, and maintainability. |
| **Public Interface Validator** | Validates public-facing code quality including documentation completeness, feature coverage, and consumer experience. |
| **Security Analyst** | Comprehensive security auditor with risk assessment. Covers OWASP Top 10, CWE Top 25, and platform-specific vulnerabilities. |
| **Code Auditor** | Deep inspection for runtime correctness issues that pass compilation, linting, and tests but could fail in production. |
| **Anxiety Reader** | Reads from the position of someone afraid of the artifact's failure modes. Surfaces concerns hiding beneath confident language. |
| **API Contract Validator** | Validates API contract consistency between documentation, types, and implementation. |
| **Release Readiness** | Final gate before publishing. Validates package.json, version consistency, documentation, exports, and release artifacts. |
| **Chain Tracer** | Traces execution and communication chains end-to-end through codebases. Follows every hop, transformation, and handoff. |
| **Deep Explore** | Deep codebase exploration using multi-strategy search and relationship tracing. |

## Commands

Commands are the invocation layer — each wraps an agent with usage instructions and context for Claude Code. One command per agent listed above.

## Pipelines

| Pipeline | Agents | Phase |
|----------|--------|-------|
| **pre-implementation** | Pre-Implementation Architect, Docs Validator, Assumption Excavator | Design |
| **post-implementation** | Code Validator, Type Safety Validator, Test Architect, Code Optimizer, Public Interface Validator, Security Analyst | Development |
| **ship** | Code Validator, Type Safety Validator, Test Architect, Code Auditor, Public Interface Validator, Security Analyst, Anxiety Reader, API Contract Validator, Release Readiness | Ship |
