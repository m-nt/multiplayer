# Teaching and Lab Guide

[Curriculum](README.md)

## Supplied materials and student work

This repository supplies notes and lab specifications. It does not yet contain a runnable Unity starter, Go server, manifests, or hosted cluster. Prepare and smoke-test these before teaching.

Supply a working local paddle game with input, fixed-step ball/collision rules, scores, restart, and configurable court dimensions. Keep presentation separate enough to replace input/state adapters. Preserve a baseline and a known-good checkpoint after each lesson as the cohort progresses.

Beginner lesson 1 requires a preconfigured Mirror demo with a host/client launcher and input/state logs. Students observe it, then build networking into their own local-game branch. Lesson 6 requires a deliberately insecure score command in a disposable branch. Lesson 7 requires delay injection. These are instructor preparation requirements, not assets already provided.

Intermediate requires a pinned Go WebSocket library, a Unity-compatible WebSocket adapter, and local PostgreSQL. A stock Mirror client does not speak the custom JSON protocol; replace that adapter explicitly. Advanced requires a disposable cluster, registry access, and SDK preparation. Select and record compatible versions before class.

## Entry exercises and bridges

Beginner: build the local game, run two processes, locate a log, and change paddle speed. Record OS, Unity, framework, transport, target, and Git commit.

Intermediate: allow 6–10 additional guided hours when Go is new. Teach modules, structs, errors, JSON, tests, goroutines, channels, cancellation, and race detection. Build a cancellable worker with a bounded queue; explain who owns its data and how it stops. Practice HTTP requests and a SQL transaction with a unique constraint. These exercises are prerequisites, not hidden work in the server lesson.

Advanced: allow 4–8 additional hours for Linux and containers when needed. Inspect processes, ports, logs, configuration, shutdown, images, and resource use. The instructor prepares cluster access and networking; cluster installation is outside lesson time.

## Two-hour classroom pattern

Allow approximately 15 minutes for retrieval and a visible failure, 25 for concepts and a worked example, 60 for implementation, and 20 for evidence and explanation. Split longer lessons into the stated blocks. Ask students to predict an observation before running it.

Teach vocabulary through behavior: show two writes arriving in one read before naming TCP's byte stream. Beginner students modify and explain one behavior. Intermediate students own an implementation and invalid-input cases. Advanced students state invariants, measure distributions, inject partial failures, and defend recovery policy.

## Shared lab conditions

Test host plus separate client, then dedicated process plus two standalone clients. Include two computers or an instructor-managed remote runner. Editor-only evidence is insufficient for completion.

Record three illustrative profiles: local baseline; 100 ms delay each way plus up to 20 ms extra jitter; and that delay with 1% datagram loss in an isolated transport harness. Record direction and injection point. These are experiment settings, not universal Internet conditions.

Application-message dropping does not model TCP packet loss. Observe retransmission with network-level impairment or instructor traces. Impair only disposable lab processes/interfaces. If network controls are unavailable, use application delay queues and provided transport traces and state that limitation.

Record commit, machine, configuration, clients, duration, transport, tick/send rates, payload sizes, impairment, and raw measurements. Begin with 60 Hz simulation and 20 Hz snapshots as experiment settings; measure before changing them.

## Shared game contract

Use court units, speed in units/second, and an input axis in [-1, 1]. Server owns the ball and scores. Reject nonfinite numbers and invalid input. Snapshots include match ID, tick, and each player's last processed input sequence. Never trust client-provided elapsed time.

Create fixtures for no input, court-edge clamping, paddle collision, wall collision, one awarded point, and restart. A fast ball may need swept collision or substeps: bound fixtures to the implemented collision model and document limits. Reuse fixtures when moving simulation to Go.

Students maintain an authority table, protocol table, lifecycle diagram, test report, load report, and runbook alongside their implementation. Remove credentials from evidence.

## Assessment and preparation

Each lesson passes only when its evidence checks pass and the student answers the reasoning question using a concrete example. A polished UI does not compensate for failed authority or cleanup.

Capstone rubric: behavior 30%, failure handling/invariants 30%, reproducible evidence 25%, independent explanation 15%. Require at least 70% overall and every mandatory capstone gate. A partner runs the submission from its instructions.

Before class, run each lab on pinned versions, capture expected results, prepare checkpoints, confirm hardware/access, and budget bridge time. Assigned reading supplements these notes; an entire RFC or manual is not required.
