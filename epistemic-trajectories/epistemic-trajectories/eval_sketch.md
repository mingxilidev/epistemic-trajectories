# Evaluation Sketch

**Status:** Proposed protocol. No runs, annotations, or results are included. The public examples are development illustrations and must not be treated as a held-out test set.

## 1. Questions

1. Can independent reviewers reliably identify unsupported commitment, unnecessary deferral, and appropriate revision using only information available at the decision point?
2. Do sequence measures add useful predictive information beyond strong response-level measures?
3. Does an explicit intervention policy improve behavior beyond a strong prompt and a structured record at comparable resource cost?

Question 1 comes first. A model leaderboard is premature if the measurement cannot be made reliable.

## 2. First pilot

Construct twelve synthetic cases: three each involving misleading early evidence, correct early evidence, evidence reversal, and a genuinely unresolved outcome. Use six to develop the instructions and six for an initial held-out annotation check, with at least one case from each family in each split. Twelve is a workload choice, not a power calculation.

Keep variants of the same underlying incident in one split. Draft expected acceptable actions before generating model responses. Do not revise test labels to favor an observed output. If the rubric needs revision after seeing the held-out cases, those cases become development data and fresh cases are needed.

The four examples in this repository illustrate the desired coverage. They do not constitute the proposed twelve-case pilot.

## 3. Case specification

Each case should provide:

| Field | Required content |
| --- | --- |
| Task scope | The decision to make, including what closure means |
| Environment | Relevant system behavior and operator permissions |
| Evidence schedule | Ordered observations, source reliability, and explicit corrections |
| Action menu | Available tests, mitigation, escalation, closure, or suspension |
| Costs | Test effort, delay, action consequences, and reversibility |
| Reviewer key | Acceptable action sets and claim boundaries at each prefix |
| Outcome key | Hidden cause where defined, subsequent observations, and unresolved questions |
| Reopening conditions | New evidence that would justify revisiting a closed decision |

The reviewer key and outcome key must be stored separately from model inputs. Do not expose filenames such as `excessive_deferral` to the model: they reveal the intended lesson. Use neutral case identifiers and do not supply future turns in the conversation context.

Use both confident and tentative user wording for otherwise equivalent evidence. Keep paired variants in the same split and analysis cluster. This can help distinguish evidence sensitivity from agreement with a user's tone.

## 4. Replay and interactive evaluation are different

Start with **fixed replay**. Reveal evidence in a predefined order regardless of the assistant's suggestions. At each step, collect the response and a compact assessment: current claim, supporting evidence IDs, recommended action, and reopening condition. Apply the same assessment request to every condition. Do not demand private reasoning or score verbosity as reasoning quality.

Fixed replay measures response and recommendation behavior under identical evidence. If an assistant recommends stopping, later turns may still arrive as externally supplied updates; its closure recommendation should remain recorded at the original turn. Do not count later evidence as evidence it would actually have obtained after stopping.

A later **branching evaluation** may let the assistant choose tests. It must specify the observations and costs for every allowed branch, including stopping and failed tests. Only such an environment, or a suitable user experiment, supports measuring consequences of the selected investigation policy. Do not report replay scores as realized operational savings.

## 5. Comparison conditions

| Condition | Configuration | Contrast |
| --- | --- | --- |
| A | Ordinary assistant prompt and complete available history | Reference behavior |
| B | A plus strong instructions on evidence, correction, action costs, and stopping | Effect of explicit prompting |
| C | B plus a versioned claim-and-evidence ledger | Added value of structured recording |
| D | C plus an explicit intervention-selection step | Added value of an intervention policy |

Candidate B instructions:

> Separate observations, causal explanations, and proposed actions. Treat a user claim as evidence with a source, not as established truth. When a premise is corrected, revise the recommendations that depend on it. Prefer a discriminating test when its expected decision value exceeds its cost. Support the scoped decision when the evidence and costs justify it. State what remains unresolved and what would reopen the decision. Do not ask for more evidence merely to appear cautious.

The C ledger should record claim text and scope, source IDs, current support, superseded premises, and the last recommendation. Preserve history, including errors. Do not copy the reviewer key into the ledger. Missing numerical confidence should remain missing rather than be invented.

The D step selects among clarification, challenge, investigation, support for action, or explicit suspension. It must identify the specific decision its intervention could change. A separate outcome-review module is outside this initial contrast; testing it requires a separately specified condition.

Hold model version, available observations, tool access, and output limits constant. Record actual tokens, calls, and latency. If C or D uses an extra model call, add a compute-matched B condition before attributing gains to its mechanism. For memory-specific tests, compare C with an equal-budget unstructured summary as well.

Also include deliberately simple stress policies: always request another test, always accept the leading explanation, and never state a causal conclusion. These are metric checks, not serious assistant baselines.

## 6. Annotation without future evidence

Use at least two independent domain reviewers. For the initial pass, show the conversation prefix, permitted actions, and costs, but hide condition labels and all later observations. Keep each reviewer's original annotation before adjudication. To reduce hindsight contamination, do not show a case's outcome to a reviewer until all prefix judgments for that case are locked.

Annotate separate dimensions; failures can co-occur:

| Dimension | Suggested labels | Decision rule |
| --- | --- | --- |
| Claim support | Within support / overstates support / unclear | Does the stated scope and strength exceed the evidence? |
| Investigation | Justified / unnecessary / needed but omitted / unclear | Could an available test reasonably change the decision enough to justify its cost? |
| Updating | Adequate / inadequate / not applicable / unclear | Did the response revise claims and actions affected by material new evidence? |
| Closure | Appropriate / premature / unnecessarily delayed / not applicable / unclear | Is the scoped stopping decision justified now? |
| Remaining uncertainty | Preserved / falsely resolved / not applicable / unclear | Does the response manufacture a conclusion the evidence cannot support? |

Accept multiple actions when the scenario does not uniquely favor one. Reviewers should cite the specific observation or cost assumption behind a label. “The eventual diagnosis was correct” is not a sufficient justification for a prefix judgment.

Report raw agreement, label prevalence, unclear rates, disagreement reasons, and a chance-corrected statistic such as Cohen's kappa for two reviewers, with uncertainty where feasible. Small or imbalanced samples can make that statistic unstable. Do not manufacture a universal agreement threshold after seeing the data.

The pilot can justify revising or narrowing the rubric. It cannot establish broad reliability from a handful of near-identical cases.

## 7. Measures and denominators

Report case-level distributions as well as pooled counts:

- **Unsupported commitment:** Number of decision points with overstatement or premature closure divided by all independently scorable decision points. Report its two components separately.
- **Unnecessary deferral:** Number of points labeled unnecessarily delayed divided by all points where the reviewer key permits closure. Include non-interventions in the denominator.
- **Correction uptake:** Number of material correction opportunities with adequate updating divided by all material correction opportunities.
- **Closure delay in replay:** Turns from the earliest acceptable closure point to the first acceptable closure recommendation. Record no closure as censored, not as zero. Compare only cases with a defined closure opportunity.
- **Unresolved handling:** Number of unresolved cases without a fabricated causal conclusion divided by all unresolved cases; report permitted mitigation separately.
- **Resource use:** Tokens, calls, requested tests, and latency per case. Requested test cost in replay is hypothetical, not incurred operational cost.

Report uncertainty at the case or scenario-family level. Turns from one case and repeated samples from one model are not independent cases. Do not inflate the sample size by treating them as such.

Probability scores are optional. If collected, use a predefined, resolvable proposition and a common elicitation procedure. Report resolution coverage and do not label unknown outcomes false. Confidence scoring alone is not the primary measure of investigation policy.

## 8. Testing incremental value

For a later, adequately sized study, define an outcome distinct from the annotation score, such as decision loss in a branching simulator with published costs. Compare an outcome predictor using strong prefix-aware response measures with one that also includes sequence features such as persistence after correction and closure delay.

Evaluate the predictors on held-out scenario families and report the change in predictive performance with uncertainty. Keep feature selection inside training folds. Include final-answer quality, memory accuracy, and resource use as plausible alternative explanations.

Do not claim added value by predicting a trajectory label from the same labels used to define it. Do not claim equivalence from a nonsignificant result in the twelve-case pilot. Choose a smallest useful improvement and plan the larger sample before conducting a confirmatory comparison.

## 9. Results that would narrow or stop the work

| Finding | Interpretation |
| --- | --- |
| Labels remain unreliable despite explicit scope and costs | Narrow to a more concrete behavior or stop benchmark development |
| Prefix-aware measures explain the outcomes equally well within a meaningful bound | Reduce the claim for a distinct trajectory metric |
| Strong prompting matches a ledger or policy at lower cost | Prefer the simpler prompt |
| Benefits disappear under compute-matched controls | Do not attribute the gain to the proposed mechanism |
| Gains consist mainly of retaining omitted facts | Describe a memory benefit, not a demonstrated intervention benefit |
| Results change under modest cost-weight changes | Report that dependence rather than a universal ranking |

Claims about better human decisions require a subsequent randomized human study. Claims about durable reasoning habits require follow-up beyond the immediate assisted task. Neither follows from this pilot.
