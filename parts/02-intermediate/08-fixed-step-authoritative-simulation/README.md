# I08 — Simulation Ticks and Authoritative State

[Part overview](../README.md) · [Previous](../07-go-server-and-client-adapter/README.md) · [Next](../09-prediction-and-reconciliation/README.md)

## Practicality and scope

Keep, but define input scheduling and overload policy rather than showing a loop that may block on I/O.

**Time:** 3–4 hours. **Prerequisite:** I07 and its passing lab.

**By the end:** Advance complete Pong rules at a fixed step independently of rendering and socket speed.

**Boundary:** Simple reproducible kinematics; no cross-engine determinism claim.

## Teaching notes

Use monotonic elapsed time to schedule fixed steps. At 60 Hz the nominal step budget is about 16.67 ms. A timer tick is a scheduling signal, not proof that precisely that duration elapsed. Cap catch-up work and record overload instead of entering an unlimited catch-up spiral.

For the required prediction-compatible path, inputs carry a session and a sequential input number; each represents one fixed-duration control sample. Process at most one sample per player per server tick in order. Bound queue depth and lead; reject floods. If no sample is available, apply neutral input and leave lastProcessed unchanged. This simple policy may pause under jitter; more advanced timed-input scheduling is optional.

Define an order: validate queued input, move paddles, advance ball/collisions, award points, update phase, produce snapshots. Send at an independent cadence. An input acknowledgement identifies the last sample actually simulated, not merely received.

## Worked example

At tick 120, process sample 80, clamp the paddle to court bounds, advance the ball, and publish lastProcessed=80. A network reader receiving sample 81 must not change that acknowledgement before tick 121 applies it.

## Guided lab

1. Port the shared rule fixtures to Go and match expected values within stated tolerance.
2. Implement bounded fixed-step scheduling and ordered one-sample-per-tick input consumption.
3. Run at different client render rates while retaining server timing.
4. Inject a slow send and a temporary simulation stall; record queue and catch-up behavior.

## Evidence required to pass

- Score changes exactly once per goal fixture.
- Socket delays do not execute inside the simulation step.
- Input floods cannot create extra simulation time or speed.
- Overload behavior is bounded and observable.

## Explain independently

Why would advancing a paddle once for every received packet let a fast sender move faster?

## Reading and preparation

NETCODE: fixed-step and state synchronization concepts; GO: time and monotonic-clock documentation. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
