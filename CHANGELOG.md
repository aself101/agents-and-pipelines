# Changelog

Definitions are versioned individually in their own frontmatter. This file records
what arrived in the repository and why.

---

## 2026-07-28 — Through the Agentic Looking Glass

Companion release for [the second essay](https://alexself.dev/blog/through-the-agentic-looking-glass).
Adds every agent the post discusses, and reorganizes the documentation around the
five-layer taxonomy the post describes.

**Added — 38 agents, 37 commands, 1 pipeline.**

*Layer 1 · Structural (3)*
`dx-validator` · `software-architecture-expert-analyst` · `data-privacy-officer-analyst`

Domain-expert families ship as analysts only. Software Architecture Expert and Data
Privacy Officer each have explorer/validator/forecaster siblings in the working set;
the essay names the role, and the analyst is the role's primary reading.

*Layer 2 · Behavioral (7)*
`runtime-validator` · `state-validator` · `performance-validator` · `chaos-validator` ·
`chaos-explorer` · `chaos-analyst` · `chaos-forecaster`

*Layer 3 · Reasoning (5)*
`perverse-outcome-detector` · `unintended-consequences` · `incentive-mapper` ·
`negative-space-analyst` · `alien-frame-analyst`

*Layer 3 · Perspective family (4)*
`hostile-reader` · `maintainers-lens` · `captive-user` · `operators-eye`

*Layer 4 · Epistemic (16)*
`heraclitus-analyst` · `nietzsche-analyst` · `confucius-forecaster` ·
`william-james-analyst` · `wittgenstein-analyst` · `husserl-analyst` ·
`machiavelli-analyst` · `wang-yangming-analyst` · `descartes-analyst` ·
`socrates-analyst` · `socrates-explorer` · `aristotle-analyst` · `hegel-analyst` ·
`sunzi-analyst` · `seneca-forecaster` · `attack-path-composer`

*Layer 5 · Meta-analysis (3)*
`coverage-gap-analyzer` · `evolution-analyst` · `threshold-calibration`

*Pipelines (1)*
`chaos-resilience` — the full four-role Layer 2 sequence. Explorer surveys the failure
surface and produces the experiment agenda, Validator executes it with live fault
injection, Analyst attributes the observed resilience to whatever actually carries it,
Forecaster projects the decay trajectories, Workflow Synthesis integrates. Requires a
running, healthy, non-production target.

**Updated.** `code-validator`, `test-architect`, `assumption-excavator` and
`anxiety-reader` refreshed to their current definitions.

**Documentation.**
- README reorganized by layer, with a per-agent command mapping.
- The 19 agents from the first release were assigned layers for the first time here.
  Most were uncontested; two sat on the L1/L3 boundary. **Gap Analyst stays at L1** —
  it compares an artifact against a structural template for its type and requires no
  domain expertise, so its findings are checkable against the artifact itself.
  **Ambiguity Mapper moved to L3** — polysemy across sections is a fact about whether
  the authors held one consistent meaning, which is a property of the reasoning rather
  than of the text. Implied Completeness Detector and Negative Space Analyst sit at L3
  for the same reason; Wittgenstein Analyst sits at L4 because it requires adopting a
  frame the author never used.
- Added `docs/taxonomy.md` — the type × layer map, the reader/writer split, the
  perspective family's third axis, and the Layer 5 dimensions.
- Added this changelog.

**Removed.** `commands/validate.md` — a duplicate binding to `code-validator-agent`
alongside `commands/code-validate.md`. The README documented `/agents:code-validate`
from the first release, so the two had been inconsistent since they shipped.

**Known gaps.**
- `coverage-gap-analyzer`, `threshold-calibration` and `workflow-synthesis` ship
  without commands. They are invoked from inside pipelines rather than directly.

---

## 2026-05-22 — Automated Doubt

Initial release, backing [the first essay](https://alexself.dev/blog/automated-doubt).

**Added — 19 agents, 18 commands, 3 pipelines.**

*Agents*
`pre-implementation-architect` · `docs-validator` · `assumption-excavator` ·
`gap-analyst` · `implied-completeness-detector` · `ambiguity-mapper` ·
`code-validator` · `type-safety-validator` · `test-architect` · `code-optimizer` ·
`public-interface-validator` · `security-analyst` · `code-auditor` ·
`anxiety-reader` · `api-contract-validator` · `release-readiness` ·
`chain-tracer` · `deep-explore` · `workflow-synthesis`

*Pipelines*
`pre-implementation` · `post-implementation` · `ship`
