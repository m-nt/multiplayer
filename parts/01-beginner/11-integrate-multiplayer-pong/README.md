# B11 — Integrate the Multiplayer Pong Game

[Part overview](../README.md) · [Previous](../10-performance-reliability-basics/README.md) · [Next](../12-test-debug-review/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Make this an integration checkpoint for previous increments, not the first networking implementation.

**Time:** 2–3 hours plus project practice. **Prerequisite:** B10 and its passing lab.

**By the end:** Complete a two-player match through authoritative rules and synchronized presentation.

**Boundary:** Keep existing mechanics; omit accounts, matchmaking services, and content expansion.

## Teaching notes

Integration checks the whole path: connection → admitted player → input → tick → snapshot → rendering → result → cleanup. A locally correct component can fail when wired to a different lifetime or identity.

The server runs the only authoritative ball and collision simulation. Client ball visuals follow snapshots; they must not award points. Inputs target the sender's paddle. Match start resets all state consistently, including old inputs.

Host mode can hide errors because client and server share a process. Test a separate server and clients before declaring success. A graphical server-only process is acceptable here if the framework supports it; a deployable headless build is taught later.

## Worked example

After a point, a leftover 'up' input from the previous round must not move a newly assigned player. Reset or scope input state to the current round/session before resuming.

## Guided lab

1. Connect the completed lifecycle, authority, snapshots, and UI.
2. Run two standalone clients against a separate server process.
3. Play to the win condition, restart, and repeat.
4. Disconnect one participant during play and verify cleanup.

## Evidence required to pass

- Only server collision logic changes the score.
- Both clients agree on final scores and outcome.
- A restart begins with fresh state and no orphan objects.

## Explain independently

Which bug could pass host testing but fail when the server has no local client?

## Reading and preparation

Review B04–B10 notes and the framework lifecycle material. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
