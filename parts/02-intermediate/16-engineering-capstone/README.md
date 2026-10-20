# I16 — Intermediate Capstone

[Part overview](../README.md) · [Previous](../15-security-and-abuse/README.md) · [Next](../README.md)

## Practicality and scope

Treat as integration and review of completed labs; no new database or matchmaking feature should first appear here.

**Time:** 4–6 guided hours plus project practice. **Prerequisite:** I15 and its passing lab.

**By the end:** Deliver a reproducible dedicated multiplayer system and explain its failure behavior.

**Boundary:** A small deployable system with explicit limits; no fleet orchestration or production-readiness claim.

## Teaching notes

Walk the complete journey: development identity → queue → assignment → ticket admission → authoritative match → durable result. Each handoff needs an identifier and an observable outcome. Losing a response must not duplicate queue membership or result effects.

Keep the deployment small enough that another student can run it. Include configuration examples without secrets, schema setup, version requirements, commands, and expected readiness. Record intentional limitations such as the server-crash result-loss window or the single-worker queue.

Review trade-offs using evidence. WebSocket simplifies a cross-language teaching stack, but its ordered stream can delay fresher updates behind recovery of earlier data. The project should acknowledge that behavior rather than claiming it is optimal for every real-time game.

## Worked example

The reviewer drops the final-result response after commit and repeats the submission. The database still contains one result and one progression change, while clients see a consistent completed outcome.

## Guided lab

1. Package run instructions, protocol, authority table, and migration/setup steps.
2. Have a partner run two remote clients through queue and match completion.
3. Inject invalid ticket, slow reader, mid-match crash, and post-commit response loss.
4. Present measurements and the unrecovered crash window; propose bounded future work.

## Evidence required to pass

- Mandatory: remote clients use the Go authoritative server and real matchmaking.
- Mandatory: invalid admission/actions are rejected and slow-reader queues are bounded.
- Mandatory: identical result retry has one durable effect.
- A partner reproduces setup and explains process/recovery limits from the documentation.

## Explain independently

Which guarantees survive a backend restart, which survive a game-server crash, and what evidence supports each?

## Reading and preparation

Review I01–I15 artifacts and the teaching guide's assessment rubric. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
