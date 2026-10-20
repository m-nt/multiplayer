# B05 — Client-Server Interaction Patterns

[Part overview](../README.md) · [Previous](../04-state-and-synchronization/README.md) · [Next](../06-authority-ownership-trust/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Keep, and turn message diagrams into a small ready/start exchange with a rejected command.

**Time:** 2 hours. **Prerequisite:** B04 and its passing lab.

**By the end:** Implement request/result, command, and notification flows without treating RPC as an immediate local call.

**Boundary:** Use framework messages; durable retries and idempotency come later.

## Teaching notes

A request expects a corresponding response, so identify which request the response answers. A command expresses intent and may be rejected. A notification reports a fact to one or more clients. Continuous snapshots repeatedly communicate current state.

A remote method call does not execute synchronously in the sender's process. UI should distinguish requested from confirmed. A ready button can show pending until the server accepts it; starting the match locally before confirmation creates contradictory phases.

Broadcast only to relevant participants. Target a rejection to the requester. Do not send every response to every client merely because broadcasting is easy.

## Worked example

A sends Ready. Server validates A's membership and phase, marks A ready, sends A a result, and sends both clients an updated lobby state. A request during Finished is rejected without restarting the match.

## Guided lab

1. Define Ready, ReadyResult, MatchStarted, Input, and Snapshot directions.
2. Implement ready handling and a visible pending state.
3. Attempt ready from an invalid phase and return a reason.
4. Trace one successful and one rejected exchange.

## Evidence required to pass

- Both clients start only after the authoritative transition.
- A rejection leaves match state unchanged.
- The message diagram includes return paths and recipients.

## Explain independently

What could go wrong if a client interprets 'request sent' as 'action accepted'?

## Reading and preparation

MIRROR: remote actions, especially command direction and recipients. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
