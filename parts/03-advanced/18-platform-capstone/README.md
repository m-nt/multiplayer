# A18 — Advanced Platform Capstone

[Part overview](../README.md) · [Previous](../17-deployment-and-releases/README.md) · [Next](../README.md)

## Practicality and scope

Keep as a demonstrated integration of taught features, with a mandatory failure matrix and an explicitly reduced fallback track.

**Time:** 4–6 guided hours plus team project practice. **Prerequisite:** A17 and its passing lab.

**By the end:** Operate a small multiplayer platform and defend its safety, recovery, capacity, and release decisions.

**Boundary:** No claim of commercial production readiness, global scale, or zero-loss live-match recovery.

## Teaching notes

The capstone connects player queueing, exclusive allocation, admission, a real authoritative match, durable results, cleanup, telemetry, and release. A happy-path video alone does not demonstrate the platform's important properties.

State invariants before the demonstration: one active assignment per server generation; one authoritative result per match; no repeated progression effect; no unbounded queue; no unsupported client silently admitted. Bind every invariant to a test and a metric or log.

Use small but real concurrency and failure injection. The full track runs the Agones path and explains which guarantees belong to the platform versus the application. If a prepared cluster is unavailable, a container/custom-allocator submission can demonstrate most concepts but must be labeled reduced track; it does not satisfy the orchestration implementation outcome.

Document residual risks: pre-handoff result loss, region-level live-state loss, untested target devices, measurement scale, credential-management limitations, and operational ownership. Describe evidence, not hypothetical completeness.

## Worked example

During a concurrent allocation burst, one response is dropped, one server crashes, and one result is redelivered. The review traces each request to a single assignment/outcome, observes recovered capacity, and confirms no duplicated reward.

## Guided lab

1. Package a deployment/runbook, architecture, protocol, invariant matrix, and capacity report.
2. Run queue-to-result with real clients on a small fleet.
3. Demonstrate allocation contention, lost response, stale generation, server crash, duplicate result, and version mismatch.
4. Perform a staged release and rollback with an active match.
5. Have another team diagnose one undisclosed fault using telemetry and runbook.

## Evidence required to pass

- Mandatory: exclusive allocation and idempotent assignment survive concurrent requests/retries.
- Mandatory: authorized durable results create one progression effect.
- Mandatory: bounded cleanup/recovery and slow-client/backlog behavior are demonstrated.
- Mandatory for full track: Kubernetes/Agones lifecycle is demonstrated.
- Measurements, release evidence, reproducible setup, and known limits are included.

## Explain independently

Which safety property would you refuse to trade for availability during a control-plane partition, and how does the player experience that choice?

## Reading and preparation

Review A01–A17 artifacts and the teaching guide's rubric; no new infrastructure feature is introduced here. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
