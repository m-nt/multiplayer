# I05 — Protocol Design and Message Schemas

[Part overview](../README.md) · [Previous](../04-transport-workshops/README.md) · [Next](../06-dedicated-server-process/README.md)

## Practicality and scope

Keep; replace ambiguous client 'score reporting' with server-owned score snapshots and authenticated server result submission.

**Time:** 2–3 hours. **Prerequisite:** I04 and its passing lab.

**By the end:** Write a protocol another implementation can follow and reject malformed or incompatible messages.

**Boundary:** One current major protocol version; compatibility policy is explicit rather than assumed.

## Teaching notes

A schema specifies field types, bounds, units, optionality, and meaning. A protocol also specifies sender, phase, response, errors, ordering, and duplicate policy. Identifiers have different purposes: request ID correlates an operation, sequence orders input, match ID scopes state, session generation separates connections.

Validate size before expensive decoding, type/version before dispatch, and authorization before mutation. Reject NaN/infinity where a format permits them, unknown required variants, wrong match, and out-of-range control values. Decide whether additive unknown fields are ignored or rejected.

Use test vectors shared by Unity and Go. JSON numbers do not preserve arbitrary integer precision in every client, so bound counters to interoperable ranges or encode large IDs as strings. Sequence wrap needs a policy; the teaching implementation can reconnect before a bounded counter wraps.

## Worked example

Input: {"version":1,"type":"input","match":"m7","sequence":184,"axis":1}. Player identity comes from the admitted connection. Snapshot includes server tick and last processed input sequence; a client-supplied score message is invalid.

## Guided lab

1. Document Join, Input, Snapshot, Reject, and MatchResult with all schema and flow fields.
2. Create valid and invalid JSON fixtures consumed by both runtimes.
3. Reject wrong version, oversize, wrong phase, stale session, and invalid axis.
4. Write the policy for additive fields and counter exhaustion.

## Evidence required to pass

- Both runtimes interpret the same fixture consistently.
- No invalid fixture changes state.
- Every client-to-server message has an authorization rule.

## Explain independently

Why does a sequence number not authenticate the sender or prove that an input was physically possible?

## Reading and preparation

WEBSOCKET: application subprotocol distinction; GO: JSON decoding documentation. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
