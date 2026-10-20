# B10 — Performance and Reliability Fundamentals

[Part overview](../README.md) · [Previous](../09-server-versus-backend/README.md) · [Next](../11-integrate-multiplayer-pong/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Replace classification-only work with byte and frequency measurement.

**Time:** 2 hours. **Prerequisite:** B09 and its passing lab.

**By the end:** Calculate a simple traffic budget and choose policies for state freshness and important events.

**Boundary:** Use the existing transport; do not implement reliable UDP.

## Teaching notes

A fresh paddle snapshot can replace an older unsent snapshot. A match result needs confirmed application handling. Transport delivery does not prove that a database committed a result.

Ordering, delivery, and duplicate protection are separate requirements. An old state update should not overwrite a newer tick. A repeated event should not award the same point twice. With TCP, packet loss may show up as a delay in application delivery rather than a missing message.

Estimate traffic from serialized bytes times sends per second times recipients. Distinguish payload bytes from actual wire bytes, which include protocol overhead and retransmissions. Measure queues too: a growing queue makes state increasingly stale.

## Worked example

An 80-byte snapshot × 20 sends/second × 2 clients is 3,200 payload bytes/second of server egress. It is not a complete network bill or wire-throughput measurement.

## Guided lab

1. Count snapshots and serialized bytes over a 60-second run.
2. Compare the measurement with the payload calculation.
3. Reduce the send rate and observe smoothness using the same delay profile.
4. Define handling for stale snapshots, repeated events, and a timed-out result.

## Evidence required to pass

- The report labels payload versus wire measurement.
- It records the trade-off rather than declaring lower traffic universally better.
- A stale tick cannot move displayed state backward.

## Explain independently

Why is 'use reliable delivery for everything' not a complete reliability design?

## Reading and preparation

TCP: reliability overview; NETCODE: snapshot interpolation trade-offs. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
