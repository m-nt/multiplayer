# I02 Teaching Materials — Authority Models in Depth

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Model permission as a function of actor, target, phase, and current session generation.
- Distinguish ownership transfer from state-authority transfer.
- Reject delayed commands from an obsolete connection.
- Explain host advantage and client-side presentation without treating them as identical authority problems.

## Topics in detail

### 1. Permission changes with time

A command valid when received can become invalid before execution. The player may leave, the round may finish, or another connection may replace the session. Carry enough context to validate at application time, including match and connection generation. Do not cache a boolean permission decision indefinitely in the packet object.

### 2. Actor, target, and operation

The actor comes from the admitted connection. The target is the object the action concerns. The operation defines the right needed. A player may control their own paddle, observe both paddles, and have no permission to modify either score. Visibility and authorization are separate: seeing an object does not grant mutation rights.

### 3. Session generations prevent stale work

When a new connection replaces an old one, advance a server-owned generation. Commands from the old generation fail even if they reference the same account and paddle. Disconnect cleanup should also compare generation before removing membership; otherwise an old callback can disconnect the newly established session.

### 4. Transfer is a transaction in game terms

If ownership changes, remove the old writer, clear or invalidate pending intent, assign the new writer, and publish the new state under a coherent rule. Do not temporarily allow both connections to issue accepted control. Server-owned ball/score state does not need to transfer merely because a paddle changes input owner.

### 5. Choosing authority by mechanic

Camera motion and local cosmetic anticipation can remain local. Movement can be predicted for responsiveness while the server decides accepted state. Inventory/reward effects need a trusted owner and durable contract. Host advantage involves both control over the process and the local timing path; dedicated hosting addresses some of those concerns but not handler correctness.

## Worked walkthrough

Current membership is player A, paddle left, generation 8. A delayed generation-7 command arrives after reconnect. A handler that checks only player A will accept stale input incorrectly. Check match, membership, generation, phase, and target immediately before applying the step. An old generation-7 disconnect callback must also fail its conditional removal, leaving generation 8 intact.

## Demonstration and guided practice

1. Pause an input in the test queue, replace the session, then release it. Expect rejection by generation.
2. Delay the old disconnect callback until after reconnect. Expect the new session to remain admitted.
3. Transfer an input slot in a controlled test and prove that only one connection can influence it at a time.

## Common misunderstandings

- “Account identity alone identifies a live connection.” One account can have multiple historical connection attempts.
- “Hidden objects are secure.” Replication visibility does not substitute for command authorization.

## Check your understanding

**1. Why does cleanup need generation checks too?**

Expected reasoning: Stale callbacks can otherwise remove new ownership that reused the same player or slot identifier.

**2. Does client prediction imply client authority?**

Expected reasoning: No. The client can anticipate a result while the server retains the right to accept and correct it.

## Independent exercise

Create a table of all mutation operations with actor, target, match phase, generation check, and authoritative writer. Test stale input, stale cleanup, wrong target, and a valid replacement-session command.

## Connections and reading

Review framework authority only as an implementation comparison. I11 replaces local test identity with authenticated admission. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
