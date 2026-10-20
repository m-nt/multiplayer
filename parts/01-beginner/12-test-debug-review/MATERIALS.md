# B12 Teaching Materials — Testing, Debugging, and Architectural Review

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Localize networking failures by following the first missing boundary.
- Write reproducible tests with setup, action, expectation, and observation.
- Use match/session/tick identifiers to connect logs across processes.
- Explain the beginner architecture and its remaining limits without reading code aloud.

## Topics in detail

### 1. Build a diagnostic ladder

Check process startup, configured address, listener, connection, admission, input receipt, state mutation, snapshot send, and display application in that order. The first missing observation narrows the search. A firewall issue is not fixed by changing interpolation, and a wrong-match rejection is not a TCP connection failure.

### 2. Addresses belong to the caller's environment

Localhost refers to the machine making the connection. A remote client connecting to localhost will look for a server on itself. Compare the configured endpoint with the server's reachable address and selected interface. Separate address errors from a server that accepted the connection but rejected the game session.

### 3. Useful logs connect events

Record role, match ID, session ID, tick, message type, and outcome as appropriate. Keep them structured enough to filter. Do not log credentials or every payload by default. A trace of one input through several processes is more informative than hundreds of unlabeled 'received' lines.

### 4. Testing is a repeatable experiment

Specify the build/configuration, number of processes, impairment, action, expected state, and observed state. Include a valid control case when testing rejection so a broken listener is not mistaken for successful validation. Keep raw observations separate from your explanation of their cause.

### 5. Architecture review is about responsibility

Explain who owns each state, how another process learns about it, what happens on disconnect, and what survives restart. A student who can name APIs but cannot identify the score writer has not demonstrated the architecture. State what has not been tested, especially public deployment or target-device performance.

## Worked walkthrough

Client B cannot move. The process is running and the server logs a connection, so startup and routing worked. The next log shows Reject(Input, NotAdmitted). The likely boundary is membership/admission, not serialization of position snapshots. After fixing admission, rerun a legal input and then a deliberately unauthorized input to confirm both paths.

## Demonstration and guided practice

1. Inject one wrong address/port and one wrong-phase action in separate runs. Expect different failure evidence.
2. Run the host, separate-server, second-machine, delay, and disconnect matrix.
3. Ask a partner to reproduce one failure from the written report without verbal guidance.

## Common misunderstandings

- “No movement means packets were lost.” Many application guards and display bugs produce the same symptom.
- “A screenshot is a complete test report.” It does not establish setup, sequence, or repeatability.

## Check your understanding

**1. Why include a legal control case in an attack test?**

Expected reasoning: Otherwise all traffic might be broken, making rejection appear correct when no messages were processed.

**2. What must be stated about untested deployment?**

Expected reasoning: That remote lab play does not establish public-network readiness or production reliability.

## Independent exercise

Submit a diagnosis report for one injected fault: expected path, first missing boundary, relevant logs, minimal fix, and rerun evidence. Finish with an oral input-to-score explanation and the complete beginner completion matrix.

## Connections and reading

Use the shared teaching guide's test conditions. I01 builds on this evidence to separate simulation, transport, and presentation. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
