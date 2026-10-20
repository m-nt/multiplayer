# B06 — Authority, Ownership, and Trust

[Part overview](../README.md) · [Previous](../05-interaction-patterns/README.md) · [Next](../07-time-latency-realtime/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Keep the score exploit in an isolated lab; require a before/after test instead of just a security discussion.

**Time:** 2 hours. **Prerequisite:** B05 and its passing lab.

**By the end:** Remove client score authority and validate movement intent at the server.

**Boundary:** Teach trust boundaries, not a full anti-cheat product.

## Teaching notes

Object ownership associates an object with a connection; it does not authorize every operation on that object. Input authority lets a player submit controls. State authority decides the resulting position, collision, or score.

A client can change its own code. Disabling a UI button therefore cannot protect the server. Derive the player from the connection, validate the match phase and input range, and let the server apply the rules. Clients never report the score as truth.

Keep validation on the execution path that actually mutates state. A helper that is never called provides no protection. Avoid accepting a player ID from the payload when the server already knows the sender.

## Worked example

The insecure message SetScore(999) visibly changes the match. Replace it with input intent; a server-observed ball crossing the goal is the only scoring path.

## Guided lab

1. Use the supplied insecure branch to submit a forged score.
2. List every state-changing entry point and its authority.
3. Remove score-setting messages; validate finite input in [-1, 1].
4. Attempt a forged score and another player's movement again.

## Evidence required to pass

- Forged score and cross-player movement leave authoritative state unchanged.
- A valid input still moves the correct paddle.
- The authority table identifies who can request and who can decide each action.

## Explain independently

Does owning your paddle imply permission to set its speed to any value?

## Reading and preparation

MIRROR: authority and command checks. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
