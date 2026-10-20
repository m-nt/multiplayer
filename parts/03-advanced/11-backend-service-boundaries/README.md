# A11 — Backend Service Architecture

[Part overview](../README.md) · [Previous](../10-capacity-and-scaling/README.md) · [Next](../12-result-integrity-and-recovery/README.md)

## Practicality and scope

Keep a modular monolith as the baseline; use an asynchronous result/progression seam to teach service boundaries concretely.

**Time:** 3 hours. **Prerequisite:** A10 and its passing lab.

**By the end:** Define transaction ownership and asynchronous handoff without dual-write loss.

**Boundary:** Extract one worker only when useful; do not turn every module into a network service.

## Teaching notes

A module boundary organizes code; a service boundary adds independent deployment and network failure. Separate services can isolate load or ownership but introduce retries, authentication, observability, and consistency problems. Start from those needs rather than the number of boxes in a diagram.

A database transaction can commit related records atomically in one database. It cannot automatically include an unrelated broker publish. Writing a result and then publishing an event creates a gap if the process crashes between them. An outbox stores the intended event in the same transaction as the result; a worker later publishes/processes it.

Caches are derived state with invalidation and staleness policy. Do not authorize match admission from an indefinitely stale cache. Version event schemas and treat consumers as independently deployable readers. An API gateway can route and apply coarse controls, but the owning service still enforces operation-specific authorization.

## Worked example

Commit match result and ProgressionRequested outbox row together. A worker may process the row more than once after a crash, so the progression operation also deduplicates by match/player identity.

## Guided lab

1. Map identity, matchmaking, allocation, results, and progression to modules and data owners.
2. Implement result-plus-outbox transaction without adding a broker initially.
3. Use a separate worker to process pending outbox records.
4. Crash between processing and acknowledgement and inspect the duplicate path.

## Evidence required to pass

- No committed result lacks its intended outbox record.
- A worker retry cannot silently duplicate progression.
- The architecture decision states why modules remain together or are extracted.
- Cache policy does not bypass authoritative admission checks.

## Explain independently

Why does adding a message queue not eliminate the need for database transactions?

## Reading and preparation

POSTGRES: transaction/constraint semantics; A12 completes durable consumer recovery. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
