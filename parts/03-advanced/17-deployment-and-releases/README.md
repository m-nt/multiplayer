# A17 — Deployment Automation and Release Management

[Part overview](../README.md) · [Previous](../16-profiling-and-optimization/README.md) · [Next](../18-platform-capstone/README.md)

## Practicality and scope

Keep a staged version rollout with compatibility and rollback evidence; avoid treating a green container rollout as a successful game release.

**Time:** 3–4 hours. **Prerequisite:** A16 and its passing lab.

**By the end:** Automate a reproducible release while routing compatible clients and draining old matches.

**Boundary:** A small staged lab rollout; no mandatory cloud vendor or commercial CI service.

## Teaching notes

A release identifies code, image digest, protocol version, configuration, and schema assumptions. Build once and promote the same artifact. Test rule fixtures, protocol fixtures, and lifecycle integration before publishing candidate capacity.

Compatibility is a routing/admission concern. Keep old and new fleet versions distinguishable, allocate a compatible one for each client, and reject unsupported versions clearly. Existing connections should remain on their original match unless a supported migration protocol exists.

Use expand/contract database changes: introduce compatible fields first, deploy readers/writers, then remove obsolete structures only after older consumers retire. Rolling back the binary cannot reverse arbitrary destructive schema changes.

Stage a small candidate capacity pool, run a synthetic match, inspect service indicators, then increase allocation share. Rollback stops new candidate assignments while draining its active matches according to policy.

## Worked example

Version 2 changes an input field incompatibly. A version-1 client must be routed to compatible capacity or receive an update-required response; silently connecting it to v2 is not a successful deployment.

## Guided lab

1. Automate build, fixtures, immutable image publication, and deployment configuration.
2. Deploy a candidate alongside the stable fleet and run a synthetic match.
3. Test old/new client compatibility and a deliberately incompatible client.
4. Trigger rollback while a candidate match is active and observe draining.
5. Document a compatible schema change and its rollback limits.

## Evidence required to pass

- Artifact identity is reproducible and no mutable tag is the only evidence.
- Unsupported clients receive explicit outcomes.
- Rollback preserves/drains active matches under the stated policy.
- Release gates include gameplay and result correctness, not only container health.

## Explain independently

Why can rolling back a server image fail to roll back the system?

## Reading and preparation

DOCKER: image/build practices; AGONES: fleet updates and canary testing; POSTGRES: schema compatibility considerations. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
