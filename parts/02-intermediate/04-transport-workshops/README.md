# I04 — Transport Workshops: TCP, UDP, and WebSocket

[Part overview](../README.md) · [Previous](../03-interaction-and-idempotency/README.md) · [Next](../05-protocol-and-schemas/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

The original scope is too large for two hours. Teach one bounded socket experiment per block and keep WebSocket as the project transport.

**Time:** 6 hours in three workshops. **Prerequisite:** I03 and its passing lab.

**By the end:** Explain what TCP actually provides, frame a byte stream, observe UDP behavior, and implement a WebSocket exchange.

**Boundary:** No custom congestion control, reliable UDP, WebSocket parser, or Socket.IO application. Use established libraries for protocol machinery.

## Teaching notes

### Workshop 1 — TCP through observable behavior

An endpoint combines address and port. A listening socket accepts connections; each accepted socket represents a separate peer conversation. Binding to loopback permits local access, while a different interface may permit remote access subject to routing and firewall rules. DNS resolves a name; it does not open a connection.

Use a packet trace to connect SYN, SYN-ACK, ACK to connection establishment, then map application connect/accept, read/write, and close calls to the conversation. An established TCP connection is not an authenticated game session. Treat a completed write as progress at a local I/O boundary, not proof that a game rule ran remotely.

TCP presents ordered bytes, not application messages. Write boundaries need not match read boundaries. For this lab define a four-byte unsigned big-endian length followed by that many UTF-8 payload bytes. Set a small lab limit, such as 4 KiB, before allocating the payload. A decoder buffers incomplete data, extracts all complete frames, and retains the remainder. Reject oversize length and EOF inside a frame. Handle partial writes according to the chosen socket API.

Observe graceful closure and abrupt termination separately. EOF after drained bytes, reset, and an application deadline are different observations. A silent peer may remain undetected without a deadline or heartbeat policy. Reconnect establishes a new transport conversation and requires fresh game admission. Instructor discussion covers half-close and TIME_WAIT; students do not modify OS tuning to hide those states.

### Workshop 2 — UDP and freshness

A datagram is one send/receive unit, subject to buffer limits. Delivery, ordering, and duplicate suppression are not guaranteed by UDP. Send a sequence number and observe a controlled drop/reorder/duplicate harness. Keep the newest valid sequence for transient state; do not apply that rule blindly to purchases or final results.

Keep datagrams small enough for the lab path and explain that path MTU and headers affect the usable payload. A large UDP message may fragment or fail; there is no universal safe payload size for every path. A UDP socket's local 'connect' operation does not perform TCP's handshake. A production UDP protocol still needs pacing, congestion response, security, and lifecycle rules.

### Workshop 3 — WebSocket and the application protocol

Use the library's opening/closing lifecycle and complete-message API. WebSocket framing is not your JSON schema. Configure message-size and time limits; distinguish ping/pong responsiveness from a healthy simulation. Use WSS when leaving the isolated local lab. Browser authentication must respect the browser client's header/cookie restrictions; do not place long-lived secrets in query strings.

Ordinary WebSocket clients are not Socket.IO clients: Socket.IO supplies an additional event protocol. RPC similarly describes a programming abstraction, not a wire transport. Compare the same intent/result interaction with the beginner framework to identify which concerns each implementation hides.

## Worked example

Encode payloads of lengths 5 and 3. Feed the decoder chunks containing only two header bytes, then the rest of the first frame plus the next header, then the final payload. It must produce exactly two messages. Use a deterministic decoder harness; do not rely on the OS randomly splitting writes during a live demo.

## Guided lab

Use the [TCP workshop worksheet](TCP-WORKSHOP.md) for exact byte vectors, lifecycle observations, and expected results in block 1.

1. Block 1: run a listener and client; capture connect, data, graceful close, and abrupt failure. Annotate application calls separately from transport events.
2. Implement the length-prefix decoder and test every split point, two coalesced frames, oversized length, and truncated EOF.
3. Block 2: send numbered datagrams through a deterministic impairment harness; reject stale state and report observed gaps/duplicates.
4. Block 3: exchange input/result JSON over WebSocket with size limits and timeout handling.
5. Compare framework RPC and WebSocket code using the same intent/result contract; record responsibility differences.

## Evidence required to pass

- Framing passes fragmented/coalesced data tests without unbounded allocation.
- The trace and explanation distinguish connection, authentication, and game admission.
- UDP observations come from an explicit harness or actual impairment, not an assumed guarantee.
- WebSocket closes and cleans up a silent/failed peer within the configured policy.

## Explain independently

If the server committed an action before the TCP connection broke, can reconnecting TCP tell the client whether that action happened?

## Reading and preparation

TCP: sections 2.2, 3.5, 3.6; GO-NET: Conn and deadlines; UDP: datagram contract; WEBSOCKET: opening/closing and control frames. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
