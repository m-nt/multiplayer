# A03 — Matchmaking Architecture and Algorithms

[Part overview](../README.md) · [Previous](../02-distributed-systems-and-failures/README.md) · [Next](../04-allocation-and-leases/README.md)

## Practicality and scope

Deepen the two-player queue with constraints, concurrent workers, and quality measurements; do not require a full skill-rating research system.

**Time:** 3–4 hours. **Prerequisite:** A02 and its passing lab.

**By the end:** Match eligible players once while balancing wait time, region latency, and bounded quality criteria.

**Boundary:** Use synthetic ratings as inputs; rating-model training and live experimentation are outside scope.

## Teaching notes

Separate hard constraints from preferences. Compatible protocol and party integrity may be hard constraints; rating distance can widen with wait time. Region choice must consider all party members and available capacity, not only the party leader.

A queue ticket is durable state with one active ticket per player or party. Workers claim compatible tickets transactionally. Choose a database mechanism such as conditional updates or row locks and unique active-membership constraints. Cancellation races with claiming; its accepted result must match the committed state.

Measure wait-time percentiles, unmatched population, rating gap, and cancellation outcomes using fixed synthetic arrivals. Average wait alone can hide starvation. A wider rating window improves some waits at the cost of match quality; quantify both.

## Worked example

A 100-rating-point preference widens with waiting time, but a two-person party cannot be split across matches merely because splitting would improve the average rating gap.

## Guided lab

1. Define hard constraints and a widening policy with a maximum bound.
2. Replay a seeded synthetic queue with parties, modes, and regions.
3. Run two workers and race cancel against claim.
4. Compare wait distributions and match quality for two policies.

## Evidence required to pass

- No ticket/player is consumed by two matches.
- Cancellation results agree with committed assignments.
- The report includes tail wait and starvation cases, not only averages.
- Party and protocol constraints remain intact.

## Explain independently

When can reducing average queue time make the player experience worse?

## Reading and preparation

POSTGRES: isolation/locking and unique constraints; A02 retry semantics. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
