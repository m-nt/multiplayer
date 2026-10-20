# I13 — Run Multiple Game Server Instances

[Part overview](../README.md) · [Previous](../12-lobby-and-matchmaking/README.md) · [Next](../14-linux-deployment/README.md)

## Practicality and scope

Keep manual process management as a concrete precursor to orchestration.

**Time:** 2–3 hours. **Prerequisite:** I12 and its passing lab.

**By the end:** Run isolated matches on different configured endpoints and track usable capacity.

**Boundary:** One match per process; do not multiplex many matches inside one process yet.

## Teaching notes

A server instance needs a unique identity beyond its port. A restarted process can reuse a port but must not inherit stale allocation or credentials. Track process generation, match ID, endpoint, and readiness.

Port allocation is not match allocation. A free port does not imply enough CPU, and a live listener may already be running a match. Capacity counts only ready, unreserved instances under this model.

Health checks detect symptoms, not certainty about all gameplay behavior. A heartbeat worker can run while the simulation is stuck, so include tick progress in health evidence.

## Worked example

Processes on ports 7001 and 7002 host matches m1 and m2. Killing 7001 must not erase m2's membership. Restarted 7001 gets a new instance identity before it may accept another assignment.

## Guided lab

1. Launch three instances with unique ports/IDs and explicit logs.
2. Run two concurrent two-player matches; leave one instance ready.
3. Terminate one active process and inspect the other match.
4. Restart it and verify stale match admission cannot reuse the new instance.

## Evidence required to pass

- Matches have independent scores, membership, and lifecycle.
- Capacity reports distinguish ready from running.
- A restarted endpoint does not accept an old assignment.

## Explain independently

Why is using only IP:port as the permanent identity of a server unsafe after restart?

## Reading and preparation

GO-NET: listening and address configuration; review I06 process lifecycle. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
