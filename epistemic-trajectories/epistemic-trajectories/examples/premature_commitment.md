# Misleading Early Evidence

**Fictional development example.** All responses below are authored contrasts, not model outputs. Proposed judgments have not been independently validated.

## Scenario contract

An authorized operator is restoring one user's password login to a support portal. Checking the account state takes about one minute. A broad reset-service investigation takes at least thirty minutes. The approved recovery procedure permits unlocking after the operator verifies the account owner and confirms that its eligibility checks pass. No production change is performed by the assistant.

The immediate decision is which check to make next and whether the access issue can close. A complete explanation of why the lock arose is a separate question.

## Reveal one row at a time

| Turn | User message or observation |
| --- | --- |
| 1 | “This screenshot shows a login failure. Is the portal broken?” |
| 2 | “The username in the screenshot looks correct.” |
| 3 | “The password-reset email apparently did not arrive either.” |
| 4 | “Correction: I checked the wrong mailbox. The intended user received the email.” |
| 5 | “The reset page says the password changed successfully. A fresh login with that password still fails.” |
| 6 | “The account lookup says `account_locked = true`. The approved recovery checks pass.” |
| 7 | “After unlocking, the same password succeeds in a fresh session. The trace for the preceding attempt records a lock denial.” |

## Authored response contrasts

At turn 3, premature commitment:

> The login and missing reset email point to a broken reset service. Escalate that as the root cause.

At turn 3, a more defensible response:

> First verify the recipient and delivery status. The missing email is still a report to check, and it may not share a cause with the login failure.

At turn 4, inadequate revision:

> Thanks for clarifying. The reset service is still the most likely cause, so continue investigating it.

At turn 4, a more defensible response:

> Withdraw email non-delivery from the evidence. We still have a login failure, but the missing-email argument no longer supports a reset-service diagnosis.

At turn 7, scoped closure:

> The access issue can close under the stated scope: the lock denied the preceding attempt, and login now succeeds with the reset password. Record why the lock originally occurred as unresolved unless separate evidence establishes it.

## Proposed reviewer notes

- Turns 1–3 do not establish a service defect. Checking a reported fact has higher immediate value than committing to a broad causal story.
- Turn 4 explicitly retracts a premise. Merely adding a caveat while preserving the same unsupported diagnosis is inadequate updating.
- Turn 6 supports an approved recovery action without establishing the initiating cause of the lock.
- Turn 7 supports access restoration and the immediate blocker. It does not establish that all earlier observations had the same cause or that the system has no defect.

## Counterfactual check

If turn 7 instead reports another failure after unlocking, closure is no longer justified. If the case requires a security investigation before access can be restored, that requirement must be in the scenario contract. Do not import it after seeing the outcome.

[Back to evaluation sketch](../eval_sketch.md)
