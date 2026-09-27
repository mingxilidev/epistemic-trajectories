# Epistemic Trajectories

**When should an AI assistant investigate further, revise its judgment, or support a decision?**

This research note proposes evaluating how assistants handle changing evidence across a conversation. It focuses on two possible failures: committing before the evidence warrants it, and delaying after the evidence is sufficient for the decision at hand.

The central question is whether these behaviors reveal anything beyond what well-designed response-level evaluations already measure.

**Status:** Research hypothesis and evaluation sketch, September 2026. No experiments have been run for this repository. The examples are fictional, authored illustrations, not recorded model outputs or a validated benchmark.

## Read the note

[**Beyond Single-Turn Calibration: Evaluating Epistemic Trajectories in AI Assistants**](essay.md)

The essay develops three distinctions:

- How credible an explanation is.
- Whether investigating it further is worthwhile.
- Whether a particular action is justified now.

It uses technical troubleshooting to make these distinctions concrete. Short troubleshooting conversations are a test setting; they do not establish effects on months-long personal assistance or human reasoning.

## Repository contents

| File | Purpose |
| --- | --- |
| [essay.md](essay.md) | Main argument, worked example, limitations, and related work |
| [eval_sketch.md](eval_sketch.md) | Proposed pilot, comparison conditions, annotation rubric, and failure criteria |
| [examples/premature_commitment.md](examples/premature_commitment.md) | Misleading early evidence and correction of a mistaken premise |
| [examples/excessive_deferral.md](examples/excessive_deferral.md) | Evidence supports a scoped decision, but investigation continues unnecessarily |
| [examples/evidence_reversal.md](examples/evidence_reversal.md) | A previously reasonable diagnosis becomes inadequate after new evidence |
| [examples/unresolved.md](examples/unresolved.md) | Recovery permits action without resolving the cause |
| [references.md](references.md) | Selected adjacent research and the limits of this note's positioning |

## What would make this useful?

A useful result would show that reviewers can reliably identify these behaviors, and that trajectory measures add predictive value beyond strong response-level measures at comparable cost.

A useful negative result would show that existing measures already explain the behavior, that labels cannot be made reliable, or that a simple prompt performs as well as a more elaborate system.

## Feedback welcome

The most helpful feedback would be:

- Existing evaluations that already capture this exact decision problem.
- Examples where the proposed annotation rules give the wrong answer.
- Ways to distinguish unnecessary delay from justified caution without hindsight.
- Simpler comparison conditions that could explain an apparent improvement.

Please use synthetic examples or material you have permission to publish. These files contain no customer records, internal logs, or production identifiers.

## Drafting disclosure

This note was developed with AI assistance from an initial proposal and research memo. Its example responses and proposed labels were authored for illustration. They are not empirical evidence about any model or organization.
