# TCP Workshop: From Bytes to a Game Message

[Lesson I04](README.md)

Use this worksheet during workshop 1. It provides deterministic cases and expected observations; students implement the decoder and small local client/server. No packet-capture privileges are needed for decoder tests. The instructor supplies a trace if live capture is unavailable.

## 1. Explain the service before using the socket

TCP is a transport protocol implemented by the endpoints' networking stacks. It provides each application with an ordered stream of bytes in each direction. It does not know that your bytes represent a paddle input.

Use byte sequence numbers and acknowledgements to explain retransmission: a missing portion of the stream is recovered before later bytes become available in order. Duplicate transmitted bytes do not become duplicate bytes in the application stream. This does **not** prevent an application from submitting the same command twice after a reconnect.

Separate three controls:

| Concern | Practical effect | Observe it |
|---|---|---|
| Loss recovery and ordering | Missing earlier bytes can delay newer game data on the same stream | Annotate an instructor loss/retransmission trace |
| Receiver flow control | A receiver that stops reading eventually constrains the sender | Pause reads and track write duration with a deadline |
| Network congestion control | Sending adapts to network conditions | Discuss a trace; do not implement a congestion algorithm |

The sender's buffers can absorb data initially, so one quick write does not prove the receiver is keeping up. Queue age and sustained behavior matter.

TCP is not encryption, player authentication, game authorization, or a database transaction. A working connection establishes none of those properties by itself.

## 2. Map the lifecycle to application behavior

Draw two lanes: client and server. Add these in order:

1. Server binds an address/port and listens.
2. Client initiates connection; annotate SYN, SYN-ACK, and ACK in the trace.
3. Server accepts a connection and obtains a per-peer stream. Its listener remains available for other peers.
4. Both applications exchange framed data. TCP acknowledgements are distinct from your InputAccepted response.
5. One side closes its sending direction; pending bytes may still be read before end-of-stream. Discuss a full graceful close separately.
6. Repeat with explicit reset or simulated blackhole. Record the actual error/deadline behavior rather than assuming every process exit produces a reset.

Record local/remote endpoint pairs. Several clients can connect to one server listening port because each connection has its own endpoint pair. Discuss TIME_WAIT as part of lifecycle protection; disabling it is not the lab solution to a port problem.

## 3. Frame two tiny messages

The lab protocol is four unsigned big-endian length bytes followed by exactly that many payload bytes. Length counts bytes, not Unicode characters. Allowed payload length is 1–4096 bytes. These deliberately tiny ASCII payloads test framing only; later they become JSON.

```text
Message A payload: UP
Frame A: 00 00 00 02 55 50

Message B payload: DOWN
Frame B: 00 00 00 04 44 4F 57 4E
```

Predict each result before running it:

| Input chunks in order | Expected emitted payloads |
|---|---|
| Whole A, then whole B | UP, then DOWN |
| Whole A and B in one chunk | UP, then DOWN |
| 00 00 / 00 02 55 / 50 | Nothing, nothing, then UP |
| A followed by first three bytes of B's header | UP; retain three bytes |
| Remaining byte of B's header and its complete payload | DOWN |
| Length 00 00 10 01 | Reject 4097 before allocating that payload |
| Length 00 00 00 00 | Reject under this lab's nonempty-message rule |
| EOF after header length 4 and only bytes 44 4F | Truncated-frame error |
| EOF with no partial header/payload | Clean end of framed input |

The slash separates decoder feed calls; it is not a transmitted byte. Feed every split point of the combined A+B byte array to test all two-chunk partitions. Also feed one byte at a time. Multiple frames may arrive per read, and one frame may require multiple reads.

Maintain parser state for header and payload with bounded retained data. Validate declared length immediately; never allocate based on an untrusted length first. Process complete frames as they become available. If your read API returns both bytes and an error, handle those bytes before interpreting the terminal condition.

## 4. Connect framing to the game

Replace UP with a small input envelope containing type, match, sequence, and axis. Identify two different validations:

- Framing: are the expected number of bytes present within the size limit?
- Application: is this message valid for this player, match, phase, and input range?

A perfectly framed SetScore(999) is still unauthorized. A perfectly valid movement command split over three reads must still be accepted once complete.

Run a server that applies a command and deliberately closes before sending the application result. The client cannot infer whether the command ran from its connection error alone. Record an operation ID so a later application-level query/retry can recover the outcome; I03 explains that contract.

## 5. Evidence and instructor answer prompts

Submit decoder test output, annotated connection trace or provided trace analysis, and a timeline of a slow-reader experiment. Set a finite write deadline so the slow-reader experiment terminates.

Explain these observations:

- One write did not equal one read: the API is a byte stream.
- A later update waited after loss: in-order delivery can delay fresher data.
- A repeated application request ran twice without deduplication: TCP byte recovery is not application idempotency.
- A silent peer did not immediately disconnect: absence of traffic is not immediate proof of failure.
- A successful write did not prove the score was saved: local I/O progress and durable application completion are different boundaries.

For the underlying protocol contract, use the TCP and GO-NET readings in the [source guide](../../../SOURCES.md).
