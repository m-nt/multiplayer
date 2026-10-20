# B04 — Game State and Synchronization

[Part overview](../README.md) · [Previous](../03-messages-protocols-transports/README.md) · [Next](../05-interaction-patterns/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Keep, with a late-join experiment that exposes the difference between state and events.

**Time:** 2 hours. **Prerequisite:** B03 and its passing lab.

**By the end:** Build a minimal snapshot and initialize a joining client's view.

**Boundary:** Use complete small snapshots; delta compression is deferred.

## Teaching notes

State answers 'what is true now?' An event answers 'what happened?' Input expresses a player's control value; a command requests an action. A score is state; a point-scored sound is an event-driven presentation effect.

A snapshot is state sampled at a particular server tick. It needs stable object identities so a receiver can update existing objects instead of spawning new ones every update. Initial synchronization must initialize a client that missed earlier events.

Choose what to send from the gameplay contract: ball position/velocity, paddles, scores, phase, and tick. Camera shake and button highlight need not be authoritative. Sending only point-scored events cannot reconstruct a scoreboard for a late joiner without history or a snapshot.

## Worked example

A client joins at score 3–2. The current snapshot initializes 3–2 directly. Replaying five celebration events would incorrectly trigger old sounds and still needs an ordered history.

## Guided lab

1. Create a state table with writer, reader, units, and update policy.
2. Expose authoritative scores and positions through the framework.
3. Connect a fresh client after scoring several points.
4. Check that object identities remain stable across updates.

## Evidence required to pass

- A joining client displays current scores and match phase.
- Repeated snapshots do not create duplicate paddles.
- Local presentation values are separated from shared game state.

## Explain independently

Which should a reconnecting client receive first: old effects or current state, and why?

## Reading and preparation

MIRROR: synchronization overview and SyncVars as an implementation example. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
