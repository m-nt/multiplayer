# I04 Teaching Materials — Transport Workshops: TCP, UDP, and WebSocket

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Describe TCP as a stream service and map connection establishment, reads/writes, and closure to observable behavior.
- Implement bounded message framing and distinguish flow control, loss recovery, and congestion response.
- Compare UDP datagram freshness with WebSocket message/lifecycle behavior.
- Select a transport by required behavior while identifying the application work it does not provide.

## Topics in detail

### 1. Addresses, ports, listeners, and accepted streams

An address identifies a network interface/path target; a port identifies a transport endpoint on a host. A listening TCP socket accepts new conversations, while each accepted connection handles a peer stream. Localhost selects the local machine. Binding and reachability differ: routing and firewalls still determine whether another machine can connect. A completed connection has not authenticated a player.

### 2. What TCP actually promises to the application

The TCP contract is ordered byte delivery while the connection functions, with failure reported when it cannot continue under the endpoint's conditions. Sequence numbers and acknowledgements support retransmission. Your two application writes can emerge as one read or many reads. Sender-side buffering also means a completed write is not proof the peer executed a game command. TCP supplies neither encryption nor application message boundaries.

### 3. Framing is a small parser, not a read assumption

Use the lab's four-byte big-endian payload length and 1–4096-byte limit. Accumulate an incomplete header, validate length before allocating payload storage, collect the payload, emit it, then continue parsing any remaining bytes. A stream ending inside a frame is an error. Test deterministic split/coalescing cases in the separate TCP worksheet instead of hoping the OS demonstrates every boundary.

### 4. Progress, backpressure, and failure detection

Flow control responds to receiver capacity; congestion control responds to network conditions. Both can constrain sending. A silent peer may need a read deadline or heartbeat policy to be recognized as unusable. Graceful EOF, an explicit reset, and deadline expiry are different observations. Reconnection creates a new conversation, so prior application outcomes require an application recovery contract.

### 5. UDP experiments isolate freshness decisions

A UDP receive corresponds to a datagram, subject to buffer handling, rather than an arbitrary slice of a byte stream. Delivery/order/duplicate guarantees must not be assumed. In a numbered-state harness, discard stale updates, count gaps, and choose a bounded send rate. Loss of a transient snapshot differs from loss of a one-time award. Production reliability, congestion response, and security exceed this experiment.

### 6. WebSocket preserves messages, not business meaning

A library manages its framing, opening/closing, and control frames. Your application still defines input schemas, size limits, identity, admission, and timeout policy. The course uses ordinary WebSocket over a TCP-based path, so ordered-stream delay remains relevant. Socket.IO adds another protocol layer; RPC is an operation abstraction. Neither should be treated as merely a spelling of TCP.

## Worked walkthrough

Use frames for UP and DOWN from [TCP-WORKSHOP.md](TCP-WORKSHOP.md). Deliver the first frame's header as two chunks and both payloads in a later chunk. The decoder must output two messages, never a partial UP. Now send numbered UDP state 10, 12, 11, 12 through a deterministic harness: accept 10 and 12 as newest state, classify 11 as stale and the repeated 12 as duplicate. Those rules would be inappropriate for distinct purchase operations.

## Demonstration and guided practice

1. Block 1: annotate connection/read/write/close behavior and execute all worksheet framing vectors.
2. Block 2: apply known datagram loss/reordering; report accepted sequence and gaps separately.
3. Block 3: run the same intent/result exchange over WebSocket and the beginner framework; compare the responsibilities supplied by each.

## Common misunderstandings

- “A short read means a complete short message.” It can contain any available prefix of a stream.
- “UDP is always faster.” Required reliability, pacing, overhead, and path conditions determine the observed application behavior.

## Check your understanding

**1. Why reject a huge declared length before allocating?**

Expected reasoning: Otherwise an untrusted four-byte header can request excessive memory before application validation runs.

**2. Can reconnecting a socket tell whether an earlier command committed?**

Expected reasoning: No. Use application operation identity and an authoritative outcome lookup or safe retry.

## Independent exercise

Submit annotated lifecycle observations, deterministic frame tests, UDP sequence classifications, and a transport decision for Pong versus a hypothetical high-update shooter. Include what your test does not measure. Use the existing TCP worksheet for exact byte-level expected results.

## Connections and reading

TCP, UDP, WEBSOCKET, and GO-NET are the assigned primary contracts. The worksheet complements this chapter; implementing transport internals remains out of scope. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
