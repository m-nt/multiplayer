# I01 — Review and Redesign the Beginner Architecture

[Part overview](../README.md) · [Previous](../README.md) · [Next](../02-authority-models/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Keep the refactor small and behavior-preserving; do not introduce a framework-sized abstraction layer.

**Time:** 2 hours. **Prerequisite:** B12 plus the Go, HTTP, SQL, and cancellation bridge in the teaching guide.

**By the end:** Separate simulation, connection handling, and presentation while preserving the beginner match.

**Boundary:** Use boundaries where responsibilities change; avoid generic service layers for every class.

## Teaching notes

Network callbacks know connections and bytes. Simulation knows rules, ticks, and state. Presentation knows transforms and UI. If collision code directly writes a socket or a renderer directly awards points, these responsibilities cannot be tested independently.

Extract a step operation that consumes validated input and elapsed fixed-step time. Return or expose state suitable for snapshots. Keep serialization in an adapter. The same simulation fixture should run without a camera or connected player.

A refactor is justified by an observed coupling: server-only execution crashes because it touches a camera, or rule tests require a scene. Preserve serialized Unity references and behavior while extracting those seams.

## Worked example

Instead of OnMessage → transform.position → Send(), use OnMessage → bounded input queue; Step → authoritative state; snapshot adapter → Send. The rendering adapter consumes state separately.

## Guided lab

1. Draw current dependencies and select two problematic crossings.
2. Extract movement/scoring rules behind a plain step function.
3. Run the shared rule fixtures without rendering.
4. Repeat a two-client match and compare scores and state.

## Evidence required to pass

- Rule fixtures run without network I/O.
- A baseline match still completes after refactoring.
- A diagram identifies the owner and lifetime of each mutable state collection.

## Explain independently

When is an interface useful here, and when would a plain function be sufficient?

## Reading and preparation

Review the game contract and GO concurrency preparation. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
