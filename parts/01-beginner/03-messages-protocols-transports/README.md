# B03 — Messages, Protocols, and Transports

[Part overview](../README.md) · [Previous](../02-architectures-and-terminology/README.md) · [Next](../04-state-and-synchronization/README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Narrow the original technology survey to observable messages and layering; deeper transport work belongs to I04.

**Time:** 2 hours. **Prerequisite:** B02 and its passing lab.

**By the end:** Describe serialization and distinguish transport, application protocol, and framework.

**Boundary:** Inspect framework traffic and sample payloads; do not write a transport.

## Teaching notes

A message is meaningful application data, such as an input axis. Serialization turns it into bytes. A schema states how to interpret those bytes: field names, types, units, and meaning. A protocol adds the allowed sequence of exchanges and what happens on invalid input.

TCP supplies an ordered byte stream; UDP supplies datagrams without delivery or ordering guarantees. HTTP expresses application requests and responses. WebSocket provides bidirectional messages. RPC presents a remote operation as an API call, but the operation still has network delay and can fail. Mirror combines higher-level game networking with a selected transport.

JSON is readable but still needs a schema. Binary is not automatically smaller or faster for every payload. Socket.IO adds its own protocol and is not interchangeable with an ordinary WebSocket endpoint.

## Worked example

The local value axis = 1 becomes {"type":"input","axis":1}. A receiver must know that 1 means upward input, not position 1 or speed 1. Serialization alone cannot supply that agreement.

## Guided lab

1. List four messages from the demo and classify input, state, event, or request.
2. Write and decode a JSON input by hand, including its units.
3. Change a field type in a disposable sample and record why parsing or validation fails.
4. Draw game logic → framework/protocol → transport → network.

## Evidence required to pass

- A message table states sender, receiver, meaning, and type.
- The student places RPC and TCP at different abstraction layers.
- Malformed data is identified rather than treated as a valid default input.

## Explain independently

Why does receiving valid JSON not prove a valid player action?

## Reading and preparation

TCP and WEBSOCKET: read only the introductions; return to lifecycle details in I04. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
