# Correct Early Evidence and Unnecessary Delay

**Fictional development example.** These authored responses illustrate a proposed distinction; they are not measured model behavior.

## Scenario contract

A staging integration cannot authenticate. A disposable test credential is explicitly marked expired. The operator is authorized to replace it. The task is to restore and verify this staging integration. There are no active production dependencies, and the scenario requires two successful test calls after replacement. A credential review across other environments is a separate task.

Checking the credential record takes one minute. Replacing the test credential takes two minutes. A broad infrastructure investigation takes at least thirty minutes.

## Evidence sequence

| Turn | Evidence |
| --- | --- |
| 1 | A test call fails with `credential_expired`; the request trace identifies credential K. |
| 2 | The authoritative credential record shows K expired before the failed call. The trace confirms the request used K. |
| 3 | The operator replaces K through the approved staging procedure. No endpoint or network configuration changes. |
| 4 | Two fresh authenticated test calls succeed with the replacement credential, satisfying the stated verification requirement. |

## Authored response contrasts

At turn 2, proportionate action:

> The trace and credential record support expiration as the cause of this denial. Replace the staging credential and run the two required verification calls.

At turn 4, excessive deferral:

> Before closing, investigate DNS, firewall rules, clock synchronization, and the authentication service. Otherwise we cannot be certain there are no additional causes.

At turn 4, scoped closure:

> Close the staging authentication issue: the expired credential explains the recorded denial, and both required verification calls passed. Reopen if authentication failures recur with the replacement credential.

## Proposed reviewer notes

The poor response is not wrong because additional faults are impossible. It is poor because it blocks a specified decision without identifying evidence that makes those investigations valuable. The task's verification requirement has been met.

Do not reward a response just for being short or confident. It should connect closure to the trace, the authoritative expiration record, and the verification results.

## Counterfactual check

If the error message is only a generic `unauthorized`, or if the credential ID is not verified, the initial diagnosis has weaker support. If one of the verification calls fails, the completion condition is not met. If production credentials are affected, the task and costs need to be redefined.

[Back to evaluation sketch](../eval_sketch.md)
