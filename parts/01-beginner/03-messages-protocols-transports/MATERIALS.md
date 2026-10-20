# B03 Teaching Materials — Messages, Protocols, and Transports

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Turn a gameplay intention into a message with explicit fields, types, and units.
- Distinguish serialization, schema, protocol, transport, and framework.
- Classify TCP, UDP, HTTP, WebSocket, Socket.IO, and RPC by responsibility.
- Identify syntactically valid data that is semantically invalid for the game.

## Topics in detail

### 1. Start with meaning before bytes

Write the intended operation in a sentence: 'the admitted player requests upward paddle movement for this input sample.' Then choose fields that represent that meaning. An axis field is not a position or an arbitrary speed. Include identifiers only when they serve a purpose. An account name sent in JSON is merely a claim until the server establishes who sent it.

### 2. Serialization and schemas

Serialization converts values into bytes; decoding reconstructs values. A schema defines their interpretation. JSON helps humans inspect fields, while a binary format can encode known types compactly. Neither chooses the correct units or allowable ranges for you. The value 100 may mean milliseconds, ticks, centimeters, or points; the sender and receiver must agree before transport matters.

### 3. Protocols include the conversation

A protocol is more than a collection of data structures. It specifies that Join precedes Input, what a rejection looks like, whether a retry is allowed, and what a disconnect means. Two applications can parse identical JSON yet disagree because one expects Ready before Start and the other starts immediately. Teach protocol state alongside message shape.

### 4. The communication layers

In the TCP lab path, TCP transports ordered bytes. UDP exposes datagrams. HTTP defines request/response semantics; WebSocket provides a persistent bidirectional message channel. RPC makes a remote request look like an operation in code. A networking framework adds game-specific conveniences such as object identity and replication. These layers can be combined, so choosing one is not always choosing against the others.

### 5. Inspecting payloads without trusting them

Validate types, sizes, and game meaning independently. The string 'up' is not a numeric axis; axis 500 is numeric but out of range. A score-setting command can be perfectly encoded while forbidden. Treat readable packets as debugging evidence, not proof of authenticity. Encryption also changes what a packet capture can inspect without an application-side trace.

## Worked walkthrough

Begin with {"type":"input","axis":1}. Describe axis as a dimensionless control in [-1,1], not speed. Add a sequence to correlate application samples. Then try {"type":"input","axis":"1"} and {"type":"input","axis":500}. The first violates the chosen type contract; the second violates the range contract. Neither should become valid through a silent fallback to a default movement value.

## Demonstration and guided practice

1. Write one input and one snapshot on the board. Label fields with types and units.
2. Encode/decode them using the prepared tool, then change a field type and observe the defined rejection.
3. Draw the actual project stack from gameplay intention to transmitted bytes. Identify which layer assigns object IDs and which carries traffic.

## Common misunderstandings

- “JSON is a networking protocol by itself.” It does not define the allowed game conversation.
- “RPC is a transport like UDP.” It is an operation abstraction that still requires a communication mechanism.

## Check your understanding

**1. Can two ordinary WebSocket endpoints exchange Socket.IO events automatically?**

Expected reasoning: Not merely because both use WebSocket somewhere; they must implement compatible application framing and protocol behavior.

**2. Does decoding successfully permit movement?**

Expected reasoning: No. Admission, phase, ownership, and value checks still apply.

## Independent exercise

Create a six-row message table covering input, state, event, join, rejection, and result. Give each a sender, receiver, payload meaning, and valid phase. Explain the actual layers used by your cohort's framework transport.

## Connections and reading

I04 implements the transport experiments. Use TCP, UDP, and WEBSOCKET introductory readings; detailed packet fields are deferred. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
