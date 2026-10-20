# A13 — Observability and Production Diagnostics

[Part overview](../README.md) · [Previous](../12-result-integrity-and-recovery/README.md) · [Next](../14-reliability-and-incidents/README.md)

## Practicality and scope

Keep a diagnostic exercise, building on existing logs rather than waiting until this lesson to instrument anything.

**Time:** 3 hours. **Prerequisite:** A12 and its passing lab.

**By the end:** Trace a failed assignment and measure user-visible service health without unbounded telemetry cardinality.

**Boundary:** Instrument one complete request path; do not log every tick payload in normal operation.

## Teaching notes

Metrics summarize counts and distributions, logs record specific events, and traces connect timed operations across boundaries. Correlation IDs connect queue, match, allocation, and result activity. Carry them through asynchronous work; a process-local request ID alone cannot join the whole story.

Use low-cardinality metric labels such as outcome, region, mode, and build version. Putting a unique player or match ID on every metric series grows storage without bound. Those identifiers belong in appropriately protected logs/traces, with sampling and retention policies.

Measure the experience: time to usable assignment, admission success, disconnect ratio, tick misses, ready capacity, result backlog age. Separate dependency latency from local CPU time. A dashboard is useful only when a known failure creates a recognizable signal and a responder can follow it to evidence.

## Worked example

Allocation requests are fast but players wait because reserved servers never become admissible. An end-to-end assignment indicator catches this, while an allocator-handler latency metric alone looks healthy.

## Guided lab

1. Instrument queue → allocation → admission and result-processing paths.
2. Create counters, latency histograms, backlog-age metrics, and structured reason-code logs.
3. Introduce delayed readiness or a failed result worker.
4. Use a trace/log correlation to identify the failing boundary and propose a targeted alert.

## Evidence required to pass

- A reviewer diagnoses the injected fault from supplied telemetry.
- Metrics avoid unbounded player/match labels.
- The alert has a threshold, window, and actionable runbook.
- Credentials and raw bearer tokens are absent from logs.

## Explain independently

Why can every individual service return successful HTTP responses while the player journey still fails?

## Reading and preparation

OTEL: logs, metrics, traces, context propagation; review A01 indicator definitions. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
