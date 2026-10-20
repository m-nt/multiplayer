# I02 — Authority Models in Depth

[Part overview](../README.md) · [Previous](../01-review-and-boundaries/README.md) · [Next](../03-interaction-and-idempotency/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Deepen the beginner authority lesson with connection replacement and ownership transitions rather than repeating only the score exploit.

**Time:** 2 hours. **Prerequisite:** I01 and its passing lab.

**By the end:** Validate every action against identity, membership, ownership, phase, and current session.

**Boundary:** Implement server authority; discuss distributed authority without building consensus.

## Teaching notes

Authority is an operation-level decision. A server may own damage while a client owns camera movement. Ownership can change over time, so validation must use current state when applying an action, not only when the packet arrives.

Reconnect creates a new connection generation. Pending input from an old generation must not affect the new session. Scope commands to match and session; resolve the player from authenticated connection state once identity is introduced. In this lesson use explicit local test identities, not a claimed secure login.

A listen host can have latency and trust advantages even when the code calls its simulation 'server authoritative.' A dedicated server removes participant control over that process but still needs correct validation.

## Worked example

Connection A owns paddle 1 at generation 7. After reconnect, generation 8 is active. A queued generation-7 input is rejected even if it names paddle 1 correctly.

## Guided lab

1. Write an operation permission table for input, ready, reconnect, score, and cosmetics.
2. Add session-generation checks at input application.
3. Queue input, replace the connection, then deliver the stale input.
4. Try a command targeting the other player's paddle.

## Evidence required to pass

- Old-session and cross-player commands are rejected.
- A legitimate new-session input succeeds.
- State mutation paths share the same authoritative checks.

## Explain independently

Why can a command be valid when received but invalid when executed?

## Reading and preparation

MIRROR: authority transfer as a comparison; apply the principle independently of its API. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
