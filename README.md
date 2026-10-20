# Multiplayer Game Development Curriculum
## Beginner → Intermediate → Advanced

## Purpose and Scope

This curriculum teaches **multiplayer game development as a discipline separate from making games in Unity**.

The objective is to teach students how to make an existing game work across multiple computers, design networking architecture, and eventually operate multiplayer games in production.

- **Beginner:** How does a multiplayer game work?
- **Intermediate:** How do we build, secure, and deploy a multiplayer game?
- **Advanced:** How do we design, scale, orchestrate, and operate multiplayer infrastructure?

Each course produces a working project. The projects build on one another so students learn a coherent system rather than disconnected technologies.

### Course overview

| Dimension | Beginner | Intermediate | Advanced |
|---|---|---|---|
| Primary objective | Understand multiplayer fundamentals | Engineer a complete multiplayer system | Design and operate production infrastructure |
| Focus | Concepts, networking models, basic implementation | Authority, protocols, game servers, matchmaking | Architecture, orchestration, scaling, reliability |
| Game development | Assumed prerequisite | Assumed prerequisite | Assumed prerequisite |
| Networking | Core concepts and basic synchronization | Deep implementation and trade-offs | Performance, scaling, failure handling |
| Server architecture | Introduction | Design and deployment | Distributed infrastructure |
| Backend APIs | Conceptual introduction | Implementation and integration | Service architecture and operations |
| Matchmaking | Conceptual introduction | Functional matchmaking | Allocation, orchestration, scaling |
| Deployment | Run local processes | Deploy a dedicated server | Docker, orchestration, automated lifecycle management |
| Security | Trust boundaries and basic validation | Authority enforcement and abuse prevention | Infrastructure security and operational controls |
| Final project | Multiplayer paddle game | Networked game with dedicated server and matchmaking | Multiplayer platform with automated game-server management |

### Prerequisites and teaching boundary

Students should already understand basic programming and have completed an introductory game-development course. This course is **not a second Unity course**.

Saber owns local game mechanics: movement, shooting, animation, physics, UI, and core gameplay. This course owns networking, multiplayer architecture, authority, game servers, and online services. The security programmer contributes threat modeling and security engineering.

Use a working single-player paddle game supplied by the game-development course. Students modify only the code necessary to introduce networking.

---

# Part I — Beginner Course
## Multiplayer Fundamentals: From Local Game to Multiplayer Game

**Objective:** Students understand the architecture, terminology, communication patterns, and fundamental problems of multiplayer games. They finish by implementing a simple networked paddle game.

**Suggested duration:** 12 lessons, approximately 2 hours each, plus independent practice.

The beginner course should broadly cover the multiplayer landscape, but implement only a small, coherent subset.

## Lesson 1 — What Actually Makes a Game Multiplayer?

**Objective:** Build a correct mental model before introducing networking code.

Topics:
- Local multiplayer versus online multiplayer.
- Client, server, network protocol, and game state.
- Why two independent game simulations drift apart.
- Game logic versus networking.
- Authority versus synchronization.
- Why multiplayer requires coordination.

Example architecture:

```text
Player A Client ── inputs ──┐
                            ▼
                     Authoritative
                      Game Server
                            ▲
Player B Client ── inputs ──┘
                            │
                      State updates
                       to clients
```

The server maintains the authoritative state in this example. Real systems may send different updates to different clients and need not transmit the entire state every time.

**Practical exercise:** Draw the architecture of a two-player game and identify what information must be shared.

**Expected outcome:** Students understand why multiplayer requires communication and coordinated state.

## Lesson 2 — Multiplayer Architectures and Networking Terminology

**Objective:** Understand the major architectural models before choosing an implementation.

Topics:
- Client-server architecture.
- Dedicated servers.
- Listen servers and player-hosted games.
- Peer-to-peer networking.
- Relays.
- Authoritative versus non-authoritative systems.
- Centralized versus distributed responsibilities.
- Game server versus backend service.

Architectures to compare:

```text
Listen server:
Player A [Client + Server] ───── Player B [Client]

Dedicated server:
Player A [Client] ───── [Dedicated Server] ───── Player B [Client]

Peer-to-peer:
Player A [Peer] ◄────────► Player B [Peer]

Relay-based:
Player A [Client] ───── [Relay] ───── Player B [Client]
```

A relay forwards traffic and does not necessarily simulate the game. Peer-to-peer systems may still assign special authority to one peer.

Compare cost, connectivity, trust, complexity, and failure modes.

**Practical exercise:** Choose an architecture for a cooperative game, a competitive shooter, and a small browser game. Justify each choice.

## Lesson 3 — Network Communication: Messages, Protocols, and Transports

**Objective:** Understand how information moves between machines.

Topics:
- Messages and serialization.
- JSON and binary serialization.
- Message schemas and versions.
- TCP and UDP.
- HTTP and WebSocket.
- Networking frameworks such as Mirror and Unity Netcode for GameObjects.
- Socket.IO.
- RPC (remote procedure call).

| Technology | What it provides | Typical use |
|---|---|---|
| TCP | Reliable, ordered byte stream | Many application protocols |
| UDP | Datagram transport without built-in delivery guarantees | Real-time networking protocols |
| HTTP | Request-response application protocol | Authentication, profiles, APIs |
| WebSocket | Persistent bidirectional communication over a connection | Browser-based real-time applications |
| Socket.IO | Higher-level event-based communication with its own protocol | Event-driven real-time applications |
| RPC | An abstraction for invoking remote operations | Framework-dependent remote calls |

WebSocket is not a replacement for understanding TCP or UDP. Socket.IO clients are not automatically compatible with ordinary WebSocket servers. RPC is an abstraction, not a transport.

**Practical exercise:** Classify example messages as player input, state update, event, or request-response operation.

## Lesson 4 — Game State and Synchronization

**Objective:** Understand what must be synchronized and why.

Topics:
- Authoritative game state.
- State versus event versus input versus command.
- Snapshots.
- State replication.
- State ownership.
- Update frequency.
- Full snapshots versus incremental updates.
- Initial synchronization.
- Late-joining players.

Example state:

```text
MatchState
├── Ball: Position, Velocity
├── Player A: Paddle Position, Score
├── Player B: Paddle Position, Score
└── Match Status: Playing / Finished
```

Not everything needs continuous synchronization. Paddle positions may need frequent updates, while match-start events happen only once.

**Practical exercise:** Decide which Pong values need synchronization, how often they should be sent, and which events should be sent only when something happens.

## Lesson 5 — Client-Server Interaction Patterns

**Objective:** Learn how clients and servers communicate at the application level.

Patterns:
1. Request-response.
2. Command and authoritative result.
3. Server-to-client notification.
4. Continuous state updates.

```text
Request-response:
Client ── Request ──► Server
Client ◄─ Response ── Server

Command:
Client ── Input/Command ──► Server
Client ◄── Accepted State ── Server

Broadcast:
Server ── Event ──► Client A
Server ── Event ──► Client B
```

Introduce RPCs without making the lesson a framework-specific API tutorial. Explain that a command expresses an intention and may be rejected; an event describes something that has occurred.

**Practical exercise:** Design the messages needed to start a match, move a paddle, score a point, and end a match.

## Lesson 6 — Authority, Ownership, and Trust

**Objective:** Understand who is allowed to decide what happens.

Topics:
- Client authority.
- Server authority.
- Object ownership.
- Input authority.
- State authority.
- Trust boundaries.
- Server-side validation.
- Why client-side validation is insufficient for competitive games.

Core principle: **Clients communicate intent; the authority responsible for a piece of game state determines whether a requested change is valid.**

Authority can be assigned per piece of state or operation. Not every system must use the same authority model.

**Practical exercise:** Let a client update its own score, demonstrate the exploit, then redesign the system so the server owns the score.

## Lesson 7 — Time, Latency, and Real-Time Constraints

**Objective:** Understand why networked games behave differently from local games.

Topics:
- Latency and round-trip time (RTT).
- Jitter and packet loss.
- Bandwidth.
- Update frequency.
- Simulation tick rate versus frame rate.
- Clock differences.
- Interpolation.
- Extrapolation.
- Prediction.
- Reconciliation.

A network message can arrive late or be lost, depending on the transport and protocol. Increasing update frequency alone does not solve every latency problem.

**Practical exercise:** Add artificial delay to Pong and observe the effects on paddle movement and ball synchronization.

## Lesson 8 — Player Lifecycle and Match Lifecycle

**Objective:** Understand the lifecycle of players and matches.

Topics:
- Connecting and disconnecting.
- Authentication as a concept.
- Joining a session.
- Spawning a player.
- Leaving and reconnecting.
- Unexpected disconnections.
- Match start and end.
- Late joining.
- Session cleanup.
- Lobby versus match.

Example lifecycle:

```text
Waiting for Players
        ↓
   Match Starting
        ↓
     In Progress
        ↓
    Match Finished
        ↓
      Cleaned Up
```

**Practical exercise:** Implement the lifecycle and test what happens when a player disconnects during each phase.

## Lesson 9 — Game Server versus Backend Services

**Objective:** Assign responsibilities to the right components.

Topics:
- Game server processes.
- Backend APIs.
- Authentication services.
- Databases.
- Matchmaking.
- Server allocation.
- Logging and monitoring.

```text
                 GAME BACKEND
          ┌────────────────────┐
          │ Accounts           │
          │ Matchmaking        │
          │ Persistent Data    │
          └─────────┬──────────┘
                    │ HTTPS / API
                    ▼
               GAME SERVER
          ┌────────────────────┐
          │ Match State        │
          │ Game Rules         │
          │ Scores             │
          │ Player Actions     │
          └─────────┬──────────┘
                    │ Real-time networking
              ┌─────┴─────┐
              ▼           ▼
           Client A    Client B
```

These are logical responsibilities, not necessarily separate machines. A small project may combine components; larger games may deploy them independently.

**Practical exercise:** Assign a list of responsibilities to a client, game server, or backend service.

## Lesson 10 — Network Performance and Reliability Fundamentals

**Objective:** Introduce constraints of real-time networked systems.

Topics:
- Bandwidth versus latency.
- Message frequency and payload size.
- Reliable versus unreliable delivery.
- Ordered versus unordered delivery.
- Lost and duplicate messages.
- Timeouts.
- Basic network debugging.
- Message freshness.

Examples:
- Paddle position: freshness may matter more than delivering every update.
- Match-ending result: should not be silently lost.
- Chat: reliable delivery is generally desirable.
- Purchase transaction: requires reliable processing and duplicate protection.

**Practical exercise:** Classify messages by delivery requirements and explain the trade-offs.

## Lesson 11 — Build the Multiplayer Pong Game

**Objective:** Apply the fundamentals in one complete project.

Students implement:
- A server process or host.
- Client connections.
- Player registration within a match.
- Paddle input messages.
- Authoritative ball movement.
- Collision handling.
- Score calculation.
- State synchronization.
- Match start and end.
- Disconnection handling.

Use an existing Unity networking framework rather than requiring students to implement raw TCP or UDP from scratch.

**Practical exercise:** Build and test a two-player game in which the server advances the authoritative simulation and clients send input and render the resulting state.

## Lesson 12 — Testing, Debugging, and Architectural Review

**Objective:** Ensure students understand what they built.

Topics:
- Testing multiple clients.
- Testing across separate computers.
- Network logs.
- Connection failures.
- Simulated latency.
- Player disconnections.
- Authority bugs.
- Synchronization bugs.
- Basic bandwidth and update-frequency analysis.

**Final assessment:** Each student must explain:
1. Where the authoritative game state lives.
2. How player input reaches the server.
3. How the server updates the game.
4. How clients learn about state changes.
5. What happens when a client disconnects.
6. What must change to deploy a dedicated server.

### Beginner completion criteria

Students can:
- Explain client-server architecture and alternative models.
- Distinguish gameplay networking from backend APIs.
- Explain authority, ownership, and state synchronization.
- Describe TCP, UDP, HTTP, WebSocket, and RPC at a conceptual level.
- Explain latency, jitter, packet loss, and tick rate.
- Build and test a basic multiplayer game.
- Explain the path from a locally hosted prototype to dedicated-server deployment.

---

# Part II — Intermediate Course
## Multiplayer Engineering: Authority, Protocols, Game Servers, and Matchmaking

**Objective:** Students design and implement a complete multiplayer system, choose communication mechanisms, run a dedicated game server, connect it to backend services, and implement basic matchmaking.

**Suggested duration:** 16 lessons, approximately 2 hours each, plus project work.

## Lesson 1 — Review and Redesign the Beginner Architecture

Topics:
- Review the Pong architecture.
- Identify authoritative state.
- Reduce unnecessary network traffic.
- Separate game simulation from rendering.
- Separate transport, protocol, and game logic.
- Define module boundaries.
- Choose where validation belongs.

**Practical exercise:** Refactor the project to separate message handling, simulation, and presentation.

## Lesson 2 — Authority Models in Depth

Topics:
- Server, client, and distributed authority.
- Object ownership.
- Input authority versus state authority.
- Authority transfer.
- Host advantage.
- State validation.
- Trust boundaries.
- Security implications.

Examples:

| Game mechanic | Recommended starting model |
|---|---|
| Competitive shooting | Server-authoritative hit and damage decisions |
| Player movement | Client input, server validation, authoritative state |
| Cosmetic effects | Often client-side presentation |
| Inventory ownership | Authoritative backend or game server, depending on operation |
| Match score | Authoritative game server |
| Player camera | Usually local to the player |
| Cooperative puzzle object | Depends on game; server authority is a straightforward baseline |

**Practical exercise:** Demonstrate a manipulable client-authoritative score, then make the score server-authoritative.

## Lesson 3 — Client-Server Interaction Patterns in Depth

Topics:
- Requests and responses.
- Commands and RPCs.
- Server-to-client and client-to-server messages.
- Broadcasts and targeted messages.
- State replication.
- Event-driven communication.
- Streaming updates.
- Acknowledgements and timeouts.
- Idempotency and duplicate messages.

A command expresses intent:

```text
FireWeapon(weapon_id, aim_direction)
```

An event describes what occurred:

```text
WeaponFired(player_id, weapon_id)
```

The server may reject a command. The corresponding event should only be emitted if the action actually occurs according to the game rules.

**Practical exercise:** Design communication flows for shooting, damage, item collection, and match completion.

## Lesson 4 — Networking Protocols and Transport Selection

Topics:

### TCP and UDP sockets
- TCP connection lifecycle.
- UDP datagrams.
- Ports and addressing.
- TCP message framing.
- Delivery and ordering guarantees.
- Packet size and fragmentation.
- Application-level reliability.
- Connection handling.

TCP exposes a byte stream, not a sequence of application messages. The application must implement framing or use a protocol that defines it. UDP preserves datagram boundaries but does not guarantee delivery, ordering, or duplicate suppression.

### WebSocket
- Connection lifecycle.
- Bidirectional communication.
- Message-based protocols.
- JSON messages.
- Heartbeats and timeouts.
- Browser compatibility.
- Authentication considerations.

### Socket.IO
- Event-based communication.
- Rooms and broadcasts.
- Acknowledgements.
- Connection lifecycle.
- Protocol compatibility.

### RPC frameworks
- Remote invocation.
- Serialization.
- RPC direction.
- Ownership and authorization checks.
- Error handling and latency.

**Practical exercise:** Implement the same simple interaction using (1) a Unity networking framework and (2) a small custom client-server implementation using WebSocket or another appropriate transport. The goal is comparison, not a production-grade custom protocol.

## Lesson 5 — Protocol Design and Message Schemas

Topics:
- Message identifiers.
- Request identifiers.
- Player and session identifiers.
- Serialization formats.
- Message validation.
- Protocol versioning.
- Backward compatibility.
- Error responses.
- Sequence numbers and timestamps.
- Message size limits.

Example envelope:

```json
{
  "version": 1,
  "type": "player_input",
  "sequence": 184,
  "payload": {
    "direction": 1
  }
}
```

The format alone does not make the system secure. The server must validate the sender, message type, payload, sequence behavior, and permitted action.

**Practical exercise:** Document a protocol for joining a match, moving a paddle, reporting a score, and ending a match. For each message, define sender, recipient, purpose, validation, and delivery expectations.

## Lesson 6 — Dedicated Game Server Architecture

Topics:
- Headless execution.
- Dedicated server builds.
- Server-only initialization.
- Removing client-only dependencies.
- Server configuration and ports.
- Match isolation.
- Process lifecycle.
- Graceful shutdown.
- Logs and diagnostics.

**Practical exercise:** Run the Pong server separately from Unity clients. Start the server, connect players, finish a match, and shut it down cleanly.

## Lesson 7 — Building a Game Server in a Different Language

**Objective:** Show that the server language need not match the client language.

Use Go for a small independent game server:

```text
Unity Client
     │ WebSocket
     ▼
Go Game Server
     ├── Connection Management
     ├── Message Validation
     ├── Game State
     ├── Game Loop
     └── State Updates
```

Topics:
- Creating a network listener.
- Accepting connections.
- Managing connected clients.
- Serializing and parsing messages.
- Handling disconnections.
- Managing shared state safely.
- Running a game loop.
- Sending state updates.
- Cleaning up resources.

**Practical exercise:** Connect Unity clients to a Go server that manages player positions and scores for a simplified paddle game.

## Lesson 8 — Game Simulation, Ticks, and Authoritative State

Topics:
- Game loops.
- Fixed simulation steps.
- Tick rates.
- Processing inputs.
- Simulation order.
- State updates and snapshots.
- Determinism.
- State consistency.
- Separating simulation from network I/O.

Simplified loop:

```text
while server_is_running:
    receive_network_messages()
    validate_and_queue_inputs()
    advance_game_simulation()
    produce_state_updates()
    send_updates_to_clients()
```

Production implementations require careful scheduling, buffering, error handling, and resource management. Avoid executing arbitrary gameplay actions directly inside network callbacks; validate and queue inputs for controlled simulation processing.

**Practical exercise:** Move Pong's authoritative ball simulation into a fixed-step server loop and compare simulation frequency with client rendering frequency.

## Lesson 9 — Synchronization, Prediction, and Reconciliation

Topics:
- Authoritative snapshots.
- Interpolation and extrapolation.
- Client-side prediction.
- Server reconciliation.
- Input sequence numbers.
- Acknowledged inputs.
- Snapshot interpolation buffers.
- Clock synchronization.
- State correction.

**Practical exercise:** Introduce artificial latency, implement basic prediction and reconciliation, and compare it with server-only movement updates.

Advanced shooter-style lag compensation is reserved for the advanced course.

## Lesson 10 — Network Reliability and Performance

Topics:
- Bandwidth budgets.
- Packet frequency.
- Serialization overhead.
- Reliable and unreliable messages.
- Sequencing.
- Prioritizing updates.
- Delta-compression concepts.
- Interest management.
- Backpressure.
- Network profiling and load testing.

**Practical exercise:** Measure the data generated by a session and reduce unnecessary updates.

## Lesson 11 — Backend Integration and Player Identity

Topics:
- Authentication APIs and access tokens.
- Token validation.
- Player identity versus session identity.
- Game-server identity.
- Service-to-service authentication.
- Secure configuration.
- Player authorization.
- Persistent versus temporary state.

```text
Account Identity
      ↓
Backend Authentication
      ↓
Authenticated Player
      ↓
Match Assignment
      ↓
Game Server Connection
      ↓
Authorized Match Participation
```

A client claiming to be a particular player does not prove account ownership.

**Practical exercise:** Authenticate through a backend API, obtain a token, and use it to join a game server. Validate the token or verify identity through a trusted backend mechanism.

## Lesson 12 — Lobby and Matchmaking Implementation

Topics:
- Lobby versus match.
- Matchmaking queues.
- Player capacity and criteria.
- Session identifiers.
- Server availability.
- Match creation.
- Joining and leaving.
- Cancellation and disconnect handling.
- Timeouts.

Basic flow:
1. A player requests a match.
2. The service places them in a queue.
3. The service finds enough eligible players.
4. It creates a match.
5. It assigns a game server.
6. Players receive connection information.
7. Players connect to the server.

**Practical exercise:** Implement a two-player queue that creates a match when two players are waiting. A small implementation can select from a fixed list of available servers.

## Lesson 13 — Running Multiple Game Server Instances

Topics:
- One process per match.
- Multiple processes per machine.
- Port allocation.
- Resource limits.
- Match isolation.
- Process monitoring.
- Server registration.
- Health checks.
- Graceful termination.
- Capacity tracking.

**Practical exercise:** Run multiple server instances on one machine using separate ports and assign different matches to them.

## Lesson 14 — Deployment Fundamentals

Topics:
- Linux server basics.
- Process management.
- Network interfaces and ports.
- Firewall rules.
- Environment variables.
- Server configuration.
- Logs and restarts.
- Resource monitoring.
- Public versus private networking.

Distinguish between a process that is running, a server reachable over the network, and a server ready to accept matches.

**Practical exercise:** Deploy a Go or headless Unity server on Linux and connect clients from another network.

## Lesson 15 — Security and Abuse Prevention

Coordinate with the security programmer.

Topics:
- Trust boundaries.
- Authentication versus authorization.
- Message validation.
- Rate limiting.
- Replay and duplicate-request concerns.
- Invalid movement and forged score updates.
- Connection exhaustion.
- Denial-of-service fundamentals.
- Secret management.
- Suspicious-activity logging.

**Practical exercise:** Modify a client to submit invalid movement or score messages and demonstrate how the server rejects them.

## Lesson 16 — Intermediate Capstone

Students deliver:
- A working multiplayer game.
- A dedicated game server.
- A documented authority model.
- A documented message protocol.
- Basic matchmaking.
- Server-side match state.
- Persistent match results.
- Deployable server configuration.
- Basic logging and error handling.
- Demonstration of invalid-action rejection.

### Intermediate completion criteria

Students can:
- Implement a multiplayer server in a separate process.
- Implement basic authoritative simulation.
- Choose suitable communication mechanisms.
- Distinguish transport, protocol, and RPC abstractions.
- Connect Unity clients to a server in another language.
- Build basic matchmaking.
- Run multiple server instances.
- Deploy and troubleshoot on Linux.
- Integrate authentication and persistent backend services.

---

# Part III — Advanced Course
## Multiplayer Infrastructure: Distributed Systems, Orchestration, and Production Operations

**Objective:** Teach students to design, deploy, scale, and operate multiplayer infrastructure that manages many matches and server instances.

The advanced course should answer:
- How do we launch many matches without manually starting processes?
- How do we choose a machine for a new match?
- How do we recover when a server crashes?
- How do we prevent double allocation?
- How do we manage server versions and rolling deployments?
- How do we observe fleet performance?
- How do we scale without wasting resources?

**Suggested duration:** 18 lessons, approximately 2 hours each, plus a substantial team project.

## Lesson 1 — Production Multiplayer Architecture

Topics:
- Clients and dedicated servers.
- Authentication services.
- Matchmaking and allocation.
- Server registries.
- Databases and message queues.
- Telemetry and monitoring.
- Deployment infrastructure.
- Control plane versus gameplay data plane.

The **control plane** manages infrastructure and match lifecycle: matchmaking, allocation, deployment, and registration.

The **gameplay data plane** carries real-time traffic needed to execute matches, such as player inputs and state updates.

```text
                    CONTROL PLANE
         ┌──────────────────────────────┐
         │ Authentication              │
         │ Matchmaking                 │
         │ Server Allocation           │
         │ Fleet Management            │
         │ Deployment Control          │
         │ Monitoring                  │
         └──────────────┬───────────────┘
                        │
                 Lifecycle and
                 Match Assignment
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
     Game Server A  Game Server B  Game Server C
          ▲             ▲             ▲
          └─────────────┼─────────────┘
                        │
                  GAMEPLAY DATA
                        │
                 Player Connections
```

These are logical responsibilities, not necessarily separate networks or machines.

## Lesson 2 — Matchmaking Architecture and Algorithms

Topics:
- Queue-based matchmaking.
- Skill-based matchmaking.
- Region selection.
- Party matchmaking.
- Match size and game-mode constraints.
- Queue time versus match quality.
- Cancellation and repeated requests.
- Matchmaking state machines.
- Idempotency.
- Concurrency and race conditions.

Distinguish finding players, creating a match, allocating a server, and confirming readiness. These may be separate steps and services.

**Practical exercise:** Design matchmaking for multiple regions, game modes, and player counts. Explain how concurrent workers avoid processing the same queue entry incorrectly.

## Lesson 3 — Server Allocation and Fleet Management

Topics:
- Available capacity.
- Server registration.
- Allocation requests and leases.
- Heartbeats and health checks.
- Readiness.
- Timeouts and expired leases.
- Failed allocations.
- Idempotency.
- Race conditions.
- Orphaned server cleanup.

Example lifecycle:

```text
Starting → Ready → Allocated → Running → Cleaning → Stopped
```

A real system may also need failure, cancellation, draining, and recovery states.

**Practical exercise:** Build an allocator that prevents concurrent requests from allocating the same capacity twice.

## Lesson 4 — Containerization with Docker

Topics:
- Images and containers.
- Dockerfiles and multi-stage builds.
- Runtime configuration.
- Environment variables.
- Container networking and ports.
- Volumes and logs.
- Resource limits.
- Graceful shutdown.
- Image versioning and registries.
- Container health checks.

Docker packages and runs a server; it does not, by itself, provide a complete matchmaking and allocation system.

**Practical exercise:** Build a Docker image for the game server and run multiple instances with distinct configurations.

For Go, introduce multi-stage builds. For Unity, discuss headless builds, runtime dependencies, and image size.

## Lesson 5 — Container Orchestration

Topics:
- Containers versus orchestration.
- Desired state.
- Scheduling.
- Resource requests and limits.
- Health checks and restart policies.
- Scaling and rolling deployments.
- Service discovery.
- Node failure.
- Graceful termination.

Kubernetes manages workloads, but multiplayer games often need additional game-specific functionality for match allocation and lifecycle management.

**Practical exercise:** Deploy a small service and game-server workload to Kubernetes and observe what happens when an instance fails.

## Lesson 6 — Game-Specific Orchestration with Agones

Topics:
- GameServer resources.
- Fleet management.
- Allocation.
- Ready and allocated states.
- Health checks.
- Server lifecycle.
- Capacity scaling.
- Draining.
- Matchmaking integration.
- Operational trade-offs.

**Practical exercise:** Deploy a dedicated server through Agones, allocate it for a match, and observe its lifecycle.

Agones does not automatically implement game matchmaking rules, account systems, or gameplay protocols.

## Lesson 7 — Dynamic Server Allocation and Match Lifecycle

End-to-end flow:

```text
Player Requests Match
          ↓
   Matchmaking Queue
          ↓
    Match Created
          ↓
   Server Allocation
          ↓
    Server Becomes Ready
          ↓
   Connection Information
          ↓
      Players Connect
          ↓
       Match Runs
          ↓
      Match Finishes
          ↓
   Results Are Persisted
          ↓
   Server Is Reused or Stopped
```

Discuss no capacity, startup failure, expired allocation, client connection failure, server crash, result-submission failure, and incomplete cleanup.

**Practical exercise:** Implement the lifecycle with simulated allocation failures and retries.

## Lesson 8 — Multi-Region Infrastructure

Topics:
- Latency and geography.
- Region-aware matchmaking.
- Regional server fleets.
- Cross-region backend services.
- Regional capacity and failover.
- Data locality.
- Regional outages.
- Deployment consistency.

Region selection should consider measured latency, routing, availability, player population, and capacity—not just geographic distance.

**Practical exercise:** Extend matchmaking to choose between two simulated regions using latency and capacity information.

## Lesson 9 — Scaling Strategies and Capacity Planning

Topics:
- Concurrent players and matches.
- CPU and memory usage.
- Network bandwidth.
- Tick rate and entity count.
- Player density.
- Vertical versus horizontal scaling.
- Autoscaling.
- Warm capacity and cold-start time.
- Capacity reservations.
- Cost per match.

Scaling APIs is different from scaling game servers. Game servers continuously simulate matches and maintain real-time connections.

**Practical exercise:** Load-test the server, measure resource consumption, and estimate instances needed for a target number of concurrent matches. Base estimates on measurements rather than assumed player counts.

## Lesson 10 — Distributed Systems Fundamentals

Topics:
- Service boundaries.
- Network partitions.
- Timeouts and retries.
- Duplicate requests.
- Idempotency.
- Eventual consistency.
- Leases and heartbeats.
- Distributed locks and their limitations.
- Partial failures.
- State reconciliation.
- Consistency trade-offs.

**Practical exercise:** Simulate allocator failures and duplicate requests, then design recovery that avoids allocating the same capacity twice.

## Lesson 11 — Backend Service Architecture

Topics:
- Authentication and authorization.
- API gateways.
- Service-to-service communication.
- Database transactions.
- Caching.
- Message queues.
- Asynchronous processing.
- Event-driven architecture.
- Persistent match results.
- Schema evolution.
- Data consistency.

Discuss when a modular monolith is sufficient and when separate services are useful. Microservices are not a universal requirement; they introduce operational complexity.

**Practical exercise:** Design interactions between matchmaking, allocation, match results, and player progression.

## Lesson 12 — Persistent Data and Match-Result Integrity

Topics:
- Persistent versus ephemeral state.
- Transaction boundaries.
- Match-result submission.
- Duplicate-result prevention.
- Idempotency keys.
- Durable event processing.
- Retries and database failures.
- Recovery after a game-server crash.
- Auditing authoritative results.

**Practical exercise:** Implement match-result submission with duplicate protection and retry handling.

## Lesson 13 — Observability and Production Diagnostics

Topics:
- Structured logs.
- Metrics.
- Distributed tracing.
- Correlation IDs.
- Match and server-instance IDs.
- Connection metrics.
- Tick duration.
- Network traffic.
- Match and allocation failures.
- Disconnect rates.
- Resource utilization.

Distinguish:
- **Metrics:** How often or how much something happens.
- **Logs:** What happened during a specific execution.
- **Traces:** How a request or operation moves across services.

**Practical exercise:** Instrument matchmaking and allocation, then diagnose a deliberately introduced failure.

## Lesson 14 — Reliability and Incident Handling

Topics:
- Health versus readiness checks.
- Graceful shutdown.
- Rolling deployments and draining.
- Circuit breakers.
- Retry budgets.
- Backpressure.
- Overload protection.
- Crash recovery.
- Capacity exhaustion.
- Incident diagnosis.

Restarting a process does not automatically restore in-memory match state. Recovery may require snapshots or replicated state—or the match may need to be declared failed.

**Practical exercise:** Terminate a game server during a match and measure how the platform detects and handles the failure.

## Lesson 15 — Security at Infrastructure Scale

Coordinate with the security programmer.

Topics:
- Threat modeling.
- Network segmentation.
- Service identity.
- Secrets management.
- Authentication between services.
- Least privilege.
- Container security.
- Dependency management.
- Rate limiting and abuse detection.
- Resource exhaustion.
- Administrative access.
- Audit logging.

**Practical exercise:** Review the deployed architecture, identify trust boundaries, and correct deliberately introduced configuration problems.

## Lesson 16 — Performance Profiling and Optimization

Topics:
- CPU and memory profiling.
- Allocation and garbage collection.
- Tick-time analysis.
- Network profiling.
- Serialization costs.
- Lock contention.
- Connection limits.
- Database latency.
- Load testing.
- Capacity estimation.

Distinguish simulation performance from networking performance.

**Practical exercise:** Profile the game server under load, identify its largest bottleneck, and document a measurable improvement.

## Lesson 17 — Deployment Automation and Release Management

Topics:
- CI/CD.
- Automated builds.
- Versioned server images.
- Configuration management.
- Infrastructure as code.
- Environment separation.
- Staged deployment.
- Rolling updates and draining.
- Client-server compatibility.
- Rollback strategies.
- Release observability.

A successful container deployment does not guarantee that older clients can communicate with a newer server.

**Practical exercise:** Automate a new server-version deployment and verify that incompatible clients can be identified.

## Lesson 18 — Advanced Capstone

Students build and demonstrate a small multiplayer platform that automatically manages dedicated server instances.

### Game layer
- Multiplayer client.
- Dedicated authoritative server.
- Defined networking protocol.
- Match lifecycle.

### Backend layer
- Authentication or simplified trusted identity.
- Matchmaking.
- Server allocation.
- Match-result persistence.

### Infrastructure layer
- Docker images.
- Automated server deployment.
- Multiple server instances.
- Health monitoring.
- Server registration and cleanup.

### Operations layer
- Structured logs.
- Basic metrics.
- Load testing.
- Failure simulation.
- Recovery procedures.

Agones and Kubernetes can power the advanced implementation. A shorter version can use a custom allocator and container deployment without implementing every production feature.

### Advanced completion criteria

Students can:
- Design multiplayer infrastructure.
- Implement matchmaking and allocation with explicit state transitions.
- Manage multiple dedicated-server instances.
- Package and deploy servers with Docker.
- Explain orchestration and use a game-server orchestration system.
- Handle duplicate requests, failures, and allocation races.
- Measure capacity and performance.
- Design observability and incident-handling mechanisms.
- Automate deployment and explain compatibility concerns.
- Justify architecture using requirements and measured behavior.

---

# 5. Technology Strategy

Limit the number of technologies students must learn. Teach transferable concepts rather than memorizing several frameworks.

| Area | Beginner | Intermediate | Advanced |
|---|---|---|---|
| Game networking | Mirror or Unity Netcode | Framework plus custom server | Production networking architecture |
| Programming language | C# | C# and Go | Go, C#, infrastructure tooling |
| Transport | Framework-managed networking | WebSocket, TCP/UDP concepts, Socket.IO comparison | Protocol optimization and transport trade-offs |
| Backend | Architecture concepts | Go + Gin + REST | Service architecture and distributed systems |
| Database | Understand persistence | PostgreSQL or MySQL | Transactions, consistency, operational reliability |
| Dedicated servers | Understand the concept | Deploy a real server | Manage a fleet |
| Containers | Conceptual introduction | Optional Docker introduction | Docker and orchestration |
| Orchestration | Not required | Manual instance management | Agones and Kubernetes |
| Observability | Basic logs | Logs and basic metrics | Metrics, tracing, alerting, incident response |

Go is a practical primary language for independent game servers and backend services because the server does not need to run inside Unity. However, do not teach raw TCP, UDP, WebSocket, Socket.IO, and RPC as five equivalent technologies: they operate at different abstraction levels.

# 6. How the Courses Differ in Practice

### Beginner — Understand and Implement

Students receive a working game and learn to make it multiplayer.

**Deliverable:** A multiplayer Pong game.

### Intermediate — Design and Integrate

Students make architectural decisions, implement an independent game server, integrate backend services, and deploy the game.

**Deliverable:** A multiplayer game with an authoritative dedicated server, basic matchmaking, and persistent match results.

### Advanced — Operate and Scale

Students manage server instances, automate deployments, handle failures, measure capacity, and design infrastructure.

**Deliverable:** A multiplayer platform that automatically allocates and manages dedicated game servers.

**The intermediate course builds the game server; the advanced course builds the infrastructure that manages game servers.**
# Multiplayer Game Development Curriculum
## Beginner → Intermediate → Advanced

## Purpose and Scope

This curriculum teaches **multiplayer game development as a discipline separate from making games in Unity**.

The objective is to teach students how to make an existing game work across multiple computers, design networking architecture, and eventually operate multiplayer games in production.

- **Beginner:** How does a multiplayer game work?
- **Intermediate:** How do we build, secure, and deploy a multiplayer game?
- **Advanced:** How do we design, scale, orchestrate, and operate multiplayer infrastructure?

Each course produces a working project. The projects build on one another so students learn a coherent system rather than disconnected technologies.

### Course overview

| Dimension | Beginner | Intermediate | Advanced |
|---|---|---|---|
| Primary objective | Understand multiplayer fundamentals | Engineer a complete multiplayer system | Design and operate production infrastructure |
| Focus | Concepts, networking models, basic implementation | Authority, protocols, game servers, matchmaking | Architecture, orchestration, scaling, reliability |
| Game development | Assumed prerequisite | Assumed prerequisite | Assumed prerequisite |
| Networking | Core concepts and basic synchronization | Deep implementation and trade-offs | Performance, scaling, failure handling |
| Server architecture | Introduction | Design and deployment | Distributed infrastructure |
| Backend APIs | Conceptual introduction | Implementation and integration | Service architecture and operations |
| Matchmaking | Conceptual introduction | Functional matchmaking | Allocation, orchestration, scaling |
| Deployment | Run local processes | Deploy a dedicated server | Docker, orchestration, automated lifecycle management |
| Security | Trust boundaries and basic validation | Authority enforcement and abuse prevention | Infrastructure security and operational controls |
| Final project | Multiplayer paddle game | Networked game with dedicated server and matchmaking | Multiplayer platform with automated game-server management |

### Prerequisites and teaching boundary

Students should already understand basic programming and have completed an introductory game-development course. This course is **not a second Unity course**.

Saber owns local game mechanics: movement, shooting, animation, physics, UI, and core gameplay. This course owns networking, multiplayer architecture, authority, game servers, and online services. The security programmer contributes threat modeling and security engineering.

Use a working single-player paddle game supplied by the game-development course. Students modify only the code necessary to introduce networking.

---

# Part I — Beginner Course
## Multiplayer Fundamentals: From Local Game to Multiplayer Game

**Objective:** Students understand the architecture, terminology, communication patterns, and fundamental problems of multiplayer games. They finish by implementing a simple networked paddle game.

**Suggested duration:** 12 lessons, approximately 2 hours each, plus independent practice.

The beginner course should broadly cover the multiplayer landscape, but implement only a small, coherent subset.

## Lesson 1 — What Actually Makes a Game Multiplayer?

**Objective:** Build a correct mental model before introducing networking code.

Topics:
- Local multiplayer versus online multiplayer.
- Client, server, network protocol, and game state.
- Why two independent game simulations drift apart.
- Game logic versus networking.
- Authority versus synchronization.
- Why multiplayer requires coordination.

Example architecture:

```text
Player A Client ── inputs ──┐
                            ▼
                     Authoritative
                      Game Server
                            ▲
Player B Client ── inputs ──┘
                            │
                      State updates
                       to clients
```

The server maintains the authoritative state in this example. Real systems may send different updates to different clients and need not transmit the entire state every time.

**Practical exercise:** Draw the architecture of a two-player game and identify what information must be shared.

**Expected outcome:** Students understand why multiplayer requires communication and coordinated state.

## Lesson 2 — Multiplayer Architectures and Networking Terminology

**Objective:** Understand the major architectural models before choosing an implementation.

Topics:
- Client-server architecture.
- Dedicated servers.
- Listen servers and player-hosted games.
- Peer-to-peer networking.
- Relays.
- Authoritative versus non-authoritative systems.
- Centralized versus distributed responsibilities.
- Game server versus backend service.

Architectures to compare:

```text
Listen server:
Player A [Client + Server] ───── Player B [Client]

Dedicated server:
Player A [Client] ───── [Dedicated Server] ───── Player B [Client]

Peer-to-peer:
Player A [Peer] ◄────────► Player B [Peer]

Relay-based:
Player A [Client] ───── [Relay] ───── Player B [Client]
```

A relay forwards traffic and does not necessarily simulate the game. Peer-to-peer systems may still assign special authority to one peer.

Compare cost, connectivity, trust, complexity, and failure modes.

**Practical exercise:** Choose an architecture for a cooperative game, a competitive shooter, and a small browser game. Justify each choice.

## Lesson 3 — Network Communication: Messages, Protocols, and Transports

**Objective:** Understand how information moves between machines.

Topics:
- Messages and serialization.
- JSON and binary serialization.
- Message schemas and versions.
- TCP and UDP.
- HTTP and WebSocket.
- Networking frameworks such as Mirror and Unity Netcode for GameObjects.
- Socket.IO.
- RPC (remote procedure call).

| Technology | What it provides | Typical use |
|---|---|---|
| TCP | Reliable, ordered byte stream | Many application protocols |
| UDP | Datagram transport without built-in delivery guarantees | Real-time networking protocols |
| HTTP | Request-response application protocol | Authentication, profiles, APIs |
| WebSocket | Persistent bidirectional communication over a connection | Browser-based real-time applications |
| Socket.IO | Higher-level event-based communication with its own protocol | Event-driven real-time applications |
| RPC | An abstraction for invoking remote operations | Framework-dependent remote calls |

WebSocket is not a replacement for understanding TCP or UDP. Socket.IO clients are not automatically compatible with ordinary WebSocket servers. RPC is an abstraction, not a transport.

**Practical exercise:** Classify example messages as player input, state update, event, or request-response operation.

## Lesson 4 — Game State and Synchronization

**Objective:** Understand what must be synchronized and why.

Topics:
- Authoritative game state.
- State versus event versus input versus command.
- Snapshots.
- State replication.
- State ownership.
- Update frequency.
- Full snapshots versus incremental updates.
- Initial synchronization.
- Late-joining players.

Example state:

```text
MatchState
├── Ball: Position, Velocity
├── Player A: Paddle Position, Score
├── Player B: Paddle Position, Score
└── Match Status: Playing / Finished
```

Not everything needs continuous synchronization. Paddle positions may need frequent updates, while match-start events happen only once.

**Practical exercise:** Decide which Pong values need synchronization, how often they should be sent, and which events should be sent only when something happens.

## Lesson 5 — Client-Server Interaction Patterns

**Objective:** Learn how clients and servers communicate at the application level.

Patterns:
1. Request-response.
2. Command and authoritative result.
3. Server-to-client notification.
4. Continuous state updates.

```text
Request-response:
Client ── Request ──► Server
Client ◄─ Response ── Server

Command:
Client ── Input/Command ──► Server
Client ◄── Accepted State ── Server

Broadcast:
Server ── Event ──► Client A
Server ── Event ──► Client B
```

Introduce RPCs without making the lesson a framework-specific API tutorial. Explain that a command expresses an intention and may be rejected; an event describes something that has occurred.

**Practical exercise:** Design the messages needed to start a match, move a paddle, score a point, and end a match.

## Lesson 6 — Authority, Ownership, and Trust

**Objective:** Understand who is allowed to decide what happens.

Topics:
- Client authority.
- Server authority.
- Object ownership.
- Input authority.
- State authority.
- Trust boundaries.
- Server-side validation.
- Why client-side validation is insufficient for competitive games.

Core principle: **Clients communicate intent; the authority responsible for a piece of game state determines whether a requested change is valid.**

Authority can be assigned per piece of state or operation. Not every system must use the same authority model.

**Practical exercise:** Let a client update its own score, demonstrate the exploit, then redesign the system so the server owns the score.

## Lesson 7 — Time, Latency, and Real-Time Constraints

**Objective:** Understand why networked games behave differently from local games.

Topics:
- Latency and round-trip time (RTT).
- Jitter and packet loss.
- Bandwidth.
- Update frequency.
- Simulation tick rate versus frame rate.
- Clock differences.
- Interpolation.
- Extrapolation.
- Prediction.
- Reconciliation.

A network message can arrive late or be lost, depending on the transport and protocol. Increasing update frequency alone does not solve every latency problem.

**Practical exercise:** Add artificial delay to Pong and observe the effects on paddle movement and ball synchronization.

## Lesson 8 — Player Lifecycle and Match Lifecycle

**Objective:** Understand the lifecycle of players and matches.

Topics:
- Connecting and disconnecting.
- Authentication as a concept.
- Joining a session.
- Spawning a player.
- Leaving and reconnecting.
- Unexpected disconnections.
- Match start and end.
- Late joining.
- Session cleanup.
- Lobby versus match.

Example lifecycle:

```text
Waiting for Players
        ↓
   Match Starting
        ↓
     In Progress
        ↓
    Match Finished
        ↓
      Cleaned Up
```

**Practical exercise:** Implement the lifecycle and test what happens when a player disconnects during each phase.

## Lesson 9 — Game Server versus Backend Services

**Objective:** Assign responsibilities to the right components.

Topics:
- Game server processes.
- Backend APIs.
- Authentication services.
- Databases.
- Matchmaking.
- Server allocation.
- Logging and monitoring.

```text
                 GAME BACKEND
          ┌────────────────────┐
          │ Accounts           │
          │ Matchmaking        │
          │ Persistent Data    │
          └─────────┬──────────┘
                    │ HTTPS / API
                    ▼
               GAME SERVER
          ┌────────────────────┐
          │ Match State        │
          │ Game Rules         │
          │ Scores             │
          │ Player Actions     │
          └─────────┬──────────┘
                    │ Real-time networking
              ┌─────┴─────┐
              ▼           ▼
           Client A    Client B
```

These are logical responsibilities, not necessarily separate machines. A small project may combine components; larger games may deploy them independently.

**Practical exercise:** Assign a list of responsibilities to a client, game server, or backend service.

## Lesson 10 — Network Performance and Reliability Fundamentals

**Objective:** Introduce constraints of real-time networked systems.

Topics:
- Bandwidth versus latency.
- Message frequency and payload size.
- Reliable versus unreliable delivery.
- Ordered versus unordered delivery.
- Lost and duplicate messages.
- Timeouts.
- Basic network debugging.
- Message freshness.

Examples:
- Paddle position: freshness may matter more than delivering every update.
- Match-ending result: should not be silently lost.
- Chat: reliable delivery is generally desirable.
- Purchase transaction: requires reliable processing and duplicate protection.

**Practical exercise:** Classify messages by delivery requirements and explain the trade-offs.

## Lesson 11 — Build the Multiplayer Pong Game

**Objective:** Apply the fundamentals in one complete project.

Students implement:
- A server process or host.
- Client connections.
- Player registration within a match.
- Paddle input messages.
- Authoritative ball movement.
- Collision handling.
- Score calculation.
- State synchronization.
- Match start and end.
- Disconnection handling.

Use an existing Unity networking framework rather than requiring students to implement raw TCP or UDP from scratch.

**Practical exercise:** Build and test a two-player game in which the server advances the authoritative simulation and clients send input and render the resulting state.

## Lesson 12 — Testing, Debugging, and Architectural Review

**Objective:** Ensure students understand what they built.

Topics:
- Testing multiple clients.
- Testing across separate computers.
- Network logs.
- Connection failures.
- Simulated latency.
- Player disconnections.
- Authority bugs.
- Synchronization bugs.
- Basic bandwidth and update-frequency analysis.

**Final assessment:** Each student must explain:
1. Where the authoritative game state lives.
2. How player input reaches the server.
3. How the server updates the game.
4. How clients learn about state changes.
5. What happens when a client disconnects.
6. What must change to deploy a dedicated server.

### Beginner completion criteria

Students can:
- Explain client-server architecture and alternative models.
- Distinguish gameplay networking from backend APIs.
- Explain authority, ownership, and state synchronization.
- Describe TCP, UDP, HTTP, WebSocket, and RPC at a conceptual level.
- Explain latency, jitter, packet loss, and tick rate.
- Build and test a basic multiplayer game.
- Explain the path from a locally hosted prototype to dedicated-server deployment.

---

# Part II — Intermediate Course
## Multiplayer Engineering: Authority, Protocols, Game Servers, and Matchmaking

**Objective:** Students design and implement a complete multiplayer system, choose communication mechanisms, run a dedicated game server, connect it to backend services, and implement basic matchmaking.

**Suggested duration:** 16 lessons, approximately 2 hours each, plus project work.

## Lesson 1 — Review and Redesign the Beginner Architecture

Topics:
- Review the Pong architecture.
- Identify authoritative state.
- Reduce unnecessary network traffic.
- Separate game simulation from rendering.
- Separate transport, protocol, and game logic.
- Define module boundaries.
- Choose where validation belongs.

**Practical exercise:** Refactor the project to separate message handling, simulation, and presentation.

## Lesson 2 — Authority Models in Depth

Topics:
- Server, client, and distributed authority.
- Object ownership.
- Input authority versus state authority.
- Authority transfer.
- Host advantage.
- State validation.
- Trust boundaries.
- Security implications.

Examples:

| Game mechanic | Recommended starting model |
|---|---|
| Competitive shooting | Server-authoritative hit and damage decisions |
| Player movement | Client input, server validation, authoritative state |
| Cosmetic effects | Often client-side presentation |
| Inventory ownership | Authoritative backend or game server, depending on operation |
| Match score | Authoritative game server |
| Player camera | Usually local to the player |
| Cooperative puzzle object | Depends on game; server authority is a straightforward baseline |

**Practical exercise:** Demonstrate a manipulable client-authoritative score, then make the score server-authoritative.

## Lesson 3 — Client-Server Interaction Patterns in Depth

Topics:
- Requests and responses.
- Commands and RPCs.
- Server-to-client and client-to-server messages.
- Broadcasts and targeted messages.
- State replication.
- Event-driven communication.
- Streaming updates.
- Acknowledgements and timeouts.
- Idempotency and duplicate messages.

A command expresses intent:

```text
FireWeapon(weapon_id, aim_direction)
```

An event describes what occurred:

```text
WeaponFired(player_id, weapon_id)
```

The server may reject a command. The corresponding event should only be emitted if the action actually occurs according to the game rules.

**Practical exercise:** Design communication flows for shooting, damage, item collection, and match completion.

## Lesson 4 — Networking Protocols and Transport Selection

Topics:

### TCP and UDP sockets
- TCP connection lifecycle.
- UDP datagrams.
- Ports and addressing.
- TCP message framing.
- Delivery and ordering guarantees.
- Packet size and fragmentation.
- Application-level reliability.
- Connection handling.

TCP exposes a byte stream, not a sequence of application messages. The application must implement framing or use a protocol that defines it. UDP preserves datagram boundaries but does not guarantee delivery, ordering, or duplicate suppression.

### WebSocket
- Connection lifecycle.
- Bidirectional communication.
- Message-based protocols.
- JSON messages.
- Heartbeats and timeouts.
- Browser compatibility.
- Authentication considerations.

### Socket.IO
- Event-based communication.
- Rooms and broadcasts.
- Acknowledgements.
- Connection lifecycle.
- Protocol compatibility.

### RPC frameworks
- Remote invocation.
- Serialization.
- RPC direction.
- Ownership and authorization checks.
- Error handling and latency.

**Practical exercise:** Implement the same simple interaction using (1) a Unity networking framework and (2) a small custom client-server implementation using WebSocket or another appropriate transport. The goal is comparison, not a production-grade custom protocol.

## Lesson 5 — Protocol Design and Message Schemas

Topics:
- Message identifiers.
- Request identifiers.
- Player and session identifiers.
- Serialization formats.
- Message validation.
- Protocol versioning.
- Backward compatibility.
- Error responses.
- Sequence numbers and timestamps.
- Message size limits.

Example envelope:

```json
{
  "version": 1,
  "type": "player_input",
  "sequence": 184,
  "payload": {
    "direction": 1
  }
}
```

The format alone does not make the system secure. The server must validate the sender, message type, payload, sequence behavior, and permitted action.

**Practical exercise:** Document a protocol for joining a match, moving a paddle, reporting a score, and ending a match. For each message, define sender, recipient, purpose, validation, and delivery expectations.

## Lesson 6 — Dedicated Game Server Architecture

Topics:
- Headless execution.
- Dedicated server builds.
- Server-only initialization.
- Removing client-only dependencies.
- Server configuration and ports.
- Match isolation.
- Process lifecycle.
- Graceful shutdown.
- Logs and diagnostics.

**Practical exercise:** Run the Pong server separately from Unity clients. Start the server, connect players, finish a match, and shut it down cleanly.

## Lesson 7 — Building a Game Server in a Different Language

**Objective:** Show that the server language need not match the client language.

Use Go for a small independent game server:

```text
Unity Client
     │ WebSocket
     ▼
Go Game Server
     ├── Connection Management
     ├── Message Validation
     ├── Game State
     ├── Game Loop
     └── State Updates
```

Topics:
- Creating a network listener.
- Accepting connections.
- Managing connected clients.
- Serializing and parsing messages.
- Handling disconnections.
- Managing shared state safely.
- Running a game loop.
- Sending state updates.
- Cleaning up resources.

**Practical exercise:** Connect Unity clients to a Go server that manages player positions and scores for a simplified paddle game.

## Lesson 8 — Game Simulation, Ticks, and Authoritative State

Topics:
- Game loops.
- Fixed simulation steps.
- Tick rates.
- Processing inputs.
- Simulation order.
- State updates and snapshots.
- Determinism.
- State consistency.
- Separating simulation from network I/O.

Simplified loop:

```text
while server_is_running:
    receive_network_messages()
    validate_and_queue_inputs()
    advance_game_simulation()
    produce_state_updates()
    send_updates_to_clients()
```

Production implementations require careful scheduling, buffering, error handling, and resource management. Avoid executing arbitrary gameplay actions directly inside network callbacks; validate and queue inputs for controlled simulation processing.

**Practical exercise:** Move Pong's authoritative ball simulation into a fixed-step server loop and compare simulation frequency with client rendering frequency.

## Lesson 9 — Synchronization, Prediction, and Reconciliation

Topics:
- Authoritative snapshots.
- Interpolation and extrapolation.
- Client-side prediction.
- Server reconciliation.
- Input sequence numbers.
- Acknowledged inputs.
- Snapshot interpolation buffers.
- Clock synchronization.
- State correction.

**Practical exercise:** Introduce artificial latency, implement basic prediction and reconciliation, and compare it with server-only movement updates.

Advanced shooter-style lag compensation is reserved for the advanced course.

## Lesson 10 — Network Reliability and Performance

Topics:
- Bandwidth budgets.
- Packet frequency.
- Serialization overhead.
- Reliable and unreliable messages.
- Sequencing.
- Prioritizing updates.
- Delta-compression concepts.
- Interest management.
- Backpressure.
- Network profiling and load testing.

**Practical exercise:** Measure the data generated by a session and reduce unnecessary updates.

## Lesson 11 — Backend Integration and Player Identity

Topics:
- Authentication APIs and access tokens.
- Token validation.
- Player identity versus session identity.
- Game-server identity.
- Service-to-service authentication.
- Secure configuration.
- Player authorization.
- Persistent versus temporary state.

```text
Account Identity
      ↓
Backend Authentication
      ↓
Authenticated Player
      ↓
Match Assignment
      ↓
Game Server Connection
      ↓
Authorized Match Participation
```

A client claiming to be a particular player does not prove account ownership.

**Practical exercise:** Authenticate through a backend API, obtain a token, and use it to join a game server. Validate the token or verify identity through a trusted backend mechanism.

## Lesson 12 — Lobby and Matchmaking Implementation

Topics:
- Lobby versus match.
- Matchmaking queues.
- Player capacity and criteria.
- Session identifiers.
- Server availability.
- Match creation.
- Joining and leaving.
- Cancellation and disconnect handling.
- Timeouts.

Basic flow:
1. A player requests a match.
2. The service places them in a queue.
3. The service finds enough eligible players.
4. It creates a match.
5. It assigns a game server.
6. Players receive connection information.
7. Players connect to the server.

**Practical exercise:** Implement a two-player queue that creates a match when two players are waiting. A small implementation can select from a fixed list of available servers.

## Lesson 13 — Running Multiple Game Server Instances

Topics:
- One process per match.
- Multiple processes per machine.
- Port allocation.
- Resource limits.
- Match isolation.
- Process monitoring.
- Server registration.
- Health checks.
- Graceful termination.
- Capacity tracking.

**Practical exercise:** Run multiple server instances on one machine using separate ports and assign different matches to them.

## Lesson 14 — Deployment Fundamentals

Topics:
- Linux server basics.
- Process management.
- Network interfaces and ports.
- Firewall rules.
- Environment variables.
- Server configuration.
- Logs and restarts.
- Resource monitoring.
- Public versus private networking.

Distinguish between a process that is running, a server reachable over the network, and a server ready to accept matches.

**Practical exercise:** Deploy a Go or headless Unity server on Linux and connect clients from another network.

## Lesson 15 — Security and Abuse Prevention

Coordinate with the security programmer.

Topics:
- Trust boundaries.
- Authentication versus authorization.
- Message validation.
- Rate limiting.
- Replay and duplicate-request concerns.
- Invalid movement and forged score updates.
- Connection exhaustion.
- Denial-of-service fundamentals.
- Secret management.
- Suspicious-activity logging.

**Practical exercise:** Modify a client to submit invalid movement or score messages and demonstrate how the server rejects them.

## Lesson 16 — Intermediate Capstone

Students deliver:
- A working multiplayer game.
- A dedicated game server.
- A documented authority model.
- A documented message protocol.
- Basic matchmaking.
- Server-side match state.
- Persistent match results.
- Deployable server configuration.
- Basic logging and error handling.
- Demonstration of invalid-action rejection.

### Intermediate completion criteria

Students can:
- Implement a multiplayer server in a separate process.
- Implement basic authoritative simulation.
- Choose suitable communication mechanisms.
- Distinguish transport, protocol, and RPC abstractions.
- Connect Unity clients to a server in another language.
- Build basic matchmaking.
- Run multiple server instances.
- Deploy and troubleshoot on Linux.
- Integrate authentication and persistent backend services.

---

# Part III — Advanced Course
## Multiplayer Infrastructure: Distributed Systems, Orchestration, and Production Operations

**Objective:** Teach students to design, deploy, scale, and operate multiplayer infrastructure that manages many matches and server instances.

The advanced course should answer:
- How do we launch many matches without manually starting processes?
- How do we choose a machine for a new match?
- How do we recover when a server crashes?
- How do we prevent double allocation?
- How do we manage server versions and rolling deployments?
- How do we observe fleet performance?
- How do we scale without wasting resources?

**Suggested duration:** 18 lessons, approximately 2 hours each, plus a substantial team project.

## Lesson 1 — Production Multiplayer Architecture

Topics:
- Clients and dedicated servers.
- Authentication services.
- Matchmaking and allocation.
- Server registries.
- Databases and message queues.
- Telemetry and monitoring.
- Deployment infrastructure.
- Control plane versus gameplay data plane.

The **control plane** manages infrastructure and match lifecycle: matchmaking, allocation, deployment, and registration.

The **gameplay data plane** carries real-time traffic needed to execute matches, such as player inputs and state updates.

```text
                    CONTROL PLANE
         ┌──────────────────────────────┐
         │ Authentication              │
         │ Matchmaking                 │
         │ Server Allocation           │
         │ Fleet Management            │
         │ Deployment Control          │
         │ Monitoring                  │
         └──────────────┬───────────────┘
                        │
                 Lifecycle and
                 Match Assignment
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
     Game Server A  Game Server B  Game Server C
          ▲             ▲             ▲
          └─────────────┼─────────────┘
                        │
                  GAMEPLAY DATA
                        │
                 Player Connections
```

These are logical responsibilities, not necessarily separate networks or machines.

## Lesson 2 — Matchmaking Architecture and Algorithms

Topics:
- Queue-based matchmaking.
- Skill-based matchmaking.
- Region selection.
- Party matchmaking.
- Match size and game-mode constraints.
- Queue time versus match quality.
- Cancellation and repeated requests.
- Matchmaking state machines.
- Idempotency.
- Concurrency and race conditions.

Distinguish finding players, creating a match, allocating a server, and confirming readiness. These may be separate steps and services.

**Practical exercise:** Design matchmaking for multiple regions, game modes, and player counts. Explain how concurrent workers avoid processing the same queue entry incorrectly.

## Lesson 3 — Server Allocation and Fleet Management

Topics:
- Available capacity.
- Server registration.
- Allocation requests and leases.
- Heartbeats and health checks.
- Readiness.
- Timeouts and expired leases.
- Failed allocations.
- Idempotency.
- Race conditions.
- Orphaned server cleanup.

Example lifecycle:

```text
Starting → Ready → Allocated → Running → Cleaning → Stopped
```

A real system may also need failure, cancellation, draining, and recovery states.

**Practical exercise:** Build an allocator that prevents concurrent requests from allocating the same capacity twice.

## Lesson 4 — Containerization with Docker

Topics:
- Images and containers.
- Dockerfiles and multi-stage builds.
- Runtime configuration.
- Environment variables.
- Container networking and ports.
- Volumes and logs.
- Resource limits.
- Graceful shutdown.
- Image versioning and registries.
- Container health checks.

Docker packages and runs a server; it does not, by itself, provide a complete matchmaking and allocation system.

**Practical exercise:** Build a Docker image for the game server and run multiple instances with distinct configurations.

For Go, introduce multi-stage builds. For Unity, discuss headless builds, runtime dependencies, and image size.

## Lesson 5 — Container Orchestration

Topics:
- Containers versus orchestration.
- Desired state.
- Scheduling.
- Resource requests and limits.
- Health checks and restart policies.
- Scaling and rolling deployments.
- Service discovery.
- Node failure.
- Graceful termination.

Kubernetes manages workloads, but multiplayer games often need additional game-specific functionality for match allocation and lifecycle management.

**Practical exercise:** Deploy a small service and game-server workload to Kubernetes and observe what happens when an instance fails.

## Lesson 6 — Game-Specific Orchestration with Agones

Topics:
- GameServer resources.
- Fleet management.
- Allocation.
- Ready and allocated states.
- Health checks.
- Server lifecycle.
- Capacity scaling.
- Draining.
- Matchmaking integration.
- Operational trade-offs.

**Practical exercise:** Deploy a dedicated server through Agones, allocate it for a match, and observe its lifecycle.

Agones does not automatically implement game matchmaking rules, account systems, or gameplay protocols.

## Lesson 7 — Dynamic Server Allocation and Match Lifecycle

End-to-end flow:

```text
Player Requests Match
          ↓
   Matchmaking Queue
          ↓
    Match Created
          ↓
   Server Allocation
          ↓
    Server Becomes Ready
          ↓
   Connection Information
          ↓
      Players Connect
          ↓
       Match Runs
          ↓
      Match Finishes
          ↓
   Results Are Persisted
          ↓
   Server Is Reused or Stopped
```

Discuss no capacity, startup failure, expired allocation, client connection failure, server crash, result-submission failure, and incomplete cleanup.

**Practical exercise:** Implement the lifecycle with simulated allocation failures and retries.

## Lesson 8 — Multi-Region Infrastructure

Topics:
- Latency and geography.
- Region-aware matchmaking.
- Regional server fleets.
- Cross-region backend services.
- Regional capacity and failover.
- Data locality.
- Regional outages.
- Deployment consistency.

Region selection should consider measured latency, routing, availability, player population, and capacity—not just geographic distance.

**Practical exercise:** Extend matchmaking to choose between two simulated regions using latency and capacity information.

## Lesson 9 — Scaling Strategies and Capacity Planning

Topics:
- Concurrent players and matches.
- CPU and memory usage.
- Network bandwidth.
- Tick rate and entity count.
- Player density.
- Vertical versus horizontal scaling.
- Autoscaling.
- Warm capacity and cold-start time.
- Capacity reservations.
- Cost per match.

Scaling APIs is different from scaling game servers. Game servers continuously simulate matches and maintain real-time connections.

**Practical exercise:** Load-test the server, measure resource consumption, and estimate instances needed for a target number of concurrent matches. Base estimates on measurements rather than assumed player counts.

## Lesson 10 — Distributed Systems Fundamentals

Topics:
- Service boundaries.
- Network partitions.
- Timeouts and retries.
- Duplicate requests.
- Idempotency.
- Eventual consistency.
- Leases and heartbeats.
- Distributed locks and their limitations.
- Partial failures.
- State reconciliation.
- Consistency trade-offs.

**Practical exercise:** Simulate allocator failures and duplicate requests, then design recovery that avoids allocating the same capacity twice.

## Lesson 11 — Backend Service Architecture

Topics:
- Authentication and authorization.
- API gateways.
- Service-to-service communication.
- Database transactions.
- Caching.
- Message queues.
- Asynchronous processing.
- Event-driven architecture.
- Persistent match results.
- Schema evolution.
- Data consistency.

Discuss when a modular monolith is sufficient and when separate services are useful. Microservices are not a universal requirement; they introduce operational complexity.

**Practical exercise:** Design interactions between matchmaking, allocation, match results, and player progression.

## Lesson 12 — Persistent Data and Match-Result Integrity

Topics:
- Persistent versus ephemeral state.
- Transaction boundaries.
- Match-result submission.
- Duplicate-result prevention.
- Idempotency keys.
- Durable event processing.
- Retries and database failures.
- Recovery after a game-server crash.
- Auditing authoritative results.

**Practical exercise:** Implement match-result submission with duplicate protection and retry handling.

## Lesson 13 — Observability and Production Diagnostics

Topics:
- Structured logs.
- Metrics.
- Distributed tracing.
- Correlation IDs.
- Match and server-instance IDs.
- Connection metrics.
- Tick duration.
- Network traffic.
- Match and allocation failures.
- Disconnect rates.
- Resource utilization.

Distinguish:
- **Metrics:** How often or how much something happens.
- **Logs:** What happened during a specific execution.
- **Traces:** How a request or operation moves across services.

**Practical exercise:** Instrument matchmaking and allocation, then diagnose a deliberately introduced failure.

## Lesson 14 — Reliability and Incident Handling

Topics:
- Health versus readiness checks.
- Graceful shutdown.
- Rolling deployments and draining.
- Circuit breakers.
- Retry budgets.
- Backpressure.
- Overload protection.
- Crash recovery.
- Capacity exhaustion.
- Incident diagnosis.

Restarting a process does not automatically restore in-memory match state. Recovery may require snapshots or replicated state—or the match may need to be declared failed.

**Practical exercise:** Terminate a game server during a match and measure how the platform detects and handles the failure.

## Lesson 15 — Security at Infrastructure Scale

Coordinate with the security programmer.

Topics:
- Threat modeling.
- Network segmentation.
- Service identity.
- Secrets management.
- Authentication between services.
- Least privilege.
- Container security.
- Dependency management.
- Rate limiting and abuse detection.
- Resource exhaustion.
- Administrative access.
- Audit logging.

**Practical exercise:** Review the deployed architecture, identify trust boundaries, and correct deliberately introduced configuration problems.

## Lesson 16 — Performance Profiling and Optimization

Topics:
- CPU and memory profiling.
- Allocation and garbage collection.
- Tick-time analysis.
- Network profiling.
- Serialization costs.
- Lock contention.
- Connection limits.
- Database latency.
- Load testing.
- Capacity estimation.

Distinguish simulation performance from networking performance.

**Practical exercise:** Profile the game server under load, identify its largest bottleneck, and document a measurable improvement.

## Lesson 17 — Deployment Automation and Release Management

Topics:
- CI/CD.
- Automated builds.
- Versioned server images.
- Configuration management.
- Infrastructure as code.
- Environment separation.
- Staged deployment.
- Rolling updates and draining.
- Client-server compatibility.
- Rollback strategies.
- Release observability.

A successful container deployment does not guarantee that older clients can communicate with a newer server.

**Practical exercise:** Automate a new server-version deployment and verify that incompatible clients can be identified.

## Lesson 18 — Advanced Capstone

Students build and demonstrate a small multiplayer platform that automatically manages dedicated server instances.

### Game layer
- Multiplayer client.
- Dedicated authoritative server.
- Defined networking protocol.
- Match lifecycle.

### Backend layer
- Authentication or simplified trusted identity.
- Matchmaking.
- Server allocation.
- Match-result persistence.

### Infrastructure layer
- Docker images.
- Automated server deployment.
- Multiple server instances.
- Health monitoring.
- Server registration and cleanup.

### Operations layer
- Structured logs.
- Basic metrics.
- Load testing.
- Failure simulation.
- Recovery procedures.

Agones and Kubernetes can power the advanced implementation. A shorter version can use a custom allocator and container deployment without implementing every production feature.

### Advanced completion criteria

Students can:
- Design multiplayer infrastructure.
- Implement matchmaking and allocation with explicit state transitions.
- Manage multiple dedicated-server instances.
- Package and deploy servers with Docker.
- Explain orchestration and use a game-server orchestration system.
- Handle duplicate requests, failures, and allocation races.
- Measure capacity and performance.
- Design observability and incident-handling mechanisms.
- Automate deployment and explain compatibility concerns.
- Justify architecture using requirements and measured behavior.

---

# 5. Technology Strategy

Limit the number of technologies students must learn. Teach transferable concepts rather than memorizing several frameworks.

| Area | Beginner | Intermediate | Advanced |
|---|---|---|---|
| Game networking | Mirror or Unity Netcode | Framework plus custom server | Production networking architecture |
| Programming language | C# | C# and Go | Go, C#, infrastructure tooling |
| Transport | Framework-managed networking | WebSocket, TCP/UDP concepts, Socket.IO comparison | Protocol optimization and transport trade-offs |
| Backend | Architecture concepts | Go + Gin + REST | Service architecture and distributed systems |
| Database | Understand persistence | PostgreSQL or MySQL | Transactions, consistency, operational reliability |
| Dedicated servers | Understand the concept | Deploy a real server | Manage a fleet |
| Containers | Conceptual introduction | Optional Docker introduction | Docker and orchestration |
| Orchestration | Not required | Manual instance management | Agones and Kubernetes |
| Observability | Basic logs | Logs and basic metrics | Metrics, tracing, alerting, incident response |

Go is a practical primary language for independent game servers and backend services because the server does not need to run inside Unity. However, do not teach raw TCP, UDP, WebSocket, Socket.IO, and RPC as five equivalent technologies: they operate at different abstraction levels.

# 6. How the Courses Differ in Practice

### Beginner — Understand and Implement

Students receive a working game and learn to make it multiplayer.

**Deliverable:** A multiplayer Pong game.

### Intermediate — Design and Integrate

Students make architectural decisions, implement an independent game server, integrate backend services, and deploy the game.

**Deliverable:** A multiplayer game with an authoritative dedicated server, basic matchmaking, and persistent match results.

### Advanced — Operate and Scale

Students manage server instances, automate deployments, handle failures, measure capacity, and design infrastructure.

**Deliverable:** A multiplayer platform that automatically allocates and manages dedicated game servers.

**The intermediate course builds the game server; the advanced course builds the infrastructure that manages game servers.**

# 7. What to Leave Out

To keep the curriculum manageable, do not require the following in the beginner course:
- Writing a custom networking protocol.
- Implementing TCP or UDP from scratch.
- Building authentication and account systems.
- Deploying databases.
- Learning Docker or Kubernetes.
- Implementing sophisticated client prediction.
- Building a matchmaking service.

The intermediate course introduces practical implementations without attempting to reproduce every production feature. The advanced course focuses on distributed systems, infrastructure, and operations.

Do not require students to build a complete production-grade anti-cheat system. Understanding authority, validation, and trust boundaries is essential; comprehensive anti-cheat technology is a much larger subject.

# 8. Final Curriculum Map

```text
                    BEGINNER
                        │
             Multiplayer Fundamentals
                        │
             Client / Server / Authority
             Protocols / Synchronization
             Latency / Match Lifecycle
                        │
                        ▼
                Multiplayer Pong
                        │
                        ▼
                   INTERMEDIATE
                        │
               Multiplayer Engineering
                        │
             Dedicated Servers / Go
             Protocols / RPCs / Prediction
             Matchmaking / Deployment
             Backend Integration / Security
                        │
                        ▼
                  Networked Game
                        │
                        ▼
                     ADVANCED
                        │
              Multiplayer Infrastructure
                        │
             Docker / Orchestration
             Server Allocation / Scaling
             Distributed Systems / Reliability
             Observability / Automation
                        │
                        ▼
              Multiplayer Game Platform
```

# 9. Teaching and Assessment Principles

Treat the lesson lists as a curriculum map, not a rigid script. Every lesson should define three explicit outcomes:

1. **Understand:** What concepts must students explain?
2. **Implement:** What must students build or modify?
3. **Explain independently:** What design decisions and trade-offs must students justify?

This keeps the program focused on engineering rather than tutorial-following.

The three courses should share a project and build on one another, but each must have a clear engineering objective:
- Beginner: understand and implement multiplayer.
- Intermediate: design and deploy a multiplayer system.
- Advanced: operate and scale multiplayer infrastructure.
# 7. What to Leave Out

To keep the curriculum manageable, do not require the following in the beginner course:
- Writing a custom networking protocol.
- Implementing TCP or UDP from scratch.
- Building authentication and account systems.
- Deploying databases.
- Learning Docker or Kubernetes.
- Implementing sophisticated client prediction.
- Building a matchmaking service.

The intermediate course introduces practical implementations without attempting to reproduce every production feature. The advanced course focuses on distributed systems, infrastructure, and operations.

Do not require students to build a complete production-grade anti-cheat system. Understanding authority, validation, and trust boundaries is essential; comprehensive anti-cheat technology is a much larger subject.

# 8. Final Curriculum Map

```text
                    BEGINNER
                        │
             Multiplayer Fundamentals
                        │
             Client / Server / Authority
             Protocols / Synchronization
             Latency / Match Lifecycle
                        │
                        ▼
                Multiplayer Pong
                        │
                        ▼
                   INTERMEDIATE
                        │
               Multiplayer Engineering
                        │
             Dedicated Servers / Go
             Protocols / RPCs / Prediction
             Matchmaking / Deployment
             Backend Integration / Security
                        │
                        ▼
                  Networked Game
                        │
                        ▼
                     ADVANCED
                        │
              Multiplayer Infrastructure
                        │
             Docker / Orchestration
             Server Allocation / Scaling
             Distributed Systems / Reliability
             Observability / Automation
                        │
                        ▼
              Multiplayer Game Platform
```

# 9. Teaching and Assessment Principles

Treat the lesson lists as a curriculum map, not a rigid script. Every lesson should define three explicit outcomes:

1. **Understand:** What concepts must students explain?
2. **Implement:** What must students build or modify?
3. **Explain independently:** What design decisions and trade-offs must students justify?

This keeps the program focused on engineering rather than tutorial-following.

The three courses should share a project and build on one# Multiplayer Game Development Curriculum
## Beginner → Intermediate → Advanced

## Purpose and Scope

This curriculum teaches **multiplayer game development as a discipline separate from making games in Unity**.

The objective is to teach students how to make an existing game work across multiple computers, design networking architecture, and eventually operate multiplayer games in production.

- **Beginner:** How does a multiplayer game work?
- **Intermediate:** How do we build, secure, and deploy a multiplayer game?
- **Advanced:** How do we design, scale, orchestrate, and operate multiplayer infrastructure?

Each course produces a working project. The projects build on one another so students learn a coherent system rather than disconnected technologies.

### Course overview

| Dimension | Beginner | Intermediate | Advanced |
|---|---|---|---|
| Primary objective | Understand multiplayer fundamentals | Engineer a complete multiplayer system | Design and operate production infrastructure |
| Focus | Concepts, networking models, basic implementation | Authority, protocols, game servers, matchmaking | Architecture, orchestration, scaling, reliability |
| Game development | Assumed prerequisite | Assumed prerequisite | Assumed prerequisite |
| Networking | Core concepts and basic synchronization | Deep implementation and trade-offs | Performance, scaling, failure handling |
| Server architecture | Introduction | Design and deployment | Distributed infrastructure |
| Backend APIs | Conceptual introduction | Implementation and integration | Service architecture and operations |
| Matchmaking | Conceptual introduction | Functional matchmaking | Allocation, orchestration, scaling |
| Deployment | Run local processes | Deploy a dedicated server | Docker, orchestration, automated lifecycle management |
| Security | Trust boundaries and basic validation | Authority enforcement and abuse prevention | Infrastructure security and operational controls |
| Final project | Multiplayer paddle game | Networked game with dedicated server and matchmaking | Multiplayer platform with automated game-server management |

### Prerequisites and teaching boundary

Students should already understand basic programming and have completed an introductory game-development course. This course is **not a second Unity course**.

Saber owns local game mechanics: movement, shooting, animation, physics, UI, and core gameplay. This course owns networking, multiplayer architecture, authority, game servers, and online services. The security programmer contributes threat modeling and security engineering.

Use a working single-player paddle game supplied by the game-development course. Students modify only the code necessary to introduce networking.

---

# Part I — Beginner Course
## Multiplayer Fundamentals: From Local Game to Multiplayer Game

**Objective:** Students understand the architecture, terminology, communication patterns, and fundamental problems of multiplayer games. They finish by implementing a simple networked paddle game.

**Suggested duration:** 12 lessons, approximately 2 hours each, plus independent practice.

The beginner course should broadly cover the multiplayer landscape, but implement only a small, coherent subset.

## Lesson 1 — What Actually Makes a Game Multiplayer?

**Objective:** Build a correct mental model before introducing networking code.

Topics:
- Local multiplayer versus online multiplayer.
- Client, server, network protocol, and game state.
- Why two independent game simulations drift apart.
- Game logic versus networking.
- Authority versus synchronization.
- Why multiplayer requires coordination.

Example architecture:

```text
Player A Client ── inputs ──┐
                            ▼
                     Authoritative
                      Game Server
                            ▲
Player B Client ── inputs ──┘
                            │
                      State updates
                       to clients
```

The server maintains the authoritative state in this example. Real systems may send different updates to different clients and need not transmit the entire state every time.

**Practical exercise:** Draw the architecture of a two-player game and identify what information must be shared.

**Expected outcome:** Students understand why multiplayer requires communication and coordinated state.

## Lesson 2 — Multiplayer Architectures and Networking Terminology

**Objective:** Understand the major architectural models before choosing an implementation.

Topics:
- Client-server architecture.
- Dedicated servers.
- Listen servers and player-hosted games.
- Peer-to-peer networking.
- Relays.
- Authoritative versus non-authoritative systems.
- Centralized versus distributed responsibilities.
- Game server versus backend service.

Architectures to compare:

```text
Listen server:
Player A [Client + Server] ───── Player B [Client]

Dedicated server:
Player A [Client] ───── [Dedicated Server] ───── Player B [Client]

Peer-to-peer:
Player A [Peer] ◄────────► Player B [Peer]

Relay-based:
Player A [Client] ───── [Relay] ───── Player B [Client]
```

A relay forwards traffic and does not necessarily simulate the game. Peer-to-peer systems may still assign special authority to one peer.

Compare cost, connectivity, trust, complexity, and failure modes.

**Practical exercise:** Choose an architecture for a cooperative game, a competitive shooter, and a small browser game. Justify each choice.

## Lesson 3 — Network Communication: Messages, Protocols, and Transports

**Objective:** Understand how information moves between machines.

Topics:
- Messages and serialization.
- JSON and binary serialization.
- Message schemas and versions.
- TCP and UDP.
- HTTP and WebSocket.
- Networking frameworks such as Mirror and Unity Netcode for GameObjects.
- Socket.IO.
- RPC (remote procedure call).

| Technology | What it provides | Typical use |
|---|---|---|
| TCP | Reliable, ordered byte stream | Many application protocols |
| UDP | Datagram transport without built-in delivery guarantees | Real-time networking protocols |
| HTTP | Request-response application protocol | Authentication, profiles, APIs |
| WebSocket | Persistent bidirectional communication over a connection | Browser-based real-time applications |
| Socket.IO | Higher-level event-based communication with its own protocol | Event-driven real-time applications |
| RPC | An abstraction for invoking remote operations | Framework-dependent remote calls |

WebSocket is not a replacement for understanding TCP or UDP. Socket.IO clients are not automatically compatible with ordinary WebSocket servers. RPC is an abstraction, not a transport.

**Practical exercise:** Classify example messages as player input, state update, event, or request-response operation.

## Lesson 4 — Game State and Synchronization

**Objective:** Understand what must be synchronized and why.

Topics:
- Authoritative game state.
- State versus event versus input versus command.
- Snapshots.
- State replication.
- State ownership.
- Update frequency.
- Full snapshots versus incremental updates.
- Initial synchronization.
- Late-joining players.

Example state:

```text
MatchState
├── Ball: Position, Velocity
├── Player A: Paddle Position, Score
├── Player B: Paddle Position, Score
└── Match Status: Playing / Finished
```

Not everything needs continuous synchronization. Paddle positions may need frequent updates, while match-start events happen only once.

**Practical exercise:** Decide which Pong values need synchronization, how often they should be sent, and which events should be sent only when something happens.

## Lesson 5 — Client-Server Interaction Patterns

**Objective:** Learn how clients and servers communicate at the application level.

Patterns:
1. Request-response.
2. Command and authoritative result.
3. Server-to-client notification.
4. Continuous state updates.

```text
Request-response:
Client ── Request ──► Server
Client ◄─ Response ── Server

Command:
Client ── Input/Command ──► Server
Client ◄── Accepted State ── Server

Broadcast:
Server ── Event ──► Client A
Server ── Event ──► Client B
```

Introduce RPCs without making the lesson a framework-specific API tutorial. Explain that a command expresses an intention and may be rejected; an event describes something that has occurred.

**Practical exercise:** Design the messages needed to start a match, move a paddle, score a point, and end a match.

## Lesson 6 — Authority, Ownership, and Trust

**Objective:** Understand who is allowed to decide what happens.

Topics:
- Client authority.
- Server authority.
- Object ownership.
- Input authority.
- State authority.
- Trust boundaries.
- Server-side validation.
- Why client-side validation is insufficient for competitive games.

Core principle: **Clients communicate intent; the authority responsible for a piece of game state determines whether a requested change is valid.**

Authority can be assigned per piece of state or operation. Not every system must use the same authority model.

**Practical exercise:** Let a client update its own score, demonstrate the exploit, then redesign the system so the server owns the score.

## Lesson 7 — Time, Latency, and Real-Time Constraints

**Objective:** Understand why networked games behave differently from local games.

Topics:
- Latency and round-trip time (RTT).
- Jitter and packet loss.
- Bandwidth.
- Update frequency.
- Simulation tick rate versus frame rate.
- Clock differences.
- Interpolation.
- Extrapolation.
- Prediction.
- Reconciliation.

A network message can arrive late or be lost, depending on the transport and protocol. Increasing update frequency alone does not solve every latency problem.

**Practical exercise:** Add artificial delay to Pong and observe the effects on paddle movement and ball synchronization.

## Lesson 8 — Player Lifecycle and Match Lifecycle

**Objective:** Understand the lifecycle of players and matches.

Topics:
- Connecting and disconnecting.
- Authentication as a concept.
- Joining a session.
- Spawning a player.
- Leaving and reconnecting.
- Unexpected disconnections.
- Match start and end.
- Late joining.
- Session cleanup.
- Lobby versus match.

Example lifecycle:

```text
Waiting for Players
        ↓
   Match Starting
        ↓
     In Progress
        ↓
    Match Finished
        ↓
      Cleaned Up
```

**Practical exercise:** Implement the lifecycle and test what happens when a player disconnects during each phase.

## Lesson 9 — Game Server versus Backend Services

**Objective:** Assign responsibilities to the right components.

Topics:
- Game server processes.
- Backend APIs.
- Authentication services.
- Databases.
- Matchmaking.
- Server allocation.
- Logging and monitoring.

```text
                 GAME BACKEND
          ┌────────────────────┐
          │ Accounts           │
          │ Matchmaking        │
          │ Persistent Data    │
          └─────────┬──────────┘
                    │ HTTPS / API
                    ▼
               GAME SERVER
          ┌────────────────────┐
          │ Match State        │
          │ Game Rules         │
          │ Scores             │
          │ Player Actions     │
          └─────────┬──────────┘
                    │ Real-time networking
              ┌─────┴─────┐
              ▼           ▼
           Client A    Client B
```

These are logical responsibilities, not necessarily separate machines. A small project may combine components; larger games may deploy them independently.

**Practical exercise:** Assign a list of responsibilities to a client, game server, or backend service.

## Lesson 10 — Network Performance and Reliability Fundamentals

**Objective:** Introduce constraints of real-time networked systems.

Topics:
- Bandwidth versus latency.
- Message frequency and payload size.
- Reliable versus unreliable delivery.
- Ordered versus unordered delivery.
- Lost and duplicate messages.
- Timeouts.
- Basic network debugging.
- Message freshness.

Examples:
- Paddle position: freshness may matter more than delivering every update.
- Match-ending result: should not be silently lost.
- Chat: reliable delivery is generally desirable.
- Purchase transaction: requires reliable processing and duplicate protection.

**Practical exercise:** Classify messages by delivery requirements and explain the trade-offs.

## Lesson 11 — Build the Multiplayer Pong Game

**Objective:** Apply the fundamentals in one complete project.

Students implement:
- A server process or host.
- Client connections.
- Player registration within a match.
- Paddle input messages.
- Authoritative ball movement.
- Collision handling.
- Score calculation.
- State synchronization.
- Match start and end.
- Disconnection handling.

Use an existing Unity networking framework rather than requiring students to implement raw TCP or UDP from scratch.

**Practical exercise:** Build and test a two-player game in which the server advances the authoritative simulation and clients send input and render the resulting state.

## Lesson 12 — Testing, Debugging, and Architectural Review

**Objective:** Ensure students understand what they built.

Topics:
- Testing multiple clients.
- Testing across separate computers.
- Network logs.
- Connection failures.
- Simulated latency.
- Player disconnections.
- Authority bugs.
- Synchronization bugs.
- Basic bandwidth and update-frequency analysis.

**Final assessment:** Each student must explain:
1. Where the authoritative game state lives.
2. How player input reaches the server.
3. How the server updates the game.
4. How clients learn about state changes.
5. What happens when a client disconnects.
6. What must change to deploy a dedicated server.

### Beginner completion criteria

Students can:
- Explain client-server architecture and alternative models.
- Distinguish gameplay networking from backend APIs.
- Explain authority, ownership, and state synchronization.
- Describe TCP, UDP, HTTP, WebSocket, and RPC at a conceptual level.
- Explain latency, jitter, packet loss, and tick rate.
- Build and test a basic multiplayer game.
- Explain the path from a locally hosted prototype to dedicated-server deployment.

---

# Part II — Intermediate Course
## Multiplayer Engineering: Authority, Protocols, Game Servers, and Matchmaking

**Objective:** Students design and implement a complete multiplayer system, choose communication mechanisms, run a dedicated game server, connect it to backend services, and implement basic matchmaking.

**Suggested duration:** 16 lessons, approximately 2 hours each, plus project work.

## Lesson 1 — Review and Redesign the Beginner Architecture

Topics:
- Review the Pong architecture.
- Identify authoritative state.
- Reduce unnecessary network traffic.
- Separate game simulation from rendering.
- Separate transport, protocol, and game logic.
- Define module boundaries.
- Choose where validation belongs.

**Practical exercise:** Refactor the project to separate message handling, simulation, and presentation.

## Lesson 2 — Authority Models in Depth

Topics:
- Server, client, and distributed authority.
- Object ownership.
- Input authority versus state authority.
- Authority transfer.
- Host advantage.
- State validation.
- Trust boundaries.
- Security implications.

Examples:

| Game mechanic | Recommended starting model |
|---|---|
| Competitive shooting | Server-authoritative hit and damage decisions |
| Player movement | Client input, server validation, authoritative state |
| Cosmetic effects | Often client-side presentation |
| Inventory ownership | Authoritative backend or game server, depending on operation |
| Match score | Authoritative game server |
| Player camera | Usually local to the player |
| Cooperative puzzle object | Depends on game; server authority is a straightforward baseline |

**Practical exercise:** Demonstrate a manipulable client-authoritative score, then make the score server-authoritative.

## Lesson 3 — Client-Server Interaction Patterns in Depth

Topics:
- Requests and responses.
- Commands and RPCs.
- Server-to-client and client-to-server messages.
- Broadcasts and targeted messages.
- State replication.
- Event-driven communication.
- Streaming updates.
- Acknowledgements and timeouts.
- Idempotency and duplicate messages.

A command expresses intent:

```text
FireWeapon(weapon_id, aim_direction)
```

An event describes what occurred:

```text
WeaponFired(player_id, weapon_id)
```

The server may reject a command. The corresponding event should only be emitted if the action actually occurs according to the game rules.

**Practical exercise:** Design communication flows for shooting, damage, item collection, and match completion.

## Lesson 4 — Networking Protocols and Transport Selection

Topics:

### TCP and UDP sockets
- TCP connection lifecycle.
- UDP datagrams.
- Ports and addressing.
- TCP message framing.
- Delivery and ordering guarantees.
- Packet size and fragmentation.
- Application-level reliability.
- Connection handling.

TCP exposes a byte stream, not a sequence of application messages. The application must implement framing or use a protocol that defines it. UDP preserves datagram boundaries but does not guarantee delivery, ordering, or duplicate suppression.

### WebSocket
- Connection lifecycle.
- Bidirectional communication.
- Message-based protocols.
- JSON messages.
- Heartbeats and timeouts.
- Browser compatibility.
- Authentication considerations.

### Socket.IO
- Event-based communication.
- Rooms and broadcasts.
- Acknowledgements.
- Connection lifecycle.
- Protocol compatibility.

### RPC frameworks
- Remote invocation.
- Serialization.
- RPC direction.
- Ownership and authorization checks.
- Error handling and latency.

**Practical exercise:** Implement the same simple interaction using (1) a Unity networking framework and (2) a small custom client-server implementation using WebSocket or another appropriate transport. The goal is comparison, not a production-grade custom protocol.

## Lesson 5 — Protocol Design and Message Schemas

Topics:
- Message identifiers.
- Request identifiers.
- Player and session identifiers.
- Serialization formats.
- Message validation.
- Protocol versioning.
- Backward compatibility.
- Error responses.
- Sequence numbers and timestamps.
- Message size limits.

Example envelope:

```json
{
  "version": 1,
  "type": "player_input",
  "sequence": 184,
  "payload": {
    "direction": 1
  }
}
```

The format alone does not make the system secure. The server must validate the sender, message type, payload, sequence behavior, and permitted action.

**Practical exercise:** Document a protocol for joining a match, moving a paddle, reporting a score, and ending a match. For each message, define sender, recipient, purpose, validation, and delivery expectations.

## Lesson 6 — Dedicated Game Server Architecture

Topics:
- Headless execution.
- Dedicated server builds.
- Server-only initialization.
- Removing client-only dependencies.
- Server configuration and ports.
- Match isolation.
- Process lifecycle.
- Graceful shutdown.
- Logs and diagnostics.

**Practical exercise:** Run the Pong server separately from Unity clients. Start the server, connect players, finish a match, and shut it down cleanly.

## Lesson 7 — Building a Game Server in a Different Language

**Objective:** Show that the server language need not match the client language.

Use Go for a small independent game server:

```text
Unity Client
     │ WebSocket
     ▼
Go Game Server
     ├── Connection Management
     ├── Message Validation
     ├── Game State
     ├── Game Loop
     └── State Updates
```

Topics:
- Creating a network listener.
- Accepting connections.
- Managing connected clients.
- Serializing and parsing messages.
- Handling disconnections.
- Managing shared state safely.
- Running a game loop.
- Sending state updates.
- Cleaning up resources.

**Practical exercise:** Connect Unity clients to a Go server that manages player positions and scores for a simplified paddle game.

## Lesson 8 — Game Simulation, Ticks, and Authoritative State

Topics:
- Game loops.
- Fixed simulation steps.
- Tick rates.
- Processing inputs.
- Simulation order.
- State updates and snapshots.
- Determinism.
- State consistency.
- Separating simulation from network I/O.

Simplified loop:

```text
while server_is_running:
    receive_network_messages()
    validate_and_queue_inputs()
    advance_game_simulation()
    produce_state_updates()
    send_updates_to_clients()
```

Production implementations require careful scheduling, buffering, error handling, and resource management. Avoid executing arbitrary gameplay actions directly inside network callbacks; validate and queue inputs for controlled simulation processing.

**Practical exercise:** Move Pong's authoritative ball simulation into a fixed-step server loop and compare simulation frequency with client rendering frequency.

## Lesson 9 — Synchronization, Prediction, and Reconciliation

Topics:
- Authoritative snapshots.
- Interpolation and extrapolation.
- Client-side prediction.
- Server reconciliation.
- Input sequence numbers.
- Acknowledged inputs.
- Snapshot interpolation buffers.
- Clock synchronization.
- State correction.

**Practical exercise:** Introduce artificial latency, implement basic prediction and reconciliation, and compare it with server-only movement updates.

Advanced shooter-style lag compensation is reserved for the advanced course.

## Lesson 10 — Network Reliability and Performance

Topics:
- Bandwidth budgets.
- Packet frequency.
- Serialization overhead.
- Reliable and unreliable messages.
- Sequencing.
- Prioritizing updates.
- Delta-compression concepts.
- Interest management.
- Backpressure.
- Network profiling and load testing.

**Practical exercise:** Measure the data generated by a session and reduce unnecessary updates.

## Lesson 11 — Backend Integration and Player Identity

Topics:
- Authentication APIs and access tokens.
- Token validation.
- Player identity versus session identity.
- Game-server identity.
- Service-to-service authentication.
- Secure configuration.
- Player authorization.
- Persistent versus temporary state.

```text
Account Identity
      ↓
Backend Authentication
      ↓
Authenticated Player
      ↓
Match Assignment
      ↓
Game Server Connection
      ↓
Authorized Match Participation
```

A client claiming to be a particular player does not prove account ownership.

**Practical exercise:** Authenticate through a backend API, obtain a token, and use it to join a game server. Validate the token or verify identity through a trusted backend mechanism.

## Lesson 12 — Lobby and Matchmaking Implementation

Topics:
- Lobby versus match.
- Matchmaking queues.
- Player capacity and criteria.
- Session identifiers.
- Server availability.
- Match creation.
- Joining and leaving.
- Cancellation and disconnect handling.
- Timeouts.

Basic flow:
1. A player requests a match.
2. The service places them in a queue.
3. The service finds enough eligible players.
4. It creates a match.
5. It assigns a game server.
6. Players receive connection information.
7. Players connect to the server.

**Practical exercise:** Implement a two-player queue that creates a match when two players are waiting. A small implementation can select from a fixed list of available servers.

## Lesson 13 — Running Multiple Game Server Instances

Topics:
- One process per match.
- Multiple processes per machine.
- Port allocation.
- Resource limits.
- Match isolation.
- Process monitoring.
- Server registration.
- Health checks.
- Graceful termination.
- Capacity tracking.

**Practical exercise:** Run multiple server instances on one machine using separate ports and assign different matches to them.

## Lesson 14 — Deployment Fundamentals

Topics:
- Linux server basics.
- Process management.
- Network interfaces and ports.
- Firewall rules.
- Environment variables.
- Server configuration.
- Logs and restarts.
- Resource monitoring.
- Public versus private networking.

Distinguish between a process that is running, a server reachable over the network, and a server ready to accept matches.

**Practical exercise:** Deploy a Go or headless Unity server on Linux and connect clients from another network.

## Lesson 15 — Security and Abuse Prevention

Coordinate with the security programmer.

Topics:
- Trust boundaries.
- Authentication versus authorization.
- Message validation.
- Rate limiting.
- Replay and duplicate-request concerns.
- Invalid movement and forged score updates.
- Connection exhaustion.
- Denial-of-service fundamentals.
- Secret management.
- Suspicious-activity logging.

**Practical exercise:** Modify a client to submit invalid movement or score messages and demonstrate how the server rejects them.

## Lesson 16 — Intermediate Capstone

Students deliver:
- A working multiplayer game.
- A dedicated game server.
- A documented authority model.
- A documented message protocol.
- Basic matchmaking.
- Server-side match state.
- Persistent match results.
- Deployable server configuration.
- Basic logging and error handling.
- Demonstration of invalid-action rejection.

### Intermediate completion criteria

Students can:
- Implement a multiplayer server in a separate process.
- Implement basic authoritative simulation.
- Choose suitable communication mechanisms.
- Distinguish transport, protocol, and RPC abstractions.
- Connect Unity clients to a server in another language.
- Build basic matchmaking.
- Run multiple server instances.
- Deploy and troubleshoot on Linux.
- Integrate authentication and persistent backend services.

---

# Part III — Advanced Course
## Multiplayer Infrastructure: Distributed Systems, Orchestration, and Production Operations

**Objective:** Teach students to design, deploy, scale, and operate multiplayer infrastructure that manages many matches and server instances.

The advanced course should answer:
- How do we launch many matches without manually starting processes?
- How do we choose a machine for a new match?
- How do we recover when a server crashes?
- How do we prevent double allocation?
- How do we manage server versions and rolling deployments?
- How do we observe fleet performance?
- How do we scale without wasting resources?

**Suggested duration:** 18 lessons, approximately 2 hours each, plus a substantial team project.

## Lesson 1 — Production Multiplayer Architecture

Topics:
- Clients and dedicated servers.
- Authentication services.
- Matchmaking and allocation.
- Server registries.
- Databases and message queues.
- Telemetry and monitoring.
- Deployment infrastructure.
- Control plane versus gameplay data plane.

The **control plane** manages infrastructure and match lifecycle: matchmaking, allocation, deployment, and registration.

The **gameplay data plane** carries real-time traffic needed to execute matches, such as player inputs and state updates.

```text
                    CONTROL PLANE
         ┌──────────────────────────────┐
         │ Authentication              │
         │ Matchmaking                 │
         │ Server Allocation           │
         │ Fleet Management            │
         │ Deployment Control          │
         │ Monitoring                  │
         └──────────────┬───────────────┘
                        │
                 Lifecycle and
                 Match Assignment
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
     Game Server A  Game Server B  Game Server C
          ▲             ▲             ▲
          └─────────────┼─────────────┘
                        │
                  GAMEPLAY DATA
                        │
                 Player Connections
```

These are logical responsibilities, not necessarily separate networks or machines.

## Lesson 2 — Matchmaking Architecture and Algorithms

Topics:
- Queue-based matchmaking.
- Skill-based matchmaking.
- Region selection.
- Party matchmaking.
- Match size and game-mode constraints.
- Queue time versus match quality.
- Cancellation and repeated requests.
- Matchmaking state machines.
- Idempotency.
- Concurrency and race conditions.

Distinguish finding players, creating a match, allocating a server, and confirming readiness. These may be separate steps and services.

**Practical exercise:** Design matchmaking for multiple regions, game modes, and player counts. Explain how concurrent workers avoid processing the same queue entry incorrectly.

## Lesson 3 — Server Allocation and Fleet Management

Topics:
- Available capacity.
- Server registration.
- Allocation requests and leases.
- Heartbeats and health checks.
- Readiness.
- Timeouts and expired leases.
- Failed allocations.
- Idempotency.
- Race conditions.
- Orphaned server cleanup.

Example lifecycle:

```text
Starting → Ready → Allocated → Running → Cleaning → Stopped
```

A real system may also need failure, cancellation, draining, and recovery states.

**Practical exercise:** Build an allocator that prevents concurrent requests from allocating the same capacity twice.

## Lesson 4 — Containerization with Docker

Topics:
- Images and containers.
- Dockerfiles and multi-stage builds.
- Runtime configuration.
- Environment variables.
- Container networking and ports.
- Volumes and logs.
- Resource limits.
- Graceful shutdown.
- Image versioning and registries.
- Container health checks.

Docker packages and runs a server; it does not, by itself, provide a complete matchmaking and allocation system.

**Practical exercise:** Build a Docker image for the game server and run multiple instances with distinct configurations.

For Go, introduce multi-stage builds. For Unity, discuss headless builds, runtime dependencies, and image size.

## Lesson 5 — Container Orchestration

Topics:
- Containers versus orchestration.
- Desired state.
- Scheduling.
- Resource requests and limits.
- Health checks and restart policies.
- Scaling and rolling deployments.
- Service discovery.
- Node failure.
- Graceful termination.

Kubernetes manages workloads, but multiplayer games often need additional game-specific functionality for match allocation and lifecycle management.

**Practical exercise:** Deploy a small service and game-server workload to Kubernetes and observe what happens when an instance fails.

## Lesson 6 — Game-Specific Orchestration with Agones

Topics:
- GameServer resources.
- Fleet management.
- Allocation.
- Ready and allocated states.
- Health checks.
- Server lifecycle.
- Capacity scaling.
- Draining.
- Matchmaking integration.
- Operational trade-offs.

**Practical exercise:** Deploy a dedicated server through Agones, allocate it for a match, and observe its lifecycle.

Agones does not automatically implement game matchmaking rules, account systems, or gameplay protocols.

## Lesson 7 — Dynamic Server Allocation and Match Lifecycle

End-to-end flow:

```text
Player Requests Match
          ↓
   Matchmaking Queue
          ↓
    Match Created
          ↓
   Server Allocation
          ↓
    Server Becomes Ready
          ↓
   Connection Information
          ↓
      Players Connect
          ↓
       Match Runs
          ↓
      Match Finishes
          ↓
   Results Are Persisted
          ↓
   Server Is Reused or Stopped
```

Discuss no capacity, startup failure, expired allocation, client connection failure, server crash, result-submission failure, and incomplete cleanup.

**Practical exercise:** Implement the lifecycle with simulated allocation failures and retries.

## Lesson 8 — Multi-Region Infrastructure

Topics:
- Latency and geography.
- Region-aware matchmaking.
- Regional server fleets.
- Cross-region backend services.
- Regional capacity and failover.
- Data locality.
- Regional outages.
- Deployment consistency.

Region selection should consider measured latency, routing, availability, player population, and capacity—not just geographic distance.

**Practical exercise:** Extend matchmaking to choose between two simulated regions using latency and capacity information.

## Lesson 9 — Scaling Strategies and Capacity Planning

Topics:
- Concurrent players and matches.
- CPU and memory usage.
- Network bandwidth.
- Tick rate and entity count.
- Player density.
- Vertical versus horizontal scaling.
- Autoscaling.
- Warm capacity and cold-start time.
- Capacity reservations.
- Cost per match.

Scaling APIs is different from scaling game servers. Game servers continuously simulate matches and maintain real-time connections.

**Practical exercise:** Load-test the server, measure resource consumption, and estimate instances needed for a target number of concurrent matches. Base estimates on measurements rather than assumed player counts.

## Lesson 10 — Distributed Systems Fundamentals

Topics:
- Service boundaries.
- Network partitions.
- Timeouts and retries.
- Duplicate requests.
- Idempotency.
- Eventual consistency.
- Leases and heartbeats.
- Distributed locks and their limitations.
- Partial failures.
- State reconciliation.
- Consistency trade-offs.

**Practical exercise:** Simulate allocator failures and duplicate requests, then design recovery that avoids allocating the same capacity twice.

## Lesson 11 — Backend Service Architecture

Topics:
- Authentication and authorization.
- API gateways.
- Service-to-service communication.
- Database transactions.
- Caching.
- Message queues.
- Asynchronous processing.
- Event-driven architecture.
- Persistent match results.
- Schema evolution.
- Data consistency.

Discuss when a modular monolith is sufficient and when separate services are useful. Microservices are not a universal requirement; they introduce operational complexity.

**Practical exercise:** Design interactions between matchmaking, allocation, match results, and player progression.

## Lesson 12 — Persistent Data and Match-Result Integrity

Topics:
- Persistent versus ephemeral state.
- Transaction boundaries.
- Match-result submission.
- Duplicate-result prevention.
- Idempotency keys.
- Durable event processing.
- Retries and database failures.
- Recovery after a game-server crash.
- Auditing authoritative results.

**Practical exercise:** Implement match-result submission with duplicate protection and retry handling.

## Lesson 13 — Observability and Production Diagnostics

Topics:
- Structured logs.
- Metrics.
- Distributed tracing.
- Correlation IDs.
- Match and server-instance IDs.
- Connection metrics.
- Tick duration.
- Network traffic.
- Match and allocation failures.
- Disconnect rates.
- Resource utilization.

Distinguish:
- **Metrics:** How often or how much something happens.
- **Logs:** What happened during a specific execution.
- **Traces:** How a request or operation moves across services.

**Practical exercise:** Instrument matchmaking and allocation, then diagnose a deliberately introduced failure.

## Lesson 14 — Reliability and Incident Handling

Topics:
- Health versus readiness checks.
- Graceful shutdown.
- Rolling deployments and draining.
- Circuit breakers.
- Retry budgets.
- Backpressure.
- Overload protection.
- Crash recovery.
- Capacity exhaustion.
- Incident diagnosis.

Restarting a process does not automatically restore in-memory match state. Recovery may require snapshots or replicated state—or the match may need to be declared failed.

**Practical exercise:** Terminate a game server during a match and measure how the platform detects and handles the failure.

## Lesson 15 — Security at Infrastructure Scale

Coordinate with the security programmer.

Topics:
- Threat modeling.
- Network segmentation.
- Service identity.
- Secrets management.
- Authentication between services.
- Least privilege.
- Container security.
- Dependency management.
- Rate limiting and abuse detection.
- Resource exhaustion.
- Administrative access.
- Audit logging.

**Practical exercise:** Review the deployed architecture, identify trust boundaries, and correct deliberately introduced configuration problems.

## Lesson 16 — Performance Profiling and Optimization

Topics:
- CPU and memory profiling.
- Allocation and garbage collection.
- Tick-time analysis.
- Network profiling.
- Serialization costs.
- Lock contention.
- Connection limits.
- Database latency.
- Load testing.
- Capacity estimation.

Distinguish simulation performance from networking performance.

**Practical exercise:** Profile the game server under load, identify its largest bottleneck, and document a measurable improvement.

## Lesson 17 — Deployment Automation and Release Management

Topics:
- CI/CD.
- Automated builds.
- Versioned server images.
- Configuration management.
- Infrastructure as code.
- Environment separation.
- Staged deployment.
- Rolling updates and draining.
- Client-server compatibility.
- Rollback strategies.
- Release observability.

A successful container deployment does not guarantee that older clients can communicate with a newer server.

**Practical exercise:** Automate a new server-version deployment and verify that incompatible clients can be identified.

## Lesson 18 — Advanced Capstone

Students build and demonstrate a small multiplayer platform that automatically manages dedicated server instances.

### Game layer
- Multiplayer client.
- Dedicated authoritative server.
- Defined networking protocol.
- Match lifecycle.

### Backend layer
- Authentication or simplified trusted identity.
- Matchmaking.
- Server allocation.
- Match-result persistence.

### Infrastructure layer
- Docker images.
- Automated server deployment.
- Multiple server instances.
- Health monitoring.
- Server registration and cleanup.

### Operations layer
- Structured logs.
- Basic metrics.
- Load testing.
- Failure simulation.
- Recovery procedures.

Agones and Kubernetes can power the advanced implementation. A shorter version can use a custom allocator and container deployment without implementing every production feature.

### Advanced completion criteria

Students can:
- Design multiplayer infrastructure.
- Implement matchmaking and allocation with explicit state transitions.
- Manage multiple dedicated-server instances.
- Package and deploy servers with Docker.
- Explain orchestration and use a game-server orchestration system.
- Handle duplicate requests, failures, and allocation races.
- Measure capacity and performance.
- Design observability and incident-handling mechanisms.
- Automate deployment and explain compatibility concerns.
- Justify architecture using requirements and measured behavior.

---

# 5. Technology Strategy

Limit the number of technologies students must learn. Teach transferable concepts rather than memorizing several frameworks.

| Area | Beginner | Intermediate | Advanced |
|---|---|---|---|
| Game networking | Mirror or Unity Netcode | Framework plus custom server | Production networking architecture |
| Programming language | C# | C# and Go | Go, C#, infrastructure tooling |
| Transport | Framework-managed networking | WebSocket, TCP/UDP concepts, Socket.IO comparison | Protocol optimization and transport trade-offs |
| Backend | Architecture concepts | Go + Gin + REST | Service architecture and distributed systems |
| Database | Understand persistence | PostgreSQL or MySQL | Transactions, consistency, operational reliability |
| Dedicated servers | Understand the concept | Deploy a real server | Manage a fleet |
| Containers | Conceptual introduction | Optional Docker introduction | Docker and orchestration |
| Orchestration | Not required | Manual instance management | Agones and Kubernetes |
| Observability | Basic logs | Logs and basic metrics | Metrics, tracing, alerting, incident response |

Go is a practical primary language for independent game servers and backend services because the server does not need to run inside Unity. However, do not teach raw TCP, UDP, WebSocket, Socket.IO, and RPC as five equivalent technologies: they operate at different abstraction levels.

# 6. How the Courses Differ in Practice

### Beginner — Understand and Implement

Students receive a working game and learn to make it multiplayer.

**Deliverable:** A multiplayer Pong game.

### Intermediate — Design and Integrate

Students make architectural decisions, implement an independent game server, integrate backend services, and deploy the game.

**Deliverable:** A multiplayer game with an authoritative dedicated server, basic matchmaking, and persistent match results.

### Advanced — Operate and Scale

Students manage server instances, automate deployments, handle failures, measure capacity, and design infrastructure.

**Deliverable:** A multiplayer platform that automatically allocates and manages dedicated game servers.

**The intermediate course builds the game server; the advanced course builds the infrastructure that manages game servers.**

# 7. What to Leave Out

To keep the curriculum manageable, do not require the following in the beginner course:
- Writing a custom networking protocol.
- Implementing TCP or UDP from scratch.
- Building authentication and account systems.
- Deploying databases.
- Learning Docker or Kubernetes.
- Implementing sophisticated client prediction.
- Building a matchmaking service.

The intermediate course introduces practical implementations without attempting to reproduce every production feature. The advanced course focuses on distributed systems, infrastructure, and operations.

Do not require students to build a complete production-grade anti-cheat system. Understanding authority, validation, and trust boundaries is essential; comprehensive anti-cheat technology is a much larger subject.

# 8. Final Curriculum Map

```text
                    BEGINNER
                        │
             Multiplayer Fundamentals
                        │
             Client / Server / Authority
             Protocols / Synchronization
             Latency / Match Lifecycle
                        │
                        ▼
                Multiplayer Pong
                        │
                        ▼
                   INTERMEDIATE
                        │
               Multiplayer Engineering
                        │
             Dedicated Servers / Go
             Protocols / RPCs / Prediction
             Matchmaking / Deployment
             Backend Integration / Security
                        │
                        ▼
                  Networked Game
                        │
                        ▼
                     ADVANCED
                        │
              Multiplayer Infrastructure
                        │
             Docker / Orchestration
             Server Allocation / Scaling
             Distributed Systems / Reliability
             Observability / Automation
                        │
                        ▼
              Multiplayer Game Platform
```

# 9. Teaching and Assessment Principles

Treat the lesson lists as a curriculum map, not a rigid script. Every lesson should define three explicit outcomes:

1. **Understand:** What concepts must students explain?
2. **Implement:** What must students build or modify?
3. **Explain independently:** What design decisions and trade-offs must students justify?

This keeps the program focused on engineering rather than tutorial-following.

The three courses should share a project and build on one another, but each must have a clear engineering objective:
- Beginner: understand and implement multiplayer.
- Intermediate: design and deploy a multiplayer system.
- Advanced: operate and scale multiplayer infrastructure. another, but each must have a clear engineering objective:
- Beginner: understand and implement multiplayer.
- Intermediate: design and deploy a multiplayer system.
- Advanced: operate and scale multiplayer infrastructure.

