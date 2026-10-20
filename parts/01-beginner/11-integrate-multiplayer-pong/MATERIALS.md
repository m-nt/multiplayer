# B11 Teaching Materials — Integrate the Multiplayer Pong Game

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Compose the previously built paths into one complete match.
- Identify the single authoritative ball/collision/scoring loop.
- Reset session and round data without carrying stale input forward.
- Verify behavior in separate processes rather than relying only on host mode.

## Topics in detail

### 1. Integration has an order

Start from a connected but unadmitted client, move through membership and readiness, enable input only in the permitted phase, advance rules, send state, display the outcome, and clean up. Each component must agree on identifiers and lifetime. A correct snapshot function attached to the wrong match is still an integration failure.

### 2. One authoritative physics path

Clients may animate the ball, but the server decides collisions and scoring. Ensure a client-side collision callback does not also increment a score or reset the round. Rendering an object and simulating an authoritative object are different responsibilities even when they look identical in the editor.

### 3. Input ownership and reset

Resolve input to the current admitted player's paddle. On restart, clear prior-round queued samples and define the initial control value. If a held button should remain held across a round, implement that as an explicit rule; do not let stale network state accidentally choose it. A new participant must not inherit another participant's pending commands.

### 4. Presentation reacts to accepted state

The scoreboard, win panel, and ready controls reflect authoritative phase and score. Local cosmetic effects can be immediate where appropriate, but they must not mutate the shared result. Handle the moment when final state arrives before cleanup or a disconnect notification so the UI does not erase the result prematurely.

### 5. Host mode can conceal dependencies

A host has both roles locally and may accidentally invoke server logic through a shared reference. A separate server plus two standalone clients forces messages across process boundaries. Test that the server can advance a match without a camera, local input device, or host player's object.

## Worked walkthrough

Walk one complete point: A's input moves its paddle at tick 100; the server advances the ball; the goal rule awards B one point; the round changes phase; a snapshot reports the new score; both clients update the display. Record that the goal rule executed once. Repeat in a server-only process so no client collision callback can be responsible for the point.

## Demonstration and guided practice

1. Follow a single match ID through join, ready, input, goal, finish, and cleanup logs.
2. Run the complete path with no host player and two standalone clients.
3. Restart after a player held movement during the final point; inspect whether the documented reset policy holds.

## Common misunderstandings

- “It worked inside one editor, so integration is complete.” Shared process state can conceal missing network paths.
- “Both clients should run the same scoring callback.” Shared visual code does not mean shared authority.

## Check your understanding

**1. Where should a final-score disagreement be investigated first?**

Expected reasoning: Identify the authoritative score and compare the received snapshots and client application path before changing game rules.

**2. Why clear old queued input on a new match?**

Expected reasoning: Its ownership, timing, and round assumptions may no longer be valid.

## Independent exercise

Create a one-page integration checklist with observable evidence at each boundary. Have another student run a match, restart it, and disconnect during play using only that checklist and the project's launch instructions.

## Connections and reading

Review B04–B10 artifacts. B12 turns this integrated build into a reproducible test and explanation. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
