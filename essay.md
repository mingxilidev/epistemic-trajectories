# Beyond Single-Turn Calibration

## Evaluating Epistemic Trajectories in AI Assistants

*Research note · September 2026 · No experimental results*

An AI assistant can be careful in its wording and still be unhelpful about when to stop investigating. It can acknowledge uncertainty, list alternatives, and avoid making an obviously false statement, while leaving a user more committed to an unsupported explanation. It can also keep asking for evidence after a narrow, practical decision is already justified.

This note proposes a small evaluation question: **Do measures of how an assistant investigates, updates, and stops across a conversation provide useful information beyond measures of individual response quality?**

The answer might be no. A sufficiently strong response-level rubric, given the relevant conversation history, may already capture the failures described here. The purpose of this proposal is to make that possibility testable, alongside the possibility that evaluating the sequence adds something useful.

The initial setting is technical troubleshooting. It offers concrete hypotheses, sequential evidence, and decisions with identifiable consequences. Long-term personal assistants are a motivation, but a short troubleshooting experiment cannot establish their long-term effects.

## 1. A reasonable sentence can belong to an unreasonable sequence

Imagine a user investigating a login failure. The user suspects a password-reset bug. An assistant replies that this is plausible. The user reports another symptom, and the assistant suggests an explanation consistent with the same bug. Later, the user discovers that an earlier observation was wrong. The assistant acknowledges the correction but continues recommending work based on the original story.

Each reply might contain cautious language. The assistant may never say that the diagnosis is certain. Yet the interaction can still move toward an unjustified conclusion because the assistant's recommended actions and summary of the case continue to privilege an explanation whose support has weakened.

There is an opposite pattern. The user obtains direct evidence of an account lock, follows an authorized recovery procedure, and confirms successful login. The assistant then requests unrelated network traces, broad infrastructure checks, and a comprehensive audit before allowing the access incident to close. These investigations could conceivably uncover something, but the assistant has not explained why they matter to the decision being made.

Both patterns concern a relationship between evidence and behavior over time. What was the assistant willing to conclude? Which test did it prioritize? Did it withdraw a recommendation when its premise disappeared? Did it recognize that restoring access and establishing a complete causal history are different tasks?

I use **epistemic trajectory** to mean this observable sequence of assessments, investigations, revisions, and commitments. The term does not imply access to a model's internal beliefs. A model's stated confidence is an output to evaluate, not a transparent measurement of an internal mental state.

## 2. Two failures with different costs

**Premature commitment** occurs when an assistant endorses a claim or closes an investigation more strongly than the evidence and decision context justify. The error may involve confidence, but it can also appear in behavior: ignoring a cheap discriminating test, presenting a provisional explanation as the incident's established cause, or continuing a recommendation after its supporting evidence is withdrawn.

**Excessive deferral** occurs when an assistant keeps blocking a decision or recommending investigation whose expected value does not justify its cost. This requires a specified decision. It is meaningless to say that evidence is sufficient without asking what it is sufficient for.

These failures are counterparts, not equal quantities. Their costs depend on the case. Delaying an irreversible change may be prudent; delaying a reversible recovery step during an outage may be costly. A dataset should represent that asymmetry rather than reward equal rates of commitment and caution.

An intervention that makes an assistant more skeptical could reduce premature commitment while increasing excessive deferral. That is a hypothesis to test, not an established tradeoff for every model. An intervention could improve both, worsen both, or have no measurable effect.

The proposed target is **appropriate convergence**: making conclusions and decisions as specific as the evidence and costs allow, while remaining able to revise them. Sometimes that means accepting an explanation. Sometimes it means taking action while leaving the cause unresolved. Sometimes it means stopping because the available investigation is exhausted, without assigning a cause at all.

## 3. Separate credibility, investigation, and action

Three questions are easy to compress into a single certainty scale:

| Question | What it concerns | Example |
| --- | --- | --- |
| How credible is this claim? | Evidential support | An account lock is a plausible explanation for the failed login. |
| Is investigating it worthwhile? | Expected usefulness of information relative to cost | Checking the account state takes little time and could change the next step. |
| Should we act now? | Consequences, constraints, and reversibility | An authorized recovery action may be warranted before the lock's full history is known. |

These questions are related, but their answers need not move together. A relatively unlikely explanation can deserve the next test if checking it is cheap and decisive. A likely explanation can deserve little additional investigation if no available test changes what should happen next. Action can be justified before causal certainty when its expected benefits and costs support it.

This distinction also matters for calibration. Calibration concerns the relationship between stated probabilities and outcomes across claims. It does not mean lowering confidence whenever a decision is risky. A costly false positive changes the action threshold; it does not, by itself, make the underlying proposition less likely.

A first evaluation need not demand numerical probabilities on every turn. It can ask the assistant to state the leading explanation, the evidence supporting it, and what would change the recommendation. If probabilities are collected, they should refer to stable, clearly scoped propositions. A claim about one login attempt cannot silently become a claim about an entire authentication system.

The same discipline applies to exploration. A speculative explanation may enter the candidate set without being treated as established. To earn greater support, a causal explanation should survive a test that distinguishes it from relevant alternatives. Merely inventing a test is not evidence that the explanation is true. Direct observations, meanwhile, do not require elaborate hypothetical predictions before they can be recorded as observations.

## 4. A worked example

Consider a fictional support portal. An operator is authorized to inspect account status and use the approved recovery procedure. The immediate goal is to restore one user's password login. Any broader prevention review can be recorded separately. This scope is part of the scenario, not a universal rule for security incidents.

The following sequence is authored to illustrate an evaluation problem. It is not a transcript of a real incident or a model test.

| Turn | Evidence available at this point | A proportionate response |
| --- | --- | --- |
| 1 | A screenshot shows a generic login failure. | Record the symptom; do not assign a cause. |
| 2 | The operator believes the entered username is correct. | Treat that as a useful report, while noting that a screenshot does not establish the account's backend state. |
| 3 | The operator says the reset email did not arrive. | Check delivery and recipient identity before treating non-delivery as established. |
| 4 | The operator corrects the report: the wrong mailbox was checked. The intended user received the email. | Withdraw non-delivery as evidence. Do not carry its implications into later summaries. |
| 5 | The reset page reports success, but a fresh login still fails. | Distinguish password reset from permission to log in; inspect the failure reason or account state. |
| 6 | An account lookup reports `account_locked = true`. | Treat the lock as a strong candidate for the current blocker; follow the approved recovery procedure if its conditions are met. |
| 7 | After unlocking, the same new password succeeds in a fresh session; the trace records that the preceding failure was a lock denial. | Support closing the scoped access incident. Record the lock as the immediate blocker, while leaving its initiating cause unresolved if it was not investigated. |

The crucial moment at turn 4 is not simply saying “thanks for clarifying.” The assistant must revise the dependency structure of its explanation. Any argument that relied on missing reset email should lose that support. The fact that the assistant previously repeated an observation does not make the observation an independent source of evidence.

At turn 6, the lock is informative, but its existence alone does not establish every causal claim. It does not show why the account locked, whether an earlier screenshot involved exactly the same attempt, or whether the reset workflow itself was defective. Those are separate propositions.

At turn 7, the supplied trace and successful retry support a practical conclusion: the observed lock blocked the preceding attempt, and access now works with the reset password. The operator can close the access-restoration task under the stated scope. A prevention task may remain open. Closure should not silently upgrade to “the authentication system has no bugs.”

An assistant that commits too early might declare a reset-service defect at turn 3 and treat the later lock as secondary without evidence. An assistant that defers excessively might refuse closure at turn 7 until every theoretical cause is eliminated. A better response preserves the narrower conclusion and identifies any remaining question without making that question an automatic blocker.

The repository's [examples](examples/premature_commitment.md) include alternate trajectories. They are teaching materials for the construct, not evidence that models exhibit it or labels on which a benchmark should be trained and evaluated.

## 5. What might evaluating the sequence add?

The distinction between a response and a trajectory needs care. A response can be evaluated with the entire conversation prefix available. Such an evaluation could notice stale assumptions, needless tests, or a failure to revise. It would be misleading to define response evaluation as deliberately ignorant and then claim victory over it.

The empirical question is about **incremental value**. Does measuring persistence of unsupported claims, time to justified closure, and recovery after correction predict consequential failures beyond strong prefix-aware response ratings?

For example, averaging turn scores might obscure a repeated low-level failure. Several individually modest omissions could collectively keep an obsolete hypothesis active. Conversely, one poor reply might be followed by a clear correction that prevents a harmful recommendation. A final-answer score would miss the path; an average could miss the recovery. Whether explicit sequence measures improve prediction remains to be shown.

The same distinction prevents an inflated claim about user benefit. A replay can measure assistant recommendations. It cannot establish that users adopt them, that their beliefs improve, or that they become better independent reasoners. Those outcomes require human participants and measures of their decisions. Simulating a cooperative user does not supply that evidence.

Short, controlled conversations are therefore a modest starting point. They let us inspect a behavior under known conditions. Generalizing to long-term assistance would require separate work on memory, changing goals, repeated exposure, and user dependence.

## 6. A small evaluation before a large system

A first pilot could use twelve synthetic trajectories, with three cases in each of four families. This is a practical scope for testing annotation, not a statistically powered model comparison.

| Family | What the case tests |
| --- | --- |
| Misleading early evidence | Whether suggestive but weak observations acquire unjustified authority |
| Correct early evidence | Whether the assistant accepts sufficiently strong evidence and avoids needless delay |
| Evidence reversal | Whether it revises a previously defensible assessment when the situation or evidence changes |
| Legitimately unresolved | Whether it can stop or act without inventing a definitive explanation |

These categories can overlap. Their purpose is coverage. In particular, a reversal case should sometimes make the earlier assessment reasonable; otherwise the evaluation merely punishes an initial mistake and never tests legitimate revision.

Each case needs an explicit task, available actions, test costs, delay costs, and evidence-release schedule. The author should also specify which questions remain unanswerable. A hidden causal story is useful for outcome scoring, but reviewers judging a decision must not see future evidence.

Two independent reviewers could first annotate a small development set using only the evidence available at each decision point. They should identify a set of acceptable actions when more than one is reasonable. Disagreements may indicate ambiguous instructions or missing cost information rather than reviewer incompetence.

After revising the rubric, freeze it and use fresh cases for a held-out check. Publish raw agreement, disagreement reasons, and an agreement statistic appropriate to the labels. Agreement does not establish validity: reviewers can consistently favor an inappropriate policy. Deliberate counterexamples and sensitivity to cost assumptions are also necessary.

Only after this measurement check is it worth comparing assistants. The [evaluation sketch](eval_sketch.md) describes four conditions: an ordinary assistant, a strong prompted assistant, a prompted assistant with a claim-and-evidence record, and that record plus an explicit intervention policy. The same base model and evidence access should be used. Differences in actual token use, latency, and tool calls must remain visible.

The strongest simple prompt matters. If it performs as well as a structured system at lower cost, the elaborate system has not earned its complexity.

## 7. Score decisions without hindsight

There are two distinct evaluation questions. Was the recommendation reasonable given the information available then? What happened after the recommendation? A lucky guess can produce the correct final diagnosis through poor reasoning. A justified decision can have an unfavorable outcome. Combining these questions into one correctness label loses the distinction the evaluation is meant to capture.

The first layer should score support for claims and suitability of actions at each decision point. Reviewers should have the conversation prefix, the allowed actions, and the stated costs. The second layer can examine later outcomes, such as recovery, unnecessary test requests, or persistence of an incorrect explanation.

In a fixed evidence replay, the assistant does not control which evidence arrives. That design can measure recommendations to stop, investigate, or revise. It cannot honestly measure the operational consequences of following those recommendations. For that, a branching simulator needs to define what each available action reveals and costs.

A useful report should retain several dimensions: unsupported commitment, unnecessary investigation, correction uptake, scoped closure, unresolved-case handling, and resource use. These should not automatically collapse into a universal “epistemic quality” score. If a combined decision-loss score is used, its weights and outcome model should be published, and conclusions checked under plausible alternative weights.

Degenerate strategies should be tested directly. Always asking for more evidence must incur costs in resolved cases. Always endorsing the leading explanation must fail where an inexpensive check could change the decision. Listing alternatives without choosing a useful next step should not earn credit merely for sounding nuanced.

## 8. Positioning relative to existing work

This proposal does not introduce multi-turn evaluation, belief revision, or the idea that information gathering has a cost. Its possible contribution is a narrow evaluation design connecting these ideas around investigation and stopping decisions. Establishing novelty would require a broader literature review.

Four adjacent papers help locate the question:

- Sharma et al., [*Towards Understanding Sycophancy in Language Models*](https://arxiv.org/abs/2310.13548), study assistants' tendency to match user beliefs and the role of preference judgments. This motivates separating agreement from judgment quality. It does not establish that anti-sycophancy interventions cause excessive deferral.
- Kadavath et al., [*Language Models (Mostly) Know What They Know*](https://arxiv.org/abs/2207.05221), examine model self-evaluation and calibration, including limitations in generalization. This motivates treating reported confidence as something to validate. It does not show that a confidence estimate determines the right next action.
- Buçinca, Malaya, and Gajos, [*To Trust or to Think*](https://arxiv.org/abs/2102.09692), find that cognitive forcing interventions can reduce overreliance while receiving worse subjective ratings. This motivates measuring intervention burden alongside decision quality. It does not establish the effectiveness of this proposed assistant design.
- Laban et al., [*LLMs Get Lost In Multi-Turn Conversation*](https://arxiv.org/abs/2505.06120), report multi-turn performance degradation and difficulties recovering from early assumptions in their evaluated tasks. This is directly adjacent evidence that interaction structure matters. The present question additionally makes the cost of continued investigation and justified stopping explicit.

These connections support investigating the question. They do not prove that the proposed construct is distinct, that current benchmarks omit it, or that a separate benchmark would be useful. An extension to existing evaluations may be preferable to a new benchmark.

## 9. What would change the proposal?

Several outcomes should lead to revision rather than defensive reinterpretation.

If reviewers cannot agree after the task and cost assumptions are made clear, the construct may be too broad. The next version should narrow to an observable behavior, such as withdrawing recommendations after their premise is retracted.

If strong response-level measures predict the relevant outcomes just as well, trajectory reporting may still be descriptive, but the claim that it adds a distinct evaluation signal should shrink. That conclusion requires enough data to bound a meaningful difference; a tiny pilot with an uncertain estimate cannot establish equivalence.

If a claim ledger helps only because it retains information that another condition loses, the result supports memory management. It does not by itself establish a benefit from selective intervention. If extra inference budget explains the gain, the intervention mechanism has not been isolated.

If an assistant improves scored decisions only by generating burdensome explanations, actual users may not benefit. Human evaluation would need to examine whether they follow, ignore, or abandon the recommendations, and whether the burden differs across participants.

The immediate deliverable is therefore a clear question, a few inspectable cases, and a protocol that can fail. A full assistant architecture should follow evidence that a simpler intervention is insufficient. The useful ambition is to learn whether we can measure when an assistant should keep investigating, when it should revise, and when it should help a user proceed.

---

*This is an evaluation hypothesis, not a demonstrated result. See [references.md](references.md) for the selected reading list and [eval_sketch.md](eval_sketch.md) for the proposed first experiment.*
