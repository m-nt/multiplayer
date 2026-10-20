# A12 — Persistent Data and Match-Result Integrity

[Part overview](../README.md) · [Previous](../11-backend-service-boundaries/README.md) · [Next](../13-observability/README.md)

## Practicality and scope

Deepen intermediate deduplication with durable handoff, conflict detection, and recovery across process loss.

**Time:** 4 hours. **Prerequisite:** A11 and its passing lab.

**By the end:** Recover committed final results through retries and worker crashes while identifying the remaining pre-commit loss window.

**Boundary:** No promise of recovering a match whose only final state died in volatile memory.

## Teaching notes

State the commit boundary: a result is durably accepted only when the backend transaction commits. Authenticate the submitting server and verify match, roster, and allocation generation. Use a unique match key and canonical payload/digest to distinguish an identical retry from a conflicting claim.

Commit result, intended progression operations, and outbox records atomically. A consumer records its deduplication/inbox key in the same transaction as its progression update. Acknowledging after commit permits redelivery; the inbox prevents repeating the effect. Avoid saying the network delivers 'exactly once' when the implementation actually tolerates repeated delivery.

A game server can retry until backend acceptance, but a crash before durable handoff can still lose the final result. Options include a durable local journal on persistent storage or earlier remote checkpoints; each changes operational cost and assumptions. The required policy marks an uncommitted crashed match failed/pending investigation rather than inventing a winner.

Audit reconciliation compares assignments, results, pending outbox records, and progression effects. Do not silently overwrite conflicting authoritative submissions.

## Worked example

A progression worker commits an increment and its inbox key, then crashes before acknowledging. The replay finds the inbox key and returns success without another increment. A different result for the same match is quarantined as a conflict.

## Guided lab

1. Add generation/roster checks and canonical result identity.
2. Test crashes before result commit, after result commit, before consumer commit, and after consumer commit before acknowledgement.
3. Restart workers and reconcile pending records.
4. Submit a conflicting result and verify explicit rejection/audit.

## Evidence required to pass

- Committed results eventually produce one logical progression effect after workers recover.
- No reward is minted from an unauthenticated client or stale server generation.
- The crash matrix explicitly labels the unrecoverable pre-handoff case.
- Conflict handling preserves evidence rather than overwriting history.

## Explain independently

Where exactly does your guarantee start, and what happens if the game server dies one instruction before that point?

## Reading and preparation

POSTGRES: transactions, unique constraints, isolation; review A11 outbox and I11 retry behavior. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
