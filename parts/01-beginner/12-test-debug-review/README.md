# B12 — Testing, Debugging, and Architectural Review

[Part overview](../README.md) · [Previous](../11-integrate-multiplayer-pong/README.md) · [Next](../README.md)

## Learning materials

Read the [detailed teaching chapter](MATERIALS.md) for exact learning outcomes, topic explanations, worked demonstrations, and questions with expected reasoning.

## Practicality and scope

Keep as a mandatory evidence gate, including another machine and an impaired run.

**Time:** 2–3 hours. **Prerequisite:** B11 and its passing lab.

**By the end:** Diagnose a failure using logs and defend the completed beginner architecture.

**Boundary:** Document limitations; do not claim production readiness.

## Teaching notes

Debug from the first missing boundary. Did the process start? Is the expected address/port listening? Did connection admission succeed? Did input reach the server? Did the tick change state? Did a snapshot reach the client? This narrows a vague 'networking is broken' report.

Use connection/session identifiers, ticks, and message types in logs, avoiding secrets. Distinguish the client's localhost from the server's address: localhost always refers to the machine running that client.

Reproducible tests state setup, action, expectation, and observation. Keep a baseline before applying delay. A screenshot proves appearance at one instant; a log plus a repeatable procedure explains behavior.

## Worked example

Client B cannot join from a second computer. Before changing replication code, check that it targets the server's LAN address and that the listener/firewall permit that path.

## Guided lab

1. Run a matrix: local host, separate server, second machine, delayed connection, mid-match disconnect.
2. Introduce one wrong port and diagnose it from evidence.
3. Have a partner reproduce a complete match from the run instructions.
4. Present the authority table and input-to-snapshot path without reading the implementation.

## Evidence required to pass

- Mandatory: two remote standalone clients complete a match.
- Mandatory: forged score is rejected and disconnect cleanup passes.
- Evidence records environment, impairment, observed failures, and remaining limitations.

## Explain independently

What changes between this classroom server and a deployable dedicated service?

## Reading and preparation

Review the teaching guide's shared test conditions and all beginner artifacts. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
