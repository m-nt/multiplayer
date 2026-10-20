# I03 — Interaction Patterns, Acknowledgements, and Idempotency

[Part overview](../README.md) · [Previous](../02-authority-models/README.md) · [Next](../04-transport-workshops/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Keep, but distinguish application completion from transport acknowledgement and add a lost-response experiment.

**Time:** 2 hours. **Prerequisite:** I02 and its passing lab.

**By the end:** Implement a retriable command whose repeated request does not repeat its effect.

**Boundary:** Use an in-memory deduplication store; durable deduplication arrives in I11.

## Teaching notes

A transport acknowledgement concerns delivery at a transport boundary. An application result states whether a rule executed. A database commit is another boundary. A timeout means the caller did not observe an answer; it does not establish that the operation never happened.

An idempotency key identifies one logical operation across retries. Store the result under a key scoped to the authenticated caller and operation. Reuse of a key with a different payload is a conflict, not permission to reinterpret the first request.

Retention is part of the contract. An in-memory cache loses history on restart and cannot guarantee durable one-time effects. Bound its size and document the retry window.

## Worked example

Ready(request-42) succeeds, but its response is dropped. Repeating request-42 returns the same accepted result without emitting another MatchStarted event.

## Guided lab

1. Add request IDs and explicit accepted/rejected responses.
2. Cache a bounded set of command outcomes for the current session.
3. Drop the first response after applying Ready and retry with the same ID.
4. Repeat with a changed payload and document the conflict.

## Evidence required to pass

- The action occurs once during a response-loss retry.
- A mismatched retry is rejected.
- The student states what breaks when the in-memory cache is lost.

## Explain independently

Why is generating a new request ID for every retry dangerous for a purchase or reward?

## Reading and preparation

POSTGRES: unique constraints and transactions preview; revisit durability in I11. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
