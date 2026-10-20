# A10 — Scaling Strategies and Capacity Planning

[Part overview](../README.md) · [Previous](../09-multi-region/README.md) · [Next](../11-backend-service-boundaries/README.md)

## Practicality and scope

Keep, replacing assumed players-per-server numbers with measured load and explicit warm-capacity math.

**Time:** 3–4 hours. **Prerequisite:** A09 and its passing lab.

**By the end:** Estimate safe concurrent capacity and reserve enough headroom for measured startup delay.

**Boundary:** A local forecast with assumptions; no claim that one laptop benchmark predicts a cloud fleet.

## Teaching notes

Measure simulation work, memory, bandwidth, and tick deadline misses under representative clients. Bot clients must produce the same input/update behavior as real clients. Record warm-up and steady-state durations, tail latency, and whether the load generator saturates first.

For one match per process, active matches are approximately concurrent players divided by players per match, rounded up, adjusted for partial matches and reserved capacity. Per-machine capacity is constrained by the tightest measured CPU, memory, network, and timing budget, with headroom.

Autoscaling has delay: detection, scheduling, image pull, initialization, readiness. A warm pool absorbs arrivals during that interval. Scale-in should remove unused/draining capacity and avoid terminating active matches. Track cost per completed match using stated sample prices, not unverified current provider prices.

## Worked example

For a hypothetical arrival rate of 2 matches/second and measured 30-second replenishment time, 60 ready slots cover steady arrivals during replenishment before adding burst/safety allowance. This is a planning approximation, not a guarantee under bursts.

## Guided lab

1. Load-test multiple server processes with a paced bot harness.
2. Identify the first violated budget and repeat around that boundary.
3. Measure cold/warm startup time and calculate a ready-pool estimate.
4. Replay a burst and delayed scale-up; define a safe scale-in policy.

## Evidence required to pass

- Capacity claims include machine, workload, duration, and tail tick behavior.
- The load generator is measured separately.
- The forecast includes warm reserve and stated assumptions.
- Active matches survive scale-in in the test.

## Explain independently

Why can low average CPU coexist with unacceptable tick deadline misses?

## Reading and preparation

GO-PROFILE and AGONES Fleet autoscaling concepts; distinguish process from node capacity. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
