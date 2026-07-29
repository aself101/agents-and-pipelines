# Agents & Pipelines

Agent, command and pipeline definitions for Claude Code and other harnesses.

These back two essays:

- [**Automated Doubt**](https://alexself.dev/blog/automated-doubt) — front-loading scrutiny through multi-agent validation.
- [**Through the Agentic Looking Glass**](https://alexself.dev/blog/through-the-agentic-looking-glass) — a six-type taxonomy and the five layers it turned out to sit on.

**57 agents · 54 commands · 4 pipelines.**

---

## Install

Definitions are plain markdown. Copy them into your Claude Code config:

```bash
cp agents/*.md    ~/.claude/agents/
cp commands/*.md  ~/.claude/commands/agents/
cp pipelines/*.md ~/.claude/commands/pipelines/
```

Both directories are intentionally **flat**. In Claude Code a command's directory
is its namespace — `commands/agents/foo.md` resolves to `/agents:foo`. Grouping
the files into subfolders would rename every command, so the layer taxonomy below
lives in the documentation rather than in the filesystem.

## Invoke

```
/agents:assumption-excavator     <path>    # a single agent
/agents:nietzsche-analyst        <path>
/pipelines:pre-implementation    <path>    # a multi-agent pipeline
```

---

## The taxonomy

Two axes. **Type** — what kind of reading or writing the agent performs. **Layer** —
how far its object sits from the artifact itself.

| Type | Reads or writes | |
|---|---|---|
| **Analyst** | reads | decomposes and assesses |
| **Explorer** | reads | maps territory, produces agendas, no verdicts |
| **Forecaster** | reads | projects trajectory |
| **Validator** | reads | checks claims against behavior |
| **Executor** | writes | applies mechanical fixes |
| **Generator** | writes | composes new artifacts |

| Layer | Object of attention |
|---|---|
| **1 · Structural** | the artifact's structure |
| **2 · Behavioral** | the artifact's behavior under time and stress |
| **3 · Reasoning** | the reasoning that produced the artifact |
| **4 · Epistemic** | the framework the reasoning operates in |
| **5 · Meta-analysis** | the agents doing the reading |

Each layer's object recedes one step further from the artifact, and each is a little
less deterministic than the one before it. See [`docs/taxonomy.md`](docs/taxonomy.md)
for the full map, including which type × layer combinations are still open territory.

---

## Layer 1 — the artifact's structure

Agents that operate on the artifact directly, grounded by questions with fairly
deterministic answers: is this SQL safe, does this code validate, does the README
describe what the software actually does.

| Agent | Command | What it does |
|---|---|---|
| **API Contract Validator** | `/agents:api-contract` | Validates API contract consistency between documentation, types, and implementation. |
| **Chain Tracer** | `/agents:chain-tracer` | Traces execution and communication chains end-to-end through codebases. |
| **Code Auditor** | `/agents:audit` | Deep inspection for runtime correctness issues that pass compilation, linting, and tests but could fail in production. |
| **Code Optimizer** | `/agents:optimize` | Reviews code after validation passes. |
| **Code Validator** | `/agents:code-validate` | Validates code quality after implementation phases. |
| **DX Validator** | `/agents:dx-validate` | Developer advocate gate that tests real user experience. |
| **Data Privacy Officer** | `/agents:data-privacy-officer-analyst` | Assesses privacy compliance posture of data processing practices against applicable data protection regulations. |
| **Deep Explore** | `/agents:deep-explore` | Deep codebase exploration using multi-strategy search and relationship tracing. |
| **Docs Validator** | `/agents:docs-validate` | Validates documentation completeness and quality across all documentation surfaces. |
| **Gap Analyst** | `/agents:gap-analyst` | Identifies what a well-formed artifact of this type should contain but doesn't. |
| **Pre-Implementation Architect** | `/agents:architect` | Reviews proposed designs BEFORE implementation begins. |
| **Public Interface Validator** | `/agents:public-interface` | Validates public-facing code quality including documentation completeness, feature coverage, unused code cleanup, and consumer experience. |
| **Release Readiness** | `/agents:release` | Final gate before publishing a package or CLI tool. |
| **Security Analyst** | `/agents:security` | Comprehensive security auditor with risk assessment and numerical scoring. |
| **Software Architecture Expert** | `/agents:software-architecture-expert-analyst` | Performs software architecture evaluation on any artifact describing or embodying a software system. |
| **Test Architect** | `/agents:test-review` | Validates test quality after code passes the validator. |
| **Type Safety Validator** | `/agents:type-safety` | Validates TypeScript type safety beyond compilation. |

## Layer 2 — the artifact's behavior under time and stress

The artifact in motion. What happens under stress, over time, across state
transitions and fault injection.

| Agent | Command | What it does |
|---|---|---|
| **Chaos Analyst** | `/agents:chaos-analyst` | Diagnoses why a system survives failure. |
| **Chaos Explorer** | `/agents:chaos-explorer` | Surveys the failure surface of a system and produces a prioritized chaos-experiment agenda. |
| **Chaos Forecaster** | `/agents:chaos-forecaster` | Projects a system's resilience trajectory. |
| **Chaos Validator** | `/agents:chaos-validate` | Validates system resilience through live fault injection and failure simulation. |
| **Performance Validator** | `/agents:performance-validate` | Validates system performance, scalability, and resource efficiency under realistic load conditions. |
| **Runtime Validator** | `/agents:runtime-validate` | Validates actual runtime behavior of HTTP APIs, SDKs, and services through execution testing. |
| **State Validator** | `/agents:state-validate` | Validates stateful workflows and multi-step interactions work correctly across request sequences. |

## Layer 3 — the reasoning that produced the artifact

Agents categorized by cognitive function rather than mechanical role. They operate
on the thinking behind the artifact rather than the artifact itself.

| Agent | Command | What it does |
|---|---|---|
| **Alien Frame Analyst** | `/agents:alien-frame-analyst` | Generates a coherent alternative frame for the problem an artifact solves — one that shares none of the artifact's assumptions. |
| **Ambiguity Mapper** | `/agents:ambiguity-mapper` | Finds terms and phrases used with multiple meanings within the same artifact — not vagueness (imprecision), but genuine polysemy where the same… |
| **Assumption Excavator** | `/agents:assumption-excavator` | Surfaces implicit assumptions buried in any artifact — agent definitions, prompts, business plans, technical specs, workflows, or documents. |
| **Implied Completeness Detector** | `/agents:implied-completeness-detector` | Reads the shape of what an artifact contains and infers what is structurally absent. |
| **Incentive Mapper** | `/agents:incentive-mapper` | Identifies what behaviors the artifact creates — intended or not. |
| **Negative Space Analyst** | `/agents:negative-space-analyst` | Reads meaning from what an artifact deliberately does not include. |
| **Perverse Outcome Detector** | `/agents:perverse-outcome-detector` | Identifies failure modes that emerge when people or systems optimize against an artifact's measurable criteria — metric gaming, threshold… |
| **Unintended Consequences** | `/agents:unintended-consequences` | Traces causal chains an artifact will unleash that weren't part of the design intent. |

### The perspective family

A third axis, filed under Layer 3 because that is where it was found. Not *what kind
of reading* or *how deep*, but *from whose position* — which is why it branches
across layers instead of sitting in one.

| Agent | Command | What it does |
|---|---|---|
| **Anxiety Reader** | `/agents:anxiety-reader` | Reads the artifact from the position of someone afraid of its failure modes — someone whose career depends on it not failing. |
| **Captive User** | `/agents:captive-user` | Models the perspective of someone who must use the artifact and has no exit option. |
| **Hostile Reader** | `/agents:hostile-reader` | Simulates someone whose interests are structurally opposed to the artifact succeeding. |
| **Maintainer's Lens** | `/agents:maintainers-lens` | Models the perspective of a competent engineer inheriting the artifact five years downstream, with no original-author context. |
| **Operator's Eye** | `/agents:operators-eye` | Models the perspective of the person who runs the artifact in production — SREs, platform engineers, on-call rotation members. |

## Layer 4 — the framework the reasoning operates in

Formal epistemic frameworks distilled from a body of thought, then disciplined into
an agent definition. Each carries its core axioms, its blind spots, and an explicit
account of what it is *not* doing.

| Agent | Command | What it does |
|---|---|---|
| **Aristotle Analyst** | `/agents:aristotle-analyst` | Performs Aristotelian four-cause decomposition on any artifact — code, specs, plans, architectures, or documents. |
| **Attack Path Composer** | `/agents:attack-path-composer` | Consume the finding sets of upstream security agents and compose them into end-to-end attack chains, assessing composed severity, required… |
| **Confucius Forecaster** | `/agents:confucius-forecaster` | Performs Confucian relational coherence trajectory projection on any artifact. |
| **Descartes Analyst** | `/agents:descartes-analyst` | Performs foundational architecture analysis on any artifact. |
| **Hegel Analyst** | `/agents:hegel-analyst` | Performs Hegelian dialectical synthesis analysis on any artifact. |
| **Heraclitus Analyst** | `/agents:heraclitus-analyst` | Performs Heraclitean unity-of-opposites analysis on any artifact. |
| **Husserl Analyst** | `/agents:husserl-analyst` | Performs phenomenological reduction of a system's designed experience-model, followed by a fulfillment audit of the experience the system… |
| **Machiavelli Analyst** | `/agents:machiavelli-analyst` | Performs Machiavellian effectual truth analysis on any artifact. |
| **Nietzsche Analyst** | `/agents:nietzsche-analyst` | Performs Nietzschean genealogical analysis on any artifact. |
| **Seneca Forecaster** | `/agents:seneca-forecaster` | Performs Senecan failure trajectory projection on any artifact. |
| **Socrates Analyst** | `/agents:socrates-analyst` | Performs Socratic examination audit on any artifact. |
| **Socrates Explorer** | `/agents:socrates-explorer` | Performs Socratic elenctic examination on any artifact. |
| **Sunzi Analyst** | `/agents:sunzi-analyst` | Performs Sunzian terrain-force-tempo analysis on any artifact. |
| **Wang Yangming Analyst** | `/agents:wang-yangming-analyst` | Performs comprehensive knowledge-action unity analysis on any artifact. |
| **William James Analyst** | `/agents:william-james-analyst` | Performs Jamesian cash-value analysis on any artifact. |
| **Wittgenstein Analyst** | `/agents:wittgenstein-analyst` | Performs Wittgensteinian language-game analysis on any artifact. |

## Layer 5 — the agents doing the reading

The lenses aimed at the lens makers. These consume historical run data or survey the
ecosystem from above. Findings at this layer are reflexive: act on one and you have
changed the thing being measured.

| Agent | Command | What it does |
|---|---|---|
| **Coverage Gap Analyzer** | — | Identifies what kinds of bugs the ecosystem is not catching. |
| **Evolution Analyst** | `/agents:evolution-analyst` | Analyzes historical validation data across the entire ecosystem to identify patterns that correlate with score improvements. |
| **Threshold Calibration** | — | Analyzes whether agent thresholds produce the right signal-to-noise ratio. |
| **Workflow Synthesis** | — | Synthesizes cross-cutting insights from multiple upstream agent outputs in any workflow. |

---

## Pipelines

| Pipeline | Agents | Phase |
|---|---|---|
| **pre-implementation** | Pre-Implementation Architect, Docs Validator, Assumption Excavator | Design |
| **post-implementation** | Code Validator, Type Safety Validator, Test Architect, Code Optimizer, Public Interface Validator, Security Analyst | Development |
| **ship** | Code Validator, Type Safety Validator, Test Architect, Code Auditor, Public Interface Validator, Security Analyst, Anxiety Reader, API Contract Validator, Release Readiness | Ship |
| **chaos-resilience** | Chaos Explorer, Chaos Validator, Chaos Analyst, Chaos Forecaster, Workflow Synthesis | Behavioral |

---

## Notes

- **Three agents ship without a command** — Coverage Gap Analyzer, Threshold
  Calibration and Workflow Synthesis are invoked from within pipelines rather than
  directly. Copy the agent file and call it via the Task tool, or write a command.
- **This repo is a subset.** The essays draw on a larger working set; what ships here
  is what the two posts actually discuss. Some definitions name downstream agents that
  aren't included — those handoffs are real, the agents just aren't part of this cut.
- **Domain experts ship as analysts.** Software Architecture Expert and Data Privacy
  Officer each have explorer/validator/forecaster siblings in the working set. Only the
  analyst ships here, matching how the essay names them.
- Definitions are versioned in their own frontmatter. See [`CHANGELOG.md`](CHANGELOG.md)
  for what arrived when.
