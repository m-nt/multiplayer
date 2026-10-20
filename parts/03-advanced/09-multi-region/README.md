# A09 — Multi-Region Infrastructure

[Part overview](../README.md) · [Previous](../08-end-to-end-match-lifecycle/README.md) · [Next](../10-capacity-and-scaling/README.md)

## Practicality and scope

Keep as a deterministic two-region simulation before any paid geographical deployment.

**Time:** 2–3 hours. **Prerequisite:** A08 and its passing lab.

**By the end:** Choose a region using all players' latency constraints and current capacity, and define outage policy.

**Boundary:** No automatic migration of an active match or globally replicated database implementation.

## Teaching notes

Geographic distance is only one influence on latency. Use measured or supplied per-player latency distributions to candidate regions, freshness of measurements, and available compatible capacity. Choose an objective such as minimizing the party's worst estimated latency, with a maximum acceptable bound.

Capacity and player population can force a trade-off between waiting and less favorable latency. Make the fallback explicit to players and tests. An unavailable nearest region does not mean silently accepting arbitrarily poor gameplay elsewhere.

Regional outage policies differ for new and existing matches. New assignments can fail over to permitted capacity; an active server lost with its region has lost live state unless a separate recovery design exists. Result writes and account data have their own locality and consistency concerns.

## Worked example

A has estimated latency 30/110 ms to regions X/Y, while B has 100/40 ms. With equal capacity, choosing X because A is party leader ignores B; compare maximum latency 100 versus 110 and the accepted threshold.

## Guided lab

1. Create two simulated regions with latency/capacity/version fixtures.
2. Implement a deterministic selection objective and tie-breaker.
3. Exhaust one region, then make it unavailable.
4. Record outcomes for new requests and already-running matches separately.

## Evidence required to pass

- Selection considers every party member and measurement age.
- Capacity/compatibility constraints cannot be bypassed by latency preference.
- Outage tests do not claim live-match migration.
- The fallback policy bounds unacceptable latency.

## Explain independently

When should matchmaking keep a player waiting instead of sending them to a distant available region?

## Reading and preparation

AGONES: latency testing and multi-cluster allocation as optional implementation context; use simulated data for the required lab. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
