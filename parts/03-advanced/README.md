# Advanced — Multiplayer Infrastructure and Operations

[Curriculum](../../README.md) · [Teaching guide](../../TEACHING-GUIDE.md) · [Review](../../CURRICULUM-REVIEW.md)

## Part practicality review

Practical as a measured small-platform course, not a promise to build a commercial global service in 36 hours. Budget 54–72 guided hours and 40–60 practice hours. Distributed-systems foundations precede allocator implementation. Use local failure injection and simulated regions; paid infrastructure is optional. The full path includes Kubernetes/Agones on a prepared cluster; a container-only alternative must be labeled a reduced track.

## Entry gate

I16 plus the Linux/container bridge and a prepared disposable cluster.

## Scope and teaching depth

Required for the full track: a small transactional allocator, concurrent queue workers, Docker, prepared Kubernetes/Agones, lifecycle reconciliation, durable result processing, telemetry, capacity evidence, and staged deployment.

Optional: paid multi-region deployment, server reuse, sophisticated autoscaling, external message broker, and live-state recovery. A container-only fallback is explicitly a reduced track.

Students defend invariants under concurrency and partial failure. Require recovery timelines, measured distributions, operational ownership, and explicit limits on guarantees.

## Lesson sequence

| ID | Lesson | Guided time | Practical outcome |
|---|---|---|---|
| A01 | [Production Multiplayer Architecture](01-production-architecture/README.md) | 2 hours | Define control-plane and gameplay responsibilities, ownership, and measurable service expectations. |
| A02 | [Distributed Systems Before Allocation](02-distributed-systems-and-failures/README.md) | 3–4 hours | State safety/liveness invariants and reproduce an ambiguous operation outcome. |
| A03 | [Matchmaking Architecture and Algorithms](03-matchmaking-algorithms/README.md) | 3–4 hours | Match eligible players once while balancing wait time, region latency, and bounded quality criteria. |
| A04 | [Server Allocation and Fleet Management](04-allocation-and-leases/README.md) | 4 hours | Reserve one ready server generation per match under concurrent requests and safely recover abandoned allocations. |
| A05 | [Containerization with Docker](05-docker-packaging/README.md) | 2–3 hours | Build a repeatable server image and run configured, resource-bounded instances. |
| A06 | [Container Orchestration](06-kubernetes-orchestration/README.md) | 3–4 hours | Explain reconciliation and distinguish scheduling, readiness, liveness, and gameplay continuity. |
| A07 | [Game-Specific Orchestration with Agones](07-agones-game-orchestration/README.md) | 4 hours | Integrate SDK readiness/health/shutdown and allocate a server from a small Fleet. |
| A08 | [Dynamic Allocation and End-to-End Match Lifecycle](08-end-to-end-match-lifecycle/README.md) | 4 hours | Reconcile queue, assignment, admission, result, and cleanup without losing or duplicating ownership. |
| A09 | [Multi-Region Infrastructure](09-multi-region/README.md) | 2–3 hours | Choose a region using all players' latency constraints and current capacity, and define outage policy. |
| A10 | [Scaling Strategies and Capacity Planning](10-capacity-and-scaling/README.md) | 3–4 hours | Estimate safe concurrent capacity and reserve enough headroom for measured startup delay. |
| A11 | [Backend Service Architecture](11-backend-service-boundaries/README.md) | 3 hours | Define transaction ownership and asynchronous handoff without dual-write loss. |
| A12 | [Persistent Data and Match-Result Integrity](12-result-integrity-and-recovery/README.md) | 4 hours | Recover committed final results through retries and worker crashes while identifying the remaining pre-commit loss window. |
| A13 | [Observability and Production Diagnostics](13-observability/README.md) | 3 hours | Trace a failed assignment and measure user-visible service health without unbounded telemetry cardinality. |
| A14 | [Reliability and Incident Handling](14-reliability-and-incidents/README.md) | 3–4 hours | Handle process loss and dependency overload under a documented player-facing policy. |
| A15 | [Security at Infrastructure Scale](15-infrastructure-security/README.md) | 2–3 hours | Limit service privileges and protect administrative/allocation paths from game clients. |
| A16 | [Performance Profiling and Optimization](16-profiling-and-optimization/README.md) | 3–4 hours | Identify the dominant bottleneck under a reproducible workload and validate a change against correctness. |
| A17 | [Deployment Automation and Release Management](17-deployment-and-releases/README.md) | 3–4 hours | Automate a reproducible release while routing compatible clients and draining old matches. |
| A18 | [Advanced Platform Capstone](18-platform-capstone/README.md) | 4–6 guided hours plus team project practice | Operate a small multiplayer platform and defend its safety, recovery, capacity, and release decisions. |

## Project checkpoints

- **A01–A04:** Define invariants, understand partial failure, and build concurrent matchmaking/allocation.
- **A05–A08:** Package, orchestrate, integrate Agones, and reconcile the whole lifecycle.
- **A09–A12:** Model regions/capacity and make durable processing recoverable.
- **A13–A16:** Diagnose, handle incidents, restrict privileges, and profile bottlenecks.
- **A17–A18:** Release compatible versions and demonstrate the operated platform.

## Completion gate

A small fleet accepts queue requests, allocates exclusive capacity, admits players, stores results durably, and cleans up. Students demonstrate concurrency safety, bounded recovery, capacity measurements, and a compatible staged release.

Use the final lesson's mandatory evidence checks and the shared assessment rubric. Complete missing preparation before continuing; do not compensate for a missing implementation with a diagram alone.
