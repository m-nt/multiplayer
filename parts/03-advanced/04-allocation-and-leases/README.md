# A04 — Server Allocation and Fleet Management

[Part overview](../README.md) · [Previous](../03-matchmaking-algorithms/README.md) · [Next](../05-docker-packaging/README.md)

## Practicality and scope

Keep the allocator implementation, with explicit transactional exclusivity, ambiguous-result recovery, and stale-owner handling.

**Time:** 4 hours. **Prerequisite:** A03 and its passing lab.

**By the end:** Reserve one ready server generation per match under concurrent requests and safely recover abandoned allocations.

**Boundary:** A small database-backed allocator; no custom distributed consensus or live match migration.

## Teaching notes

Define server states Starting, Ready, Reserved, Running, Draining, Stopped, and Failed. State transitions have guards and owners. Ready means capacity can accept a reservation; Running is not free even if a heartbeat is healthy.

Allocation uses a stable request ID. In one transaction, claim a Ready row through a conditional update/lock, advance its allocation generation, and create a unique assignment record. Commit before returning the endpoint. A retry queries that record. Do not implement 'select free server, then later mark busy' without concurrency protection.

Heartbeats carry instance identity and generation. A missing heartbeat creates suspicion. Expired reservations need reconciliation: revoke admission, verify/terminate the old generation, then permit reuse. Never immediately mark an uncertain running process Ready just because a timer fired.

Fencing must protect admission and result acceptance as well as the allocator row. In the required lab, destroy an abandoned server process before reuse and create a fresh instance identity; do not attempt to heal an unknown live match in place.

## Worked example

Fifty simultaneous allocation requests contend for three ready instances. Exactly three distinct successful reservations are allowed; the others receive a clear capacity outcome. Repeating a winning request returns its original assignment.

## Guided lab

1. Create server/assignment tables with uniqueness and guarded transitions.
2. Run the contention harness against three ready instances.
3. Drop a post-commit response and retry its request ID.
4. Expire an unconsumed reservation and reconcile the old process before creating replacement capacity.
5. Resume a stale heartbeat/result writer and verify generation checks.

## Evidence required to pass

- No server generation belongs to two active assignments.
- Request retries recover the same committed outcome.
- An uncertain old instance cannot be reused or submit accepted stale results.
- Orphan cleanup is repeatable and auditable.

## Explain independently

Why is 'heartbeat missing, mark server free' capable of creating two owners of one match process?

## Reading and preparation

POSTGRES: transactions/constraints; AGONES-ALLOCATION later provides a managed comparison. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
