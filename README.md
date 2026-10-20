# Multiplayer Game Development Curriculum

A practical path from an existing local paddle game to an authoritative multiplayer game and a small operated server fleet.

This course teaches networking and multiplayer engineering. Students already know programming and basic Unity game development. Saber supplies local mechanics; the networking instructor owns online behavior; the security instructor reviews trust boundaries.

## Course map

| Part | Lessons | Guided time estimate | Independent practice | Outcome |
|---|---:|---:|---:|---|
| [I — Beginner](parts/01-beginner/README.md) | 12 | 24–30 hours | 12–24 hours | Two remote clients playing authoritative Pong |
| [II — Intermediate](parts/02-intermediate/README.md) | 16 | 48–64 hours | 32–48 hours | Dedicated server, identity, matchmaking, durable results |
| [III — Advanced](parts/03-advanced/README.md) | 18 | 54–72 hours | 40–60 hours | Measured allocation, failure handling, fleet deployment |

Estimates are planning allowances, not promises. A lesson is a learning unit, sometimes spanning several two-hour sessions. Language bridges and infrastructure preparation need extra time when entry checks are not met.

Each lesson directory contains a practicality review, scope, teaching notes, worked example, guided lab, pass criteria, and an independent reasoning question. Follow the numbered order within each part.

- [Teaching guide](TEACHING-GUIDE.md): starter requirements, environment, assessment, and instructor preparation.
- [Curriculum review](CURRICULUM-REVIEW.md): changes to the original outline and lesson crosswalk.
- [Source guide](SOURCES.md): primary readings and assigned sections.

## One project, increasing depth

1. Beginner: C# and **Mirror**, with one instructor-selected transport. Observe a running demo in lesson 1 and add networking throughout; lesson 11 integrates existing work.
2. Intermediate: retain Unity presentation and replace its network adapter with a custom **Go + WebSocket** authoritative server. Add HTTP APIs and PostgreSQL. TCP and UDP are focused experiments, not additional production stacks.
3. Advanced: package that system, implement a bounded allocator, then compare with **Kubernetes + Agones** on a prepared cluster. Simulate multiple regions locally first.

Mirror is the reference teaching choice. An instructor may substitute Netcode for GameObjects after adapting all labs and checkpoints. Do not switch frameworks mid-course. Socket.IO is a comparison topic, not another required stack.

The Go server uses simple kinematic paddle/ball rules. Define dimensions, collision rules, fixed-step behavior, and fixtures before porting; do not assume Unity physics can be reproduced across languages.

## Entry and completion gates

- Beginner: modify and build a local C# game, use Git, read logs, and understand functions and collections.
- Intermediate: pass beginner remote-client tests and complete the Go/HTTP/SQL bridge in the teaching guide.
- Advanced: run the intermediate system from documented commands and explain its identity and persistence boundaries; complete Linux/container preparation.
- Every part requires working behavior, failure demonstrations, reproducible evidence, and an explanation without a tutorial.

This repository contains teaching materials and lab specifications, not a completed starter implementation. Classroom capstones demonstrate skills; they do not certify production readiness. Submissions must list untested assumptions.
