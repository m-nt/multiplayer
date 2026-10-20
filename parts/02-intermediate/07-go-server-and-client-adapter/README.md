# I07 — Build an Independent Go Server

[Part overview](../README.md) · [Previous](../06-dedicated-server-process/README.md) · [Next](../08-fixed-step-authoritative-simulation/README.md)

## Practicality and scope

Keep as a small cross-language server, with the language bridge mandatory and full ball simulation deferred to I08.

**Time:** 4–6 hours. **Prerequisite:** I06 and its passing lab.

**By the end:** Connect Unity presentation to a Go WebSocket server that owns paddle positions.

**Boundary:** Use simple kinematics; do not port Unity engine physics or preserve Mirror's wire protocol.

## Teaching notes

Separate connection readers, simulation ownership, and writers. Readers decode and enqueue bounded inputs. One simulation owner mutates each match's state. Writers send immutable snapshots through bounded per-client queues so a slow socket cannot stall everyone.

Cancellation must stop readers, writers, and timers and release the connection's match membership. Define who closes each channel; having every worker close the same channel is not a cleanup policy. Follow the selected WebSocket library's concurrency contract, commonly using one serialized writer.

Unity presentation should depend on a state/input adapter rather than on the server language. Replace Mirror-specific message calls for this branch. Work from the protocol fixtures, not from assuming JSON field casing and integer types will align automatically.

## Worked example

A reader accepts axis -1 into a queue of fixed capacity. The simulation consumes validated intent and emits paddle positions. If one client's outgoing queue fills, replace stale unsent snapshots or disconnect it under the declared policy; never block the match owner.

## Guided lab

1. Implement listener, admission stub, bounded input queue, and session cleanup.
2. Add one simulation owner and a position-only step.
3. Replace the Unity networking adapter with the custom WebSocket protocol.
4. Connect two clients, then stop reading from one test client.
5. Run relevant Go tests with the race detector under connect/disconnect load.

## Evidence required to pass

- Two clients see server-owned paddle movement.
- A slow reader cannot block the other client or grow memory without bound.
- Workers terminate after disconnect and repeated session churn.
- The report distinguishes race-detector coverage from a proof of all possible schedules.

## Explain independently

Why is putting a mutex around every map insufficient if a socket write holds that mutex?

## Reading and preparation

GO and GO-RACE: goroutines, cancellation, shared-state races; selected WebSocket library concurrency documentation. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
