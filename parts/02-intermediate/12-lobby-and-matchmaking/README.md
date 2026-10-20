# I12 — Lobby and Matchmaking Implementation

[Part overview](../README.md) · [Previous](../11-identity-backend-and-results/README.md) · [Next](../13-multiple-server-instances/README.md)

## Practicality and scope

Keep a single-worker two-player queue and fixed server list; avoid prematurely introducing distributed matchmaking.

**Time:** 3–4 hours. **Prerequisite:** I11 and its passing lab.

**By the end:** Create an eligible pair, assign one available server, and issue match-bound admission.

**Boundary:** One backend process owns the queue; distributed workers are advanced work.

## Teaching notes

A lobby is a social/readiness context. A queue searches for compatible participants. A match assignment binds chosen players to a particular server and match. These are separate states even when one process implements them.

Use one active queue ticket per player. Model Queued → Reserved → Assigned, with cancellation/expiry paths. Decide the cancellation race: once assignment commits, cancellation becomes a leave operation rather than deleting a queue row that is already consumed.

Do not hand out an address until the selected server is ready and reserved. Issue admission tickets after assignment. A client that receives no response can query its existing ticket instead of enqueueing again.

## Worked example

A retries queue entry after a timeout. The service returns A's existing ticket. When B arrives, exactly one assignment is created; A does not occupy both player slots.

## Guided lab

1. Implement enqueue, status, cancel, and assignment using one owner or transaction.
2. Use a fixed list of ready server endpoints.
3. Pair two distinct eligible players and issue I11 admission tickets.
4. Race cancellation against pairing and retry enqueue after a lost response.

## Evidence required to pass

- A player never appears twice in a match.
- A retry returns existing queue/assignment state.
- No server address is assigned before readiness and reservation.
- Cancellation has a deterministic documented outcome.

## Explain independently

Why should 'find a player pair' and 'reserve a server' be separate observable steps?

## Reading and preparation

Review I03 idempotency and I11 authorization; Agones allocation reading is optional preview. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
