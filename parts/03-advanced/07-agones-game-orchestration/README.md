# A07 — Game-Specific Orchestration with Agones

[Part overview](../README.md) · [Previous](../06-kubernetes-orchestration/README.md) · [Next](../08-end-to-end-match-lifecycle/README.md)

## Practicality and scope

Keep as a managed comparison after students implement the small allocator; use only the one-match-per-server lifecycle.

**Time:** 4 hours. **Prerequisite:** A06 and its passing lab.

**By the end:** Integrate SDK readiness/health/shutdown and allocate a server from a small Fleet.

**Boundary:** No multi-match reuse, advanced counters/lists, or production cluster administration in the required lab.

## Teaching notes

A GameServer resource describes a game process and its lifecycle. A Fleet manages a group. Connect the server SDK and signal readiness only after initialization. Allocation selects eligible capacity and returns an endpoint; it does not authenticate players or choose balanced teams.

For the required warm-pool flow, initialize → Ready → allocate → admit assigned players → run → submit result → Shutdown. Keep game admission disabled for unrelated players even when the platform reports Ready or Allocated. SDK health should reflect the intended server-health policy.

Atomic capacity selection and application-level idempotency are different boundaries. If the allocation response disappears, blindly repeating the external allocation operation can reserve additional capacity. Keep the A04 assignment/request record, attach correlation metadata, reconcile ambiguous outcomes, and recover or retire surplus reservations before issuing tickets.

Pin the cluster/Agones/SDK versions together and use their matching documentation. Local networking must make the returned address reachable from the client; an internal address is not automatically public.

## Worked example

One Fleet has two Ready servers. Allocate a match, verify the selected resource moves to Allocated, and bind that resource/instance identity to the assignment before returning an admission ticket.

## Guided lab

1. Integrate the selected Go SDK and run the local development mode first.
2. Deploy a two-server Fleet and inspect readiness and health transitions.
3. Allocate one server, bind the assignment, and connect two authorized clients.
4. Finish and shut down; observe replacement ready capacity.
5. Inject a lost allocator response and recover through the assignment/reconciliation layer.

## Evidence required to pass

- SDK readiness is not emitted before the listener/game initialization completes.
- One assignment identifies one allocated resource and authorized roster.
- Lost responses do not result in multiple player-visible assignments.
- Completion frees/replaces capacity under the one-match-per-process policy.

## Explain independently

Which responsibilities remain yours after adopting Agones allocation?

## Reading and preparation

AGONES-SDK, AGONES-SERVER, and AGONES-ALLOCATION: core lifecycle and default Ready allocation. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
