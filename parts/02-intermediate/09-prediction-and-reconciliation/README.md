# I09 — Interpolation, Prediction, and Reconciliation

[Part overview](../README.md) · [Previous](../08-fixed-step-authoritative-simulation/README.md) · [Next](../10-network-budgets-and-backpressure/README.md)

## Practicality and scope

Keep as a multi-block algorithm lab; restrict required prediction to a paddle with the shared fixed-step rule.

**Time:** 4–6 hours. **Prerequisite:** I08 and its passing lab.

**By the end:** Predict the local paddle, reconcile acknowledged input, and interpolate remote state with measurable trade-offs.

**Boundary:** Do not predict authoritative scores or the ball's full collision history. Shooter rewind is an optional extension.

## Teaching notes

### Remote objects: interpolation

Keep snapshots ordered by server tick. Estimate a render time behind the latest authoritative timeline and find two surrounding samples. Interpolate with alpha = (renderTime - olderTime) / (newerTime - olderTime), clamped to the supported interval. On buffer underrun, choose a bounded hold/extrapolation policy and record it. Teleports and round resets require a discontinuity rule, not a long interpolation through the court.

### Local paddle: prediction

Generate input samples at the agreed fixed rate, assign a sequence, store them in bounded history, send them, and apply the same clamp/movement function locally. Rendering may run faster, but must not create extra control samples. The server's I08 scheduler applies one sample per tick, independently validates speed, and acknowledges only processed samples.

### Reconciliation

When a newer snapshot for the current session arrives: set the prediction state to the authoritative paddle position; discard history through lastProcessed; replay remaining samples in sequence using the same fixed duration; then optionally ease the displayed transform toward that corrected prediction. Keep visual smoothing separate from the logical predicted state. An acknowledgement beyond sent history is invalid; a correction older than retained history requires a full resync instead of unbounded replay.

Network delay can make server tick and input sequence differ. Do not treat them as the same counter or infer sample duration from packet-arrival gaps. Bound pending history and reconnect/resync after a prolonged stall.

### Optional shooter extension

Using a separate supplied hitbox harness, retain bounded server history and evaluate a shot at a validated, clamped historical time. Compare a rewound hit against current state; restore state after the query. Untrusted timestamps cannot choose arbitrary history. Discuss cover fairness and CPU/memory cost. This is an extension, not a hidden prerequisite for the infrastructure course.

## Worked example

Snapshot says position 4.0 and lastProcessed=40. Stored samples 41 and 42 each move +0.1 court units, so replay yields 4.2 before court clamping. Starting replay from the old predicted position would apply movement twice.

## Guided lab

1. Block 1: add tick-ordered remote interpolation and expose buffer age/underruns.
2. Block 2: store and predict fixed-rate local input, then implement authoritative reset plus replay.
3. Apply delay/jitter and force a server correction at the court edge.
4. Test stale snapshots, session reset, missing retained history, and delayed acknowledgements.
5. Compare input-to-display delay, correction magnitude, and visible jitter against server-only rendering.

## Evidence required to pass

- The numeric replay example passes, including the court-edge clamp.
- Old or wrong-session snapshots cannot rewind current state.
- History remains bounded and resync works after a prolonged stall.
- Measurements distinguish responsiveness from authoritative correctness.

## Explain independently

Why can smoothing a visual correction hide a disagreement without actually repairing the predicted simulation?

## Reading and preparation

NETCODE: snapshot interpolation and client prediction; work through the numeric example before reading further. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
