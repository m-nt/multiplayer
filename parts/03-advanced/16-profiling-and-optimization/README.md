# A16 — Performance Profiling and Optimization

[Part overview](../README.md) · [Previous](../15-infrastructure-security/README.md) · [Next](../17-deployment-and-releases/README.md)

## Practicality and scope

Keep one measured bottleneck and one controlled improvement; avoid a generic optimization checklist.

**Time:** 3–4 hours. **Prerequisite:** A15 and its passing lab.

**By the end:** Identify the dominant bottleneck under a reproducible workload and validate a change against correctness.

**Boundary:** Optimize the measured system; do not extrapolate from synthetic microbenchmarks alone.

## Teaching notes

Profiling attributes cost: CPU samples identify hot work, allocation/heap profiles show memory pressure, and blocking/lock profiles reveal contention. Timing metrics tell you whether the player-facing budget is missed; a profile helps explain why. Capture them under the same workload and configuration.

Separate simulation, encoding, socket I/O, database work, and load generation. More network throughput does not mean faster gameplay. Garbage collection or shared-lock pauses can create tail tick misses even when average work looks cheap.

Change one cause, preserve protocol and rule fixtures, and compare repeated runs after warm-up. Report absolute units and distributions, not only a favorable percentage. Keep diagnostic endpoints private and bounded; profiling is itself extra work.

## Worked example

A snapshot serializer allocates a new temporary buffer for every client each tick. A measured allocation reduction may improve tick tails, but reusing one mutable buffer concurrently could corrupt messages. Retest ownership and output correctness.

## Guided lab

1. Use the A10 load fixture and capture CPU, allocation, and tick-time evidence.
2. Choose the largest justified bottleneck and state the hypothesis.
3. Implement one improvement and rerun equivalent trials.
4. Run simulation/protocol fixtures and compare tail timing, memory, and bytes.

## Evidence required to pass

- Before/after evidence includes absolute values and workload details.
- The claimed improvement is larger than observed run-to-run variation or is labeled inconclusive.
- Correctness and queue bounds remain intact.
- The report identifies the next limiting resource.

## Explain independently

Why can a faster serializer make the full system no faster when another stage is saturated?

## Reading and preparation

GO-PROFILE: CPU, heap, blocking, and tracing tools; A10 workload controls. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
