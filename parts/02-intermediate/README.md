# Intermediate — Multiplayer Engineering

[Curriculum](../../README.md) · [Teaching guide](../../TEACHING-GUIDE.md) · [Review](../../CURRICULUM-REVIEW.md)

## Part practicality review

Practical with a bounded game and explicit language preparation. Budget 48–64 guided hours and 32–48 practice hours. Transport, custom server, prediction, identity/persistence, and deployment need multiple blocks. Keep Unity presentation, use Go for simple kinematic authority, and use one WebSocket protocol rather than maintaining several full implementations.

## Entry gate

B12 plus the Go, HTTP, SQL, and cancellation bridge in the teaching guide.

## Scope and teaching depth

Required: one custom WebSocket application protocol, fixed-step Go simulation, local paddle prediction, bounded I/O, development identity tickets, transactional results, single-owner matchmaking, and Linux deployment.

Optional: binary encoding, delta compression, spatial-interest harness, shooter rewind, alternative transports, and a different backend HTTP router. Use the standard Go HTTP stack or one preselected router; learning a router is not a separate course outcome.

Students implement contracts and diagnose edge cases. Require concrete ownership, queue bounds, parsing rules, timing semantics, and persistence boundaries.

## Lesson sequence

| ID | Lesson | Guided time | Practical outcome |
|---|---|---|---|
| I01 | [Review and Redesign the Beginner Architecture](01-review-and-boundaries/README.md) | 2 hours | Separate simulation, connection handling, and presentation while preserving the beginner match. |
| I02 | [Authority Models in Depth](02-authority-models/README.md) | 2 hours | Validate every action against identity, membership, ownership, phase, and current session. |
| I03 | [Interaction Patterns, Acknowledgements, and Idempotency](03-interaction-and-idempotency/README.md) | 2 hours | Implement a retriable command whose repeated request does not repeat its effect. |
| I04 | [Transport Workshops: TCP, UDP, and WebSocket](04-transport-workshops/README.md) | 6 hours in three workshops | Explain what TCP actually provides, frame a byte stream, observe UDP behavior, and implement a WebSocket exchange. |
| I05 | [Protocol Design and Message Schemas](05-protocol-and-schemas/README.md) | 2–3 hours | Write a protocol another implementation can follow and reject malformed or incompatible messages. |
| I06 | [Dedicated Game Server Architecture](06-dedicated-server-process/README.md) | 2–3 hours | Run a server with no local player and control it through configuration and shutdown. |
| I07 | [Build an Independent Go Server](07-go-server-and-client-adapter/README.md) | 4–6 hours | Connect Unity presentation to a Go WebSocket server that owns paddle positions. |
| I08 | [Simulation Ticks and Authoritative State](08-fixed-step-authoritative-simulation/README.md) | 3–4 hours | Advance complete Pong rules at a fixed step independently of rendering and socket speed. |
| I09 | [Interpolation, Prediction, and Reconciliation](09-prediction-and-reconciliation/README.md) | 4–6 hours | Predict the local paddle, reconcile acknowledged input, and interpolate remote state with measurable trade-offs. |
| I10 | [Network Reliability and Performance](10-network-budgets-and-backpressure/README.md) | 2–3 hours | Enforce traffic/queue budgets and reduce measured waste without losing essential state. |
| I11 | [Backend Identity and Durable Match Results](11-identity-backend-and-results/README.md) | 4–6 hours | Bind a connection to an authorized player and persist one authoritative result despite retries. |
| I12 | [Lobby and Matchmaking Implementation](12-lobby-and-matchmaking/README.md) | 3–4 hours | Create an eligible pair, assign one available server, and issue match-bound admission. |
| I13 | [Run Multiple Game Server Instances](13-multiple-server-instances/README.md) | 2–3 hours | Run isolated matches on different configured endpoints and track usable capacity. |
| I14 | [Deployment Fundamentals](14-linux-deployment/README.md) | 3–4 hours | Deploy the server securely enough for the controlled lab and diagnose cross-network reachability. |
| I15 | [Security and Abuse Prevention](15-security-and-abuse/README.md) | 2–3 hours | Bound untrusted work and reject forged, replayed, or excessive actions without harming valid players. |
| I16 | [Intermediate Capstone](16-engineering-capstone/README.md) | 4–6 guided hours plus project practice | Deliver a reproducible dedicated multiplayer system and explain its failure behavior. |

## Project checkpoints

- **I01–I03:** Establish ownership, boundaries, and retry semantics.
- **I04–I05:** Run transport experiments and specify the application protocol.
- **I06–I10:** Run a dedicated process, replace it with Go, implement ticks/prediction, and bound traffic.
- **I11–I13:** Add identity, durable results, matchmaking, and multiple instances.
- **I14–I16:** Deploy, test abuse cases, and hand off a reproducible system.

## Completion gate

Two Unity clients reach a separately deployed authoritative Go server through a two-player queue. Identity, admission, invalid-action rejection, bounded I/O, and duplicate-safe durable results pass documented tests.

Use the final lesson's mandatory evidence checks and the shared assessment rubric. Complete missing preparation before continuing; do not compensate for a missing implementation with a diagram alone.
