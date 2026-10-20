# Beginner — Multiplayer Fundamentals

[Curriculum](../../README.md) · [Teaching guide](../../TEACHING-GUIDE.md) · [Review](../../CURRICULUM-REVIEW.md)

## Part practicality review

Practical as a first networking course when every concept is attached to a running game. Use supplied demos for the first observations, then implement small increments. Keep one framework and defer raw sockets, accounts, deployment, and prediction algorithms. Budget 24–30 guided hours and 12–24 practice hours.

## Entry gate

The local-game build and logging entry check in the teaching guide.

## Scope and teaching depth

Required: one framework, authoritative Pong, state initialization, basic interpolation observation, lifecycle cleanup, remote-client tests, and simple measurements.

Deferred: raw sockets, Go, databases, real identity, prediction implementation, public deployment, and orchestration.

Students first predict and observe, then change one behavior. Use plain process/message/state explanations and work from the supplied local mechanics.

## Lesson sequence

| ID | Lesson | Guided time | Practical outcome |
|---|---|---|---|
| B01 | [What Makes a Game Multiplayer?](01-what-makes-a-game-multiplayer/README.md) | 2 hours | Trace one paddle input through the server to both displays and identify the authoritative state. |
| B02 | [Architectures and Networking Terminology](02-architectures-and-terminology/README.md) | 2 hours | Distinguish hosting topology from authority and justify a deployment model using constraints. |
| B03 | [Messages, Protocols, and Transports](03-messages-protocols-transports/README.md) | 2 hours | Describe serialization and distinguish transport, application protocol, and framework. |
| B04 | [Game State and Synchronization](04-state-and-synchronization/README.md) | 2 hours | Build a minimal snapshot and initialize a joining client's view. |
| B05 | [Client-Server Interaction Patterns](05-interaction-patterns/README.md) | 2 hours | Implement request/result, command, and notification flows without treating RPC as an immediate local call. |
| B06 | [Authority, Ownership, and Trust](06-authority-ownership-trust/README.md) | 2 hours | Remove client score authority and validate movement intent at the server. |
| B07 | [Time, Latency, and Real-Time Constraints](07-time-latency-realtime/README.md) | 2–3 hours | Measure RTT, distinguish tick/send/render rates, and explain the cost of buffering. |
| B08 | [Player and Match Lifecycle](08-player-match-lifecycle/README.md) | 2 hours | Implement join, ready, play, finish, and disconnect behavior with a documented late-join policy. |
| B09 | [Game Server versus Backend Services](09-server-versus-backend/README.md) | 2 hours | Place live simulation and durable operations correctly and isolate a result-submission boundary. |
| B10 | [Performance and Reliability Fundamentals](10-performance-reliability-basics/README.md) | 2 hours | Calculate a simple traffic budget and choose policies for state freshness and important events. |
| B11 | [Integrate the Multiplayer Pong Game](11-integrate-multiplayer-pong/README.md) | 2–3 hours plus project practice | Complete a two-player match through authoritative rules and synchronized presentation. |
| B12 | [Testing, Debugging, and Architectural Review](12-test-debug-review/README.md) | 2–3 hours | Diagnose a failure using logs and defend the completed beginner architecture. |

## Project checkpoints

- **B01–B03:** Observe a running game and explain its message path.
- **B04–B06:** Replicate state, implement ready/start, and enforce server authority.
- **B07–B10:** Observe delay, manage cleanup, isolate backend work, and measure traffic.
- **B11–B12:** Integrate and independently demonstrate remote play and failure handling.

## Completion gate

Two standalone clients on separate machines play a full match; only the server decides scores, ball motion, and match outcome. Disconnect cleanup and delayed-network behavior are demonstrated.

Use the final lesson's mandatory evidence checks and the shared assessment rubric. Complete missing preparation before continuing; do not compensate for a missing implementation with a diagram alone.
