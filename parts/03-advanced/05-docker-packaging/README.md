# A05 — Containerization with Docker

[Part overview](../README.md) · [Previous](../04-allocation-and-leases/README.md) · [Next](../06-kubernetes-orchestration/README.md)

## Practicality and scope

Keep a practical packaging lab, distinct from matchmaking or orchestration.

**Time:** 2–3 hours. **Prerequisite:** A04 and its passing lab.

**By the end:** Build a repeatable server image and run configured, resource-bounded instances.

**Boundary:** Package the existing Go server; Unity image construction is an optional comparison.

## Teaching notes

An image is a packaged filesystem/configuration template; a container is a running instance. Separate compilation tools from runtime files with a multi-stage build. Record the toolchain and base image identity so the build is reproducible enough for review.

Pass per-instance configuration at runtime. Do not bake service credentials into image layers. Run under an appropriate non-root identity and make writable paths explicit. Container networking still needs the correct published port and address; a Dockerfile port declaration alone does not expose a service externally.

Signal delivery matters: the server must receive termination and execute its bounded shutdown policy. Logs should reach the platform collector, and durable results must live outside ephemeral container filesystems. Resource limits can expose overload but cannot fix slow simulation.

## Worked example

Three containers share one image but have different host ports and instance IDs. Replacing a container discards its private writable layer, so a result stored only there is not durable platform storage.

## Guided lab

1. Create a multi-stage build and record the resulting image digest.
2. Run two instances with different configuration and resource limits.
3. Send termination and observe graceful cleanup versus forced kill.
4. Recreate a container and show which data survives in the backend.

## Evidence required to pass

- The runtime image contains no build-time secrets.
- Two clients reach the intended configured container endpoint.
- Shutdown and non-root execution work with required writable paths.
- Container-local data is not mislabeled as durable result storage.

## Explain independently

Why does putting a server in Docker not automatically provide safe match allocation?

## Reading and preparation

DOCKER: multi-stage builds; official runtime networking and resource-limit sections for the chosen engine. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
