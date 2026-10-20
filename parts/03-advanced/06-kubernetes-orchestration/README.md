# A06 — Container Orchestration

[Part overview](../README.md) · [Previous](../05-docker-packaging/README.md) · [Next](../07-agones-game-orchestration/README.md)

## Practicality and scope

Keep on a prepared cluster; installation and cloud procurement are not hidden lesson tasks.

**Time:** 3–4 hours. **Prerequisite:** A05 and its passing lab.

**By the end:** Explain reconciliation and distinguish scheduling, readiness, liveness, and gameplay continuity.

**Boundary:** Small disposable workloads; do not use a generic rolling Deployment as proof of live-match preservation.

## Teaching notes

An orchestrator compares desired state with observed resources and schedules/restarts workloads. Resource requests affect placement; limits constrain consumption. A replacement process restores capacity but does not reconstruct the killed match's memory.

Startup probes accommodate initialization; readiness controls whether traffic should be directed to a workload through supporting routing mechanisms; liveness can trigger restart. A ready HTTP handler does not prove the simulation advances. Design each probe around a specific failure and avoid a liveness check that restarts every server during a shared database outage.

Discovery and routing need to preserve per-match affinity. Randomly routing every gameplay connection to any replica can connect a player to the wrong state owner. This is why the next lesson adds game-specific allocation.

## Worked example

A container answers HTTP health requests while its tick counter is frozen. A process-alive check passes, but gameplay health fails. The repair policy must account for the match that restart will destroy.

## Guided lab

1. Deploy a small backend service and one disposable game-server workload.
2. Configure startup/readiness/liveness with distinct purposes.
3. Fail readiness, then crash a process, and observe the different outcomes.
4. Delete a game-server instance and demonstrate that replacement does not resume its match.

## Evidence required to pass

- Probe failures have the expected routing/restart consequences.
- Students can identify requests versus limits and pending scheduling.
- No claim is made that recreation restores gameplay state.
- The endpoint-routing plan preserves match identity.

## Explain independently

Why can a database-dependent liveness probe turn a temporary backend outage into a fleet-wide restart?

## Reading and preparation

KUBERNETES: probes, resource management, workload lifecycle. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
