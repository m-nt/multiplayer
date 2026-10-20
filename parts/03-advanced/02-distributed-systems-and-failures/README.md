# A02 — Distributed Systems Before Allocation

[Part overview](../README.md) · [Previous](../01-production-architecture/README.md) · [Next](../03-matchmaking-algorithms/README.md)

## Practicality and scope

Move original advanced lesson 10 here; leases, ambiguous timeouts, and atomic decisions are prerequisites for safe allocation.

**Time:** 3–4 hours. **Prerequisite:** A01 and its passing lab.

**By the end:** State safety/liveness invariants and reproduce an ambiguous operation outcome.

**Boundary:** Use a database as the decision authority; do not implement a consensus algorithm.

## Teaching notes

A remote operation can commit even when its response never arrives. A timeout says 'unknown to the caller.' Retries therefore need a stable operation ID and a queryable outcome. Partial failure means one component can be unavailable while another keeps running.

Safety says something bad never happens, such as two live assignments owning one server generation. Liveness says useful progress eventually happens under stated assumptions. If the authoritative database is unreachable, rejecting new allocations can preserve safety while reducing availability.

A lease grants time-limited ownership according to an agreed authority's clock. Expiration does not stop a paused old process. Add a monotonically increasing generation/fencing value and enforce it wherever stale ownership could cause effects. A token in a log has no fencing power by itself.

Use local monotonic time for elapsed waits and database-authoritative time for persisted lease decisions. Do not assume every machine's wall clock agrees. Backoff with jitter and a total retry budget limits amplification but does not make an unsafe operation safe.

## Worked example

Worker A pauses after obtaining generation 7. Its lease expires; worker B obtains generation 8. When A resumes, mutation attempts carrying 7 must fail against the authoritative generation check.

## Guided lab

1. Build a small operation harness that can commit then drop the response.
2. Retry with the same ID and recover the original outcome.
3. Pause a lease holder, advance the test clock, issue a new generation, then resume the old holder.
4. Write the safety invariant and the availability sacrifice during database loss.

## Evidence required to pass

- Dropped responses do not create duplicate logical operations.
- Stale generations are rejected by the receiving boundary.
- Tests use controlled time rather than long sleeps.
- The student distinguishes 'suspected dead' from 'certainly stopped'.

## Explain independently

Why is a distributed lock with an expiry insufficient if an old worker can still write after it resumes?

## Reading and preparation

POSTGRES: transaction isolation and locking; apply the supplied failure timelines before allocator code. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
