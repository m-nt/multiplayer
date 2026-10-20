# B01 — What Makes a Game Multiplayer?

[Part overview](../README.md) · [Previous](../README.md) · [Next](../02-architectures-and-terminology/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Keep, but replace a diagram-only introduction with observation of a working two-process demo.

**Time:** 2 hours. **Prerequisite:** The local-game build and logging entry check in the teaching guide.

**By the end:** Trace one paddle input through the server to both displays and identify the authoritative state.

**Boundary:** Observe the supplied network plumbing; do not implement sockets.

## Teaching notes

A local game keeps state in one process. Two copies have different memory and receive input at different times; running the same code does not make their states agree. Networking must communicate enough information to coordinate those copies.

A client collects input and presents the game. A server is a process with assigned responsibilities, not necessarily a separate physical machine. In this project the server advances the ball and decides the score. Clients hold replicas for display. Authority means the right to decide; synchronization means distributing the decision.

Teach the distinction by pausing one client. Its picture stops, but the authoritative game can continue. A disconnected display is not evidence that the whole match stopped.

## Worked example

Player A presses up. A sends an input intention. The server advances A's paddle on its next tick and sends state to A and B. Label each arrow with what crosses the process boundary; do not draw a shared-memory arrow.

## Guided lab

1. Run the supplied host and a separate client; identify their process IDs and logs.
2. Trace one input and one state update using a shared sequence label.
3. Pause or stop one client and observe the other process.
4. Draw the actual path and mark where ball, score, and input live.

## Evidence required to pass

- A log excerpt connects an input to a server update and client display.
- The diagram distinguishes a client replica from authoritative state.
- Student explains why two local games alone are not synchronized.

## Explain independently

If both clients can calculate a collision, why does this project still choose one authority?

## Reading and preparation

MIRROR: inspect the authority guide's introductory distinction. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
