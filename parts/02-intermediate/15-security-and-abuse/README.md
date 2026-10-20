# I15 — Security and Abuse Prevention

[Part overview](../README.md) · [Previous](../14-linux-deployment/README.md) · [Next](../16-engineering-capstone/README.md)

## Practicality and scope

Keep as an adversarial review of already-integrated controls, not the first time security appears.

**Time:** 2–3 hours. **Prerequisite:** I14 and its passing lab.

**By the end:** Bound untrusted work and reject forged, replayed, or excessive actions without harming valid players.

**Boundary:** Authorized isolated lab only; no Internet scanning or production denial-of-service exercise.

## Teaching notes

List assets and entry points before controls: authoritative score, player identity, match capacity, CPU, memory, and durable results. An attacker can submit validly encoded messages at invalid rates or phases. Parsing success is not permission.

Use separate limits for connections, handshake duration, message size, action rate, and work queues. Rate limits should scope to admitted identity/session where possible; IP-only limits can punish multiple legitimate players behind NAT. Apply bounds before expensive work.

Log reason codes and aggregate counts without echoing credentials or arbitrary payloads. Replay protection depends on operation semantics: stale input sequences are rejected, while retried result IDs return a prior outcome. Encryption does not make a malicious participant trustworthy.

## Worked example

A valid player sends 10,000 inputs in one second. The server must not simulate 10,000 movement steps or allocate an unbounded queue. Reject/throttle according to policy while the other player's match continues.

## Guided lab

1. Threat-model each client and service endpoint with the security instructor.
2. Run a bounded local harness for oversized data, input flood, replay, and forged score.
3. Observe CPU, queues, rejection counts, and valid-player progress.
4. Fix one uncovered control and rerun the exact case.

## Evidence required to pass

- No invalid message changes authoritative score or ownership.
- Queues and work remain within declared bounds during the harness.
- Valid-client progress and rejected-action evidence are recorded.

## Explain independently

Why does 'the connection uses TLS' not protect game rules from a modified client?

## Reading and preparation

Review I02, I05, I10, and I11 controls; optional OWASP WebSocket guidance in SOURCES. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
