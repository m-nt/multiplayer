# B09 — Game Server versus Backend Services

[Part overview](../README.md) · [Previous](../08-player-match-lifecycle/README.md) · [Next](../10-performance-reliability-basics/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Keep as a small integration seam, not a requirement to build accounts or databases.

**Time:** 2 hours. **Prerequisite:** B08 and its passing lab.

**By the end:** Place live simulation and durable operations correctly and isolate a result-submission boundary.

**Boundary:** Use a local fake backend; no credentials, database, or real rewards.

## Teaching notes

The game server owns the active match's fast-changing state. A backend can own accounts and durable match records. These are logical responsibilities; one machine may host both without combining their lifetimes.

A database write should not sit inside every simulation tick. Produce a final result after the authoritative match finishes and hand it to a separate operation. That operation may fail even though the game ended correctly.

Persistence survives process loss; an in-memory dictionary does not. A local fake backend illustrates a boundary but cannot establish durability. Authentication will establish who a player is; authorization will decide which match they may join.

## Worked example

The score changes in server memory during play. At Finished, the server produces MatchResult(matchId, players, scores, outcome). A fake receiver can accept or fail it without controlling ball movement.

## Guided lab

1. Assign movement, accounts, matchmaking, scores, and result history to components.
2. Add a result sink interface or simple callback at match completion.
3. Replace the sink with one that delays or fails.
4. Show a result-pending status without blocking simulation or UI.

## Evidence required to pass

- No result submission occurs every frame.
- A fake backend failure is reported distinctly from a gameplay failure.
- Students label fake storage as non-durable.

## Explain independently

Why should a client not submit its own claimed final score to a rewards service?

## Reading and preparation

POSTGRES: introductory transaction purpose only; implementation comes in I11. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
