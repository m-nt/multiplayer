# Primary Reading and Source Guide

[Curriculum](README.md)

The lesson explanations, example numbers, laboratory policies, and assessment design are authored teaching material. The sources below support protocol and tool behavior; they are not substitutes for the lesson notes. Protocol/tool references were consulted during this revision on October 20, 2026. Web documentation can change: instructors must select compatible versions and verify the labs before each cohort. Do not interpret a current documentation URL as a pinned dependency.

Read only the assigned portions. Beginner students need concepts and diagrams; intermediate students need API contracts; advanced students need lifecycle, concurrency, and failure semantics.

## MIRROR — framework implementation reference

- [Authority](https://mirror-networking.gitbook.io/docs/manual/guides/authority): server authority, client authority, assignment/removal. Assigned B01, B02, B06, I02.
- [Synchronization](https://mirror-networking.gitbook.io/docs/manual/guides/synchronization): state delivery and initialization. Assigned B04.
- [Remote actions](https://mirror-networking.gitbook.io/docs/manual/guides/communications/remote-actions): command and RPC direction. Assigned B05.

Use the pinned framework's own manual for server-build and lifecycle APIs. Compare the framework behavior with the lesson contract rather than treating the API name as a complete authorization policy.

## TCP — transport specification

- [RFC 9293](https://www.rfc-editor.org/rfc/rfc9293.html): section 2.2 for concepts, 3.5 for establishment, 3.6 for closing, and selected 3.8 discussion for communication. B03/B10 read concepts only; I04 annotates a trace.
- Instructor emphasis: stream boundaries, connection lifecycle, acknowledgement versus application execution. Students do not implement TCP itself.

## UDP — datagrams

- [RFC 768](https://www.rfc-editor.org/rfc/rfc768): read the short introduction and user-interface discussion for I04.
- [UDP usage guidelines, RFC 8085](https://www.rfc-editor.org/rfc/rfc8085): instructor/optional intermediate reading on congestion and message size. The small lab harness is not a production UDP protocol.

## WEBSOCKET — protocol and application boundary

- [RFC 6455](https://www.rfc-editor.org/rfc/rfc6455): sections 1, 4, 5.5, and 7 for overview, opening, control frames, and closing. Assigned I04–I05; B03 needs only the overview.
- [Socket.IO comparison](https://socket.io/docs/v4/): read the protocol-compatibility explanation for the optional comparison. No Socket.IO implementation is required.
- [OWASP WebSocket security guidance](https://cheatsheetseries.owasp.org/cheatsheets/WebSocket_Security_Cheat_Sheet.html): authentication, authorization, size/rate controls, and logging. Assigned I15 with the security instructor.

Select one maintained WebSocket library and pin it for the cohort. Its concurrency, cancellation, message-limit, and browser-authentication contracts must be part of instructor preparation.

## GO, GO-NET, GO-RACE, GO-PROFILE — implementation contracts

- [A Tour of Go](https://go.dev/tour/): structs, errors, methods, goroutines, and channels for the bridge.
- [net package](https://pkg.go.dev/net): Listener, Conn, Read, Write, and deadlines for I04/I06.
- [context package](https://pkg.go.dev/context): cancellation ownership for I07.
- [encoding/json package](https://pkg.go.dev/encoding/json): decoding, types, and unknown-field behavior for I05.
- [time package](https://pkg.go.dev/time): monotonic clocks and timers for I08.
- [Race detector](https://go.dev/doc/articles/race_detector): usage and limitations for I07.
- [Diagnostics](https://go.dev/doc/diagnostics): profiles, tracing, and runtime statistics for A10/A16.

Read the documentation corresponding to the recorded Go toolchain. A race-detector run checks executed paths; it is not a proof of concurrency correctness.

## NETCODE — original explanatory work

- [Glenn Fiedler: Snapshot Interpolation](https://gafferongames.com/post/snapshot_interpolation/): diagrams and buffering trade-offs for B07; implementation discussion for I09.
- [Gabriel Gambetta: Client-Side Prediction and Server Reconciliation](https://www.gabrielgambetta.com/client-side-prediction-server-reconciliation.html): numbered inputs, acknowledgement, and replay for I09.
- [Glenn Fiedler: Fix Your Timestep](https://gafferongames.com/post/fix_your_timestep/): fixed steps and overload for I08.

These are original engineering explanations by their authors, not framework API contracts. Use the curriculum's explicit sample scheduling, bounded histories, and fixtures when implementing the lab.

## POSTGRES — durable decisions

- [Transactions](https://www.postgresql.org/docs/current/tutorial-transactions.html): atomic commit/rollback for the bridge and I11.
- [Constraints](https://www.postgresql.org/docs/current/ddl-constraints.html): primary, unique, and foreign keys for I11/A04/A12.
- [Transaction isolation](https://www.postgresql.org/docs/current/transaction-iso.html): concurrent visibility and anomalies for A02–A04.
- [Explicit locking](https://www.postgresql.org/docs/current/explicit-locking.html): row locks, lock order, and deadlocks for A03–A04.
- [INSERT and ON CONFLICT](https://www.postgresql.org/docs/current/sql-insert.html): duplicate-safe writes for I11/A12.

Constraints, transactions, and retry policy must work together. Read the chosen database version's manual; do not assume every SQL engine implements the same syntax or isolation behavior.

## DOCKER — packaging

- [Multi-stage builds](https://docs.docker.com/build/building/multi-stage/): separation of build and runtime for A05.
- [Resource constraints](https://docs.docker.com/engine/containers/resource_constraints/): limits and observed behavior for A05/A10.
- [Port publishing](https://docs.docker.com/engine/network/port-publishing/): container versus host reachability for A05.

## KUBERNETES — workload lifecycle

- [Startup, readiness, and liveness probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/): distinct failure consequences for A06/A14.
- [Resource management](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/): requests and limits for A06/A10.
- [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/): narrowly scoped permissions for A15.

Use a prepared disposable cluster. Match-state recovery remains an application problem.

## AGONES-SERVER, AGONES-SDK, AGONES-ALLOCATION — game lifecycle

- [GameServer specification](https://agones.dev/site/docs/reference/gameserver/): resource configuration for A07.
- [Server SDKs](https://agones.dev/site/docs/guides/client-sdks/): Ready, Health, Shutdown, and local development for A07.
- [Allocation specification](https://agones.dev/site/docs/reference/gameserverallocation/): selection and allocation state for A07/A08.
- [Fleet autoscaler](https://agones.dev/site/docs/reference/fleetautoscaler/): ready capacity policy for A10.
- [Fleet updates](https://agones.dev/site/docs/guides/fleet-updates/): existing allocations and version rollout for A17.
- [Service accounts](https://agones.dev/site/docs/advanced/service-accounts/): access boundaries for A15.

Use version-matched docs and avoid optional/experimental features unless intentionally taught. Platform allocation and application request deduplication have separate responsibilities.

## OTEL — diagnostics

- [OpenTelemetry signals](https://opentelemetry.io/docs/concepts/signals/): metrics, logs, and traces for A01/A13.
- [Context propagation](https://opentelemetry.io/docs/concepts/context-propagation/): connecting operations across boundaries for A13.

Students select a small actionable signal set, then diagnose an injected fault. Installing a dashboard alone does not meet the lesson outcome.
