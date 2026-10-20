# B08 — Player and Match Lifecycle

[Part overview](../README.md) · [Previous](../07-time-latency-realtime/README.md) · [Next](../09-server-versus-backend/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Keep, with a finite-state table and explicit cleanup tests.

**Time:** 2 hours. **Prerequisite:** B07 and its passing lab.

**By the end:** Implement join, ready, play, finish, and disconnect behavior with a documented late-join policy.

**Boundary:** Reject late joins during play in the required lab; secure reconnect resumes in intermediate.

## Teaching notes

A connected socket is not necessarily a player admitted to a match. Separate connection, membership, and spawned object. The beginner project can use temporary session identity without pretending it is an account.

Represent the match as Waiting → Starting → Playing → Finished → Cleaned. For every transition define trigger, guard, owner, and cleanup. Cleanup should tolerate being called more than once because disconnect and shutdown paths can overlap.

Choose a policy for loss of a participant. This lab ends the match with a disconnected outcome and frees both slots. Rejoining starts a new session; it does not silently inherit another player's object. A lobby coordinates readiness; the live match advances gameplay.

## Worked example

Player B leaves during Starting. The server cancels the start, releases B's slot, and returns A to Waiting. It must not start a one-player match from a previously scheduled callback.

## Guided lab

1. Write a transition table including disconnect in every phase.
2. Implement capacity two and reject a third player.
3. Cancel pending start work when membership changes.
4. Connect/disconnect repeatedly and inspect objects and slots.

## Evidence required to pass

- No orphan paddle or occupied slot remains after cleanup.
- A stale start callback cannot move the match into Playing.
- Late-join and disconnect policies are visible to both clients.

## Explain independently

Why should cleanup be safe when both a timeout and a disconnect callback request it?

## Reading and preparation

MIRROR: connection and object lifecycle sections. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
