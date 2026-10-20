# A01 — Production Multiplayer Architecture

[Part overview](../README.md) · [Previous](../README.md) · [Next](../02-distributed-systems-and-failures/README.md)

## Practicality and scope

Keep, but start with requirements and failure ownership rather than a diagram containing every possible service.

**Time:** 2 hours. **Prerequisite:** I16 plus the Linux/container bridge and a prepared disposable cluster.

**By the end:** Define control-plane and gameplay responsibilities, ownership, and measurable service expectations.

**Boundary:** Use a modular backend unless a measured need justifies separation.

## Teaching notes

The gameplay data plane carries inputs and state between admitted clients and their server. The control plane selects players, assigns capacity, manages versions, and coordinates lifecycle. An existing match should not need a successful matchmaking HTTP call for every tick.

State ownership must be explicit: one server owns a live match, the backend owns assignment records, and durable storage owns committed results. Distinguish the record that describes a server from the process that actually exists. They can disagree after failure.

Choose service indicators before dashboards: assignment latency from queue eligibility to usable endpoint, successful admission ratio, and tick deadline misses. State a lab target and the measurement window. A diagram cannot establish reliability without an operational contract.

## Worked example

During a matchmaking outage, existing matches continue, but new players cannot receive assignments. If result submission is also unavailable, completed matches enter a bounded pending-result policy instead of silently losing outcomes.

## Guided lab

1. Draw the running intermediate system with state owners and trust boundaries.
2. Mark which network calls are required during each match phase.
3. Stop the queue service while an existing match runs.
4. Define three lab service indicators, target thresholds, and observation windows.

## Evidence required to pass

- The diagram separates live state from durable records.
- An existing match's dependency on backend availability is demonstrated.
- Each proposed target has an exact numerator/denominator or timing definition.

## Explain independently

Which backend failures should stop an existing match, and which should only prevent new matches?

## Reading and preparation

OTEL: signals overview; review I16 limitations. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
