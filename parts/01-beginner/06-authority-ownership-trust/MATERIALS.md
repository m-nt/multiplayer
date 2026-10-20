# B06 Teaching Materials — Authority, Ownership, and Trust

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Assign input authority and state authority separately.
- Trace untrusted client values to the server mutation that must validate them.
- Derive player identity from the connection rather than a freely chosen payload field.
- Demonstrate that rejected score and movement claims have no authoritative effect.

## Topics in detail

### 1. A trust boundary is crossed by every client request

The server receives bytes from software it does not control. A legitimate account can run a modified client. Treat the decoded payload as a request, not as a fact about the world. This is why hiding a button or clamping a value only inside the client cannot protect the shared score.

### 2. Ownership is narrower than permission

Owning a paddle means the server associates that player with the relevant input object. It does not grant permission to teleport, set arbitrary speed, write the other paddle, or award points. Write permissions by operation: may submit input for self; may not set score; may request readiness only in Waiting.

### 3. Resolve the actor at the receiving boundary

Use the admitted connection to find the player. If a message includes a target ID, compare it against what that actor is allowed to target. Do not let a payload's playerId select the acting identity. Even a framework's default ownership gate does not replace validation of the action's values and phase.

### 4. Validate before the authoritative mutation

Check that the match exists, the phase permits input, the sender owns the paddle, and the axis is finite and within the agreed range. Then move using server-controlled speed and time. Rejection must leave the state unchanged. Log a reason code so tests can distinguish rejection from a message that never arrived.

### 5. Put scoring behind the game rule

The server's goal-crossing rule awards the point once and resets the round according to the game contract. A client may display a celebration, but cannot submit 'my score is now 9' as truth. This eliminates a direct score-writing path rather than trying to detect every forged score afterward.

## Worked walkthrough

Suppose a client submits {playerId:'other', axis:1, speed:999}. The handler identifies the actor from the connection, rejects an unauthorized target, and does not use a client speed field. For a valid self-input, it applies axis × serverSpeed × fixedStep. The same validly connected client sending SetScore(999) is rejected because that operation is not in the accepted client protocol.

## Demonstration and guided practice

1. Run the isolated insecure score setter and observe how little effort a forged claim requires.
2. Replace it with server-owned scoring, then rerun exactly the same attack input.
3. Send legal movement, wrong-target movement, an out-of-range axis, and input after Finished. Record acceptance/rejection and before/after state.

## Common misunderstandings

- “The player is authenticated, so their payload is trustworthy.” Identity does not grant arbitrary actions.
- “Owning an object means owning all its state.” Input rights and authoritative decisions must be defined separately.

## Check your understanding

**1. Why keep server validation if the normal client already clamps axis?**

Expected reasoning: A modified client can bypass that local clamp; only the authority can enforce the shared rule.

**2. What evidence proves rejection?**

Expected reasoning: The server records the reason and authoritative state remains unchanged, while a valid control case still succeeds.

## Independent exercise

Build an authority table for input, score, ready, restart, and local camera. For each network operation, name the actor, valid phase, target permission, value checks, and state mutation. Demonstrate two forbidden actions and one valid action.

## Connections and reading

MIRROR authority is the framework reference. I02 adds time-dependent ownership and session replacement; I15 adds bounded abuse tests. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
