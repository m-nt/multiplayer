# B07 — Time, Latency, and Real-Time Constraints

[Part overview](../README.md) · [Previous](../06-authority-ownership-trust/README.md) · [Next](../08-player-match-lifecycle/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Keep observation and simple interpolation; prediction and reconciliation are demonstrations only at this level.

**Time:** 2–3 hours. **Prerequisite:** B06 and its passing lab.

**By the end:** Measure RTT, distinguish tick/send/render rates, and explain the cost of buffering.

**Boundary:** Do not implement full prediction or clock synchronization.

## Teaching notes

RTT measures a round trip. Dividing it by two estimates one-way delay only under an assumption of roughly symmetric paths. Jitter is variation in arrival timing. Bandwidth is capacity or throughput, not a synonym for response time.

Simulation ticks advance rules; snapshot sends publish state; render frames draw pictures. These can run at different frequencies. Raising render FPS does not make remote information newer.

Interpolation draws between known samples, usually by rendering a little behind the newest server time. It smooths irregular arrivals at the cost of added visual delay. Extrapolation guesses beyond known samples and can be wrong. Prediction applies local controls before confirmation; reconciliation corrects the guess. Those algorithms require more state than a visual smoothing function.

## Worked example

At 20 snapshots/second, nominal spacing is 50 ms. A 100 ms render buffer can retain two intervals, but a longer arrival gap still empties it. Buffering trades freshness for smoother motion.

## Guided lab

1. Measure echo RTT with one local monotonic clock.
2. Apply the teaching guide's delay profile and compare raw snapping to a supplied interpolator.
3. Display simulation ticks, snapshot arrivals, and render frames separately.
4. Record what happens when arrivals stop longer than the buffer.

## Evidence required to pass

- Evidence includes baseline and impaired observations.
- The student explains added buffering delay.
- No prediction algorithm is claimed solely from smoothing a transform.

## Explain independently

Why can increasing the snapshot rate worsen a connection that is already congested?

## Reading and preparation

NETCODE: interpolation and prediction overview; read diagrams before implementation details. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
