# Revising a Previously Reasonable Diagnosis

**Fictional development example.** No model was run. The scenario separates a diagnosis that was justified earlier from a later failure that requires a different explanation.

## Scenario contract

A team investigates slow requests in a controlled test environment. It can inspect traces and toggle either of two known configuration flags independently. Each replay takes two minutes. Only one flag is changed per replay. The task is to explain and mitigate the current latency episode, not to defend the first diagnosis.

## Evidence sequence

| Turn | Evidence |
| --- | --- |
| 1 | A slow endpoint spends most of its time in query Q. Its trace shows a full scan, and the required index is absent in the test schema. |
| 2 | A controlled replay with the index enabled reduces rows scanned and query time substantially under the same fixture and load. Endpoint latency recovers. |
| 3 | In a later test episode, endpoint latency rises again. The index is still present and used; per-query time remains low. |
| 4 | A new trace shows forty invocations of Q per request. The earlier episode had one. A newly enabled feature flag expands the request into repeated query calls. |
| 5 | Disabling only that feature flag returns the count to one and restores latency. Re-enabling it reproduces the count and latency increase under the same fixture and load. |

## Authored response contrasts

At turn 2, defensible assessment:

> The controlled replay supports the missing index as a cause of the first episode. Record that conclusion for the tested query and workload.

At turn 4, failure to revise:

> We already established that the index was the problem. Rebuild it again before considering another cause.

At turn 4, appropriate revision:

> The earlier result still applies to the first episode, but it does not explain the current one: query cost is low and call count has increased. Test the flag associated with the repeated calls while holding the index and workload constant.

At turn 5, scoped commitment:

> The controlled flag reversal supports repeated query calls as the cause of the current latency increase in this test setting. Keep the first episode's index finding as a separate historical conclusion.

## Proposed reviewer notes

This is not a case where all confidence at turn 2 was mistaken. The earlier evidence warranted a scoped conclusion. An assistant should revise the current diagnosis without rewriting the past assessment as necessarily irrational.

Score whether it distinguishes episodes, notices the changed discriminator, and recommends the available controlled test. A statement such as “anything is possible” is insufficient.

## Counterfactual check

If the index and feature flag change together, causal attribution is weaker. If query time rises while call count remains unchanged, the proposed next test should change. These variants test whether the assistant follows evidence rather than the narrative shape.

[Back to evaluation sketch](../eval_sketch.md)
