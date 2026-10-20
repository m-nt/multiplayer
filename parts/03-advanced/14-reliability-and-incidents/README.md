# A14 — Reliability and Incident Handling

[Part overview](../README.md) · [Previous](../13-observability/README.md) · [Next](../15-infrastructure-security/README.md)

## Practicality and scope

Keep a controlled game day with quantified detection and recovery, not a list of reliability patterns.

**Time:** 3–4 hours. **Prerequisite:** A13 and its passing lab.

**By the end:** Handle process loss and dependency overload under a documented player-facing policy.

**Boundary:** No transparent live-match recovery without an implemented checkpoint/replication design.

## Teaching notes

A retry budget limits extra attempts over a time window. Backpressure refuses or slows new work when capacity is exhausted. A circuit breaker stops repeated calls to a failing dependency for a period. None replaces idempotency, and each needs recovery behavior when the dependency returns.

Draining means stop accepting new assignments while honoring active matches or a declared deadline. Removing readiness alone may not terminate existing long-lived connections. Plan the sequence across allocator state, game admission, connections, and process shutdown.

Measure time to detect, time to restore service, and number of affected matches. Distinguish capacity recovery from match recovery. A process restart restores availability for future assignments; its old clients need a failed-match or supported resume outcome.

An incident timeline separates facts from hypotheses and records the user impact. A useful runbook names evidence, decision points, bounded actions, and escalation conditions.

## Worked example

Result storage slows down. Unbounded retries exhaust workers and prevent matchmaking. A bounded result queue and retry budget preserve capacity while exposing pending-result age and an admission policy for overload.

## Guided lab

1. Declare failure policy and stop conditions for a disposable game day.
2. Kill an active server, then separately slow result storage and exhaust ready capacity.
3. Measure detection/recovery and observe retry/backlog behavior.
4. Write a short incident timeline and improve one runbook step.

## Evidence required to pass

- Retries and queues remain bounded.
- Players receive the documented failed/pending outcome.
- Capacity recovery is not mislabeled as live-match restoration.
- The runbook can be followed by a partner.

## Explain independently

When should a platform reject a new match instead of accepting it into a growing queue?

## Reading and preparation

KUBERNETES probe/lifecycle material; A02 retry budgets and A13 signals. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
