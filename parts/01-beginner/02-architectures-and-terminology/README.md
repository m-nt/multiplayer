# B02 — Architectures and Networking Terminology

[Part overview](../README.md) · [Previous](../01-what-makes-a-game-multiplayer/README.md) · [Next](../03-messages-protocols-transports/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Keep the comparison, but require experiments with host failure and remote clients instead of choosing architecture by genre alone.

**Time:** 2 hours. **Prerequisite:** B01 and its passing lab.

**By the end:** Distinguish hosting topology from authority and justify a deployment model using constraints.

**Boundary:** Draw peer-to-peer and relay alternatives; implement only client-server.

## Teaching notes

A listen server runs beside a player's client, often in the same process. A dedicated server has no local player. Both can be authoritative. Peer-to-peer describes communication between peers; it does not guarantee that every peer has equal decision rights.

A relay forwards traffic to help connectivity. It does not automatically validate movement or decide a score. Hosting topology answers where communication goes; authority answers who decides. A backend handles longer-lived operations such as accounts, while a game server owns the live match.

Compare host departure, operating cost, reachability, trust, and player latency. 'Peer-to-peer is free' ignores relay and coordination costs. A public address is also not a guarantee that a firewall permits traffic.

## Worked example

Two friends can use a listen server, accepting that the host leaving ends the game. A ranked match may choose a dedicated server to avoid assigning that responsibility to a participant. Neither choice alone guarantees fair rules.

## Guided lab

1. Run the demo in host mode and connect a separate process.
2. Close the host and record what the remote client observes.
3. Sketch a dedicated-server alternative and identify which failure remains.
4. Write a decision for cooperative, competitive, and browser scenarios using explicit constraints.

## Evidence required to pass

- Each decision names a cost and a failure mode.
- The student does not assign game-rule authority to a relay automatically.
- Host departure is reproduced and recorded.

## Explain independently

Could a game behind a relay still let a cheating host change the score? Explain the path.

## Reading and preparation

MIRROR: server/client and authority concepts. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
