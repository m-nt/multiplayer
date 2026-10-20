# I10 — Network Reliability and Performance

[Part overview](../README.md) · [Previous](../09-prediction-and-reconciliation/README.md) · [Next](../11-identity-backend-and-results/README.md)

## Practicality and scope

Keep measurement and backpressure mandatory; delta compression and spatial interest are bounded extensions for this two-player game.

**Time:** 2–3 hours. **Prerequisite:** I09 and its passing lab.

**By the end:** Enforce traffic/queue budgets and reduce measured waste without losing essential state.

**Boundary:** Do not build a new reliable transport or claim scalability from two clients.

## Teaching notes

Budget bytes, messages, serialization time, and queue age separately. A queue that grows while throughput looks high is accumulating latency. Replace stale unsent full snapshots where safe; reliable final results require a different retry path.

Delta encoding sends changes relative to a baseline. Without a known baseline, a delta cannot reconstruct state; include resync behavior before treating compression as complete. Quantization trades precision for size and must be evaluated against collision/presentation tolerance.

Interest management answers who needs which state. Visibility does not authorize an action. In Pong everyone sees the court, so a spectator/private-field test is enough; a spatial grid is an optional harness with synthetic entities.

## Worked example

Keep only the newest unsent full snapshot for a slow client. Do not apply that same replacement policy to two distinct match-result records, because neither supersedes the other.

## Guided lab

1. Measure payload bytes, serialization time, queue depth, and age over a fixed run.
2. Add a slow reader and implement the declared bounded queue policy.
3. Reduce one source of waste and rerun identical conditions.
4. If adding deltas, drop the baseline and demonstrate a full-state resync.

## Evidence required to pass

- Traffic comparison includes equivalent gameplay and duration.
- Slow readers cannot cause unbounded memory growth.
- Important outcomes are not silently dropped by the snapshot policy.

## Explain independently

Why can a smaller payload produce worse frame timing if encoding it is expensive?

## Reading and preparation

NETCODE: snapshot encoding trade-offs; GO profiling material for measurement. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
