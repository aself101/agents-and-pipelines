# The taxonomy

Two axes describe an agent definition: **what kind of reading or writing it performs**,
and **how far its object sits from the artifact**.

## Type — six kinds

Four read. Two write.

| Type | | What it produces |
|---|---|---|
| **Analyst** | reads | Decomposition and assessment. Renders a verdict on present state. |
| **Explorer** | reads | A map and an inquiry agenda. Deliberately withholds verdicts — the explorer surfaces the questions, the analyst answers them. |
| **Forecaster** | reads | Trajectory. Where the present state is heading, under what conditions, with what early-warning signal. |
| **Validator** | reads | Per-claim conformance. Checks what the artifact says about itself against what it does. |
| **Executor** | writes | Applied fixes, scoped to the mechanically unambiguous. |
| **Generator** | writes | New artifacts composed inside a stated frame. |

The reader/writer split matters more than it looks. A reader can be run on anything
without consequence. A writer changes the thing it was pointed at — which is why the
write side gets sparser as the layers get deeper.

## Layer — five depths

Each layer's object recedes one step further from the artifact.

| Layer | Object of attention | Determinacy |
|---|---|---|
| **1 · Structural** | the artifact's structure | Questions with fairly deterministic answers |
| **2 · Behavioral** | the artifact's behavior under time and stress | Answers depend on conditions you can specify |
| **3 · Reasoning** | the reasoning that produced the artifact | Answers depend on intent you have to infer |
| **4 · Epistemic** | the framework the reasoning operates in | Answers depend on the frame you chose to ask from |
| **5 · Meta-analysis** | the agents doing the reading | Answers dissolve when acted on |

Layer 5 is the terminus rather than a rung: the recession runs out of room and curls
back onto the observer. An issue discovered there is about the measurement system
itself, so acting on the finding changes the thing being measured.

## The matrix

Where the two axes have been populated **across the working ecosystem** — not
within this repo, which ships a subset.

| Layer | Analyst | Explorer | Forecaster | Validator | Executor | Generator |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| 1 · Structural | ● | ● | ● | ● | ● | ● |
| 2 · Behavioral | ● | ● | ● | ● | ○ | ○ |
| 3 · Reasoning | ● | ● | ● | ● | ○ | ● |
| 4 · Epistemic | ● | ● | ● | ● | ○ | ● |
| 5 · Meta-analysis | ● | ○ | ○ | ○ | ○ | ○ |

● built  ○ open territory

Two readings worth drawing out. The reader side fills in first and fills in
completely — every layer has all four. The writer side thins as you descend, and
Layer 5 has neither. That is a fact about what has been built, not a claim that a
Layer 5 writer is impossible.

## The third axis

The perspective family — Anxiety Reader, Hostile Reader, Maintainer's Lens, Captive
User, Operator's Eye — does not fit the two axes cleanly. It is filed under Layer 3
because that is where it was found, but it is effectively a third axis: not *what
kind of reading* or *how deep*, but *from whose position*. Which is why it branches
across layers rather than sitting in one.

The strain is informative. Found structure resists the scheme you brought to it.
Designed structure doesn't.

## Layer 5 dimensions

What can be measured about a definition, once the definition is the thing under
measurement.

| Dimension | Question |
|---|---|
| **Completeness** | What did the agent find — and fail to find — given its constraints? |
| **Calibration** | Are the thresholds, criteria, and severity assignments producing a reasonable signal-to-noise ratio? |
| **Stability** | Is the definition's characterization consistent across runs, time, and the models that run it? |
| **Interaction** | Do the findings converge, diverge, interfere, or overlap with other definitions? |
| **Epistemic audit** | Is the reasoning behind the definition sound — and what would falsify it? |

| Agent | Primary | Secondary |
|---|---|---|
| **Coverage Gap Analyzer** | Completeness | Interaction |
| **Evolution Analyst** | Stability | — |
| **Threshold Calibration** | Calibration | — |

No agent claims Interaction or the epistemic audit as its primary dimension, but the
two are not in the same state. Interaction has secondary coverage from the Coverage
Gap Analyzer. The epistemic audit has none at all — it is the emptiest cell in the
layer, and the one asking the question the layer exists to ask.

## Choosing a lens

Not every lens maps well to every artifact — it is possible to flirt with the
unintelligible. What has held up in practice:

| If you want to know | Reach for |
|---|---|
| Whether the language is doing consistent work | Wittgenstein Analyst |
| Whether the system behaves as it claims | Husserl, Machiavelli or Wang Yangming |
| Whether your confidence is earned | Descartes Analyst, Socrates Explorer |
| Where the ground actually is | Sunzi Analyst (terrain), Machiavelli Analyst (incentives) |
| What breaks and when | Seneca Forecaster, then Perverse Outcome Detector or Attack Path Composer |

The convergences between lenses are interesting. The divergences are the measurement.
