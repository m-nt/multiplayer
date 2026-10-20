# A15 — Security at Infrastructure Scale

[Part overview](../README.md) · [Previous](../14-reliability-and-incidents/README.md) · [Next](../16-profiling-and-optimization/README.md)

## Practicality and scope

Keep a scoped configuration review with the security instructor; demonstrate denied access, not just a checklist.

**Time:** 2–3 hours. **Prerequisite:** A14 and its passing lab.

**By the end:** Limit service privileges and protect administrative/allocation paths from game clients.

**Boundary:** No offensive activity outside disposable authorized environments.

## Teaching notes

Separate player credentials, game-server identity, allocator identity, and operator access. A game server should submit its own result, not modify all accounts or allocate unlimited infrastructure. A pod's service account should have only the permissions needed for its actual API operations.

Network segmentation reduces reachable surfaces but does not replace authentication. Protect allocation/admin endpoints independently of gameplay ports. Secrets belong in a managed delivery mechanism with access controls, rotation, and log redaction, not image layers or public manifests.

Dependency pinning and image scanning expose known issues; they do not prove an application secure. Resource limits and quotas contain some exhaustion, while admission rate limits and bounded parsing protect application work. Test secret rotation with overlapping validity if active matches require continuity.

## Worked example

A compromised player connection attempts the allocator endpoint using its admission ticket. Network policy may block the path; if reachable, service authentication must still reject that player credential.

## Guided lab

1. Draw player, server, allocator, database, and operator trust boundaries.
2. Remove a deliberately overbroad lab service permission.
3. Attempt cross-match result submission and player access to allocation.
4. Rotate a development service credential and inspect active-match behavior and logs.

## Evidence required to pass

- Unauthorized operations demonstrably fail.
- A server cannot submit another allocation generation's result.
- Secrets are absent from images/config examples/log evidence.
- The least-privilege change preserves legitimate lifecycle operations.

## Explain independently

Why should a firewall rule and service-level authorization both exist on the allocator path?

## Reading and preparation

KUBERNETES security/RBAC documentation and AGONES service-account guidance for the pinned environment. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
