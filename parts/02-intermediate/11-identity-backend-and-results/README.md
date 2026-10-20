# I11 — Backend Identity and Durable Match Results

[Part overview](../README.md) · [Previous](../10-network-budgets-and-backpressure/README.md) · [Next](../12-lobby-and-matchmaking/README.md)

## Practicality and scope

Expand the original identity lesson to teach the persistence required by the capstone. Use two blocks: admission, then transactional results.

**Time:** 4–6 hours. **Prerequisite:** I10 and its passing lab.

**By the end:** Bind a connection to an authorized player and persist one authoritative result despite retries.

**Boundary:** Use an instructor-provided development identity issuer; no password storage or production identity-provider implementation.

## Teaching notes

Authentication establishes an identity; authorization permits participation in a specific match. A development issuer produces a short-lived opaque ticket associated with player, match, expiry, and intended server/allocation. The game server verifies it through the backend and consumes it atomically. Record a connection generation on admission. A new reconnect ticket follows policy rather than reusing a consumed one.

TLS protects credentials in transit. Do not log bearer tickets. Game-server service credentials are separate from player tickets and authorize result submission only for assigned matches. A random match ID is an identifier, not an access control mechanism.

For persistence, create a result table with a unique match ID and a normalized payload digest, plus any progression update in the same transaction. A repeated identical submission returns the stored outcome; a different payload for the same match is a conflict. Do not perform a read-then-insert race without a database constraint.

Retry after database commit/response loss returns the existing record. A server crash before any durable submission is still a loss window at this level; report that limitation. Advanced A12 adds durable handoff and recovery.

## Worked example

Server submits m7 result, database commits, response disappears. The retry for m7 with the same digest returns the original result and does not increment wins twice. A client ticket cannot call the server-only result endpoint.

## Guided lab

1. Block 1: implement development ticket issue, verification/consumption, expiry, and match binding.
2. Attempt expired, wrong-match, reused, and wrong-server tickets.
3. Block 2: persist results and progression in a transaction with a unique match key.
4. Drop a post-commit response and retry; then submit a conflicting score.
5. Restart the backend/database connection and read the persisted result.

## Evidence required to pass

- Invalid tickets and client result submissions fail authorization.
- An identical retry produces one durable result and one progression update.
- Conflicting retry is visible, not silently accepted.
- The remaining pre-submission crash window is documented.

## Explain independently

Why does a signed or otherwise valid identity token not automatically authorize entry to any match?

## Reading and preparation

POSTGRES: transactions, unique constraints, ON CONFLICT; TLS and ticket validation use the chosen library's official guidance. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
