# B05 Teaching Materials — Client-Server Interaction Patterns

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Design request/response, command/result, notification, and snapshot flows.
- Separate sent, pending, accepted, and rejected UI states.
- Choose targeted versus broadcast recipients.
- Explain why calling an RPC cannot be treated as immediate local completion.

## Topics in detail

### 1. Requests and correlated responses

A request asks for a particular operation or answer. A response must be tied to that request, especially if several are pending. Correlation prevents a late response to an earlier action from updating the wrong UI. At beginner level, use one outstanding ready request or an explicit request label; later lessons generalize the identifier and retry policy.

### 2. Commands express intent, not entitlement

A Ready command asks the server to change membership state. The server checks sender membership and current phase before applying it. A command handler can return a rejection without making any state change. Design both outcomes first; otherwise clients often assume that every request must succeed and become stuck when the server refuses.

### 3. Notifications announce accepted facts

MatchStarted should follow the authoritative transition. Send it to participants who need to know. A rejection usually concerns the requester, while a lobby-state change concerns the group. Broadcasting private or irrelevant information is not a substitute for choosing recipients. Snapshot state remains useful for clients that did not see the original notification.

### 4. Remote calls have time and failure

A local function usually returns directly in the caller's control flow. A remote operation crosses queues, serialization, another process, and a return path. The UI can show Pending during that interval. A timeout is an observation about waiting; it is not always proof that the server rejected the action. Full duplicate-safe retry handling is taught later.

### 5. Events and state should tell a consistent story

If a start event says the match is Playing but the snapshot still says Waiting, the client needs a defined ordering or state version. At this level, publish the state change from the same authoritative transition and let clients use the resulting state as the source of truth. Do not start timers independently on each button click.

## Worked walkthrough

A presses Ready and sees Pending. The server associates the message with A's connection, checks Waiting, marks A ready, and returns Accepted. The shared lobby snapshot now shows A ready. When B becomes ready, the server performs one transition to Starting. If A repeats Ready after Finished, return Rejected(InvalidPhase) and keep the finished result intact.

## Demonstration and guided practice

1. Introduce visible delay between Ready and its response; ask learners which UI state should be shown.
2. Send Ready during Finished and confirm that no start animation or state transition occurs.
3. Disconnect before the response arrives and ensure the old response cannot affect a later session's UI.

## Common misunderstandings

- “Calling the RPC means the operation succeeded.” It only initiates a remote path.
- “The start event is all a new client needs.” A late client also needs current state and admission policy.

## Check your understanding

**1. Why target a rejection rather than broadcast it?**

Expected reasoning: It answers one caller and should not confuse other players' pending operations.

**2. What should the UI do after a timeout?**

Expected reasoning: Show an unresolved/retryable state under the protocol; do not invent acceptance or rejection.

## Independent exercise

Draw a sequence diagram for ready/start including a delayed result, a rejection, and disconnect. Label the exact server mutation and the clients' UI states at each point.

## Connections and reading

MIRROR remote actions provides direction and recipient concepts. I03 adds stable operation IDs and duplicate-safe retries. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
