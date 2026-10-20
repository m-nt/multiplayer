# A08 — Dynamic Allocation and End-to-End Match Lifecycle

[Part overview](../README.md) · [Previous](../07-agones-game-orchestration/README.md) · [Next](../09-multi-region/README.md)

## Practicality and scope

Keep, correcting the original ambiguous readiness order and adding explicit recovery for every handoff.

**Time:** 4 hours. **Prerequisite:** A07 and its passing lab.

**By the end:** Reconcile queue, assignment, admission, result, and cleanup without losing or duplicating ownership.

**Boundary:** Warm capacity is required; cold provisioning is a modeled optional branch.

## Teaching notes

The warm path is queue → match record → reserve Ready instance → bind assignment → issue tickets → admit roster → run → persist result → retire instance. Cold provisioning adds start/wait-for-ready before reservation. Do not advertise an endpoint that has not met readiness and assignment conditions.

These steps are not one transaction across every service. Persist a state machine with deadlines and retryable operations. A reconciliation worker compares desired lifecycle state with observed process/allocation state and advances or compensates it. Compensation means a defined forward action, such as revoking admission and stopping an unused server, not magically undoing time.

No-show policy matters: when only one participant arrives, wait a bounded interval, then cancel or apply the defined outcome. Cleanup must tolerate repeats and must not release an instance that still has active match authority.

## Worked example

Assignment commits but ticket delivery fails. The client queries its existing queue ticket and recovers the same assignment. It does not create a second match. If nobody arrives before the deadline, reconciliation retires the reserved instance.

## Guided lab

1. Implement durable lifecycle states, deadlines, and reason codes.
2. Inject failure after reservation, after assignment commit, during admission, and during result submission.
3. Run reconciliation repeatedly until each case reaches a defined state.
4. Test one-player no-show and capacity exhaustion.

## Evidence required to pass

- Every injected handoff failure has a bounded recovery or explicit terminal failure.
- No duplicate match is created by a status retry.
- Cleanup is idempotent and respects active authority.
- Players receive a meaningful waiting, failed, or connected outcome.

## Explain independently

Why should reconciliation be safe to run twice even when normal requests usually succeed?

## Reading and preparation

A02–A07 artifacts; AGONES lifecycle documentation as the observed external state. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
