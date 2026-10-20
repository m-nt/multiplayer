# B08 Teaching Materials — Player and Match Lifecycle

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Distinguish connection, match membership, and spawned representation.
- Build a guarded match-state transition table.
- Choose explicit disconnect, late-join, and restart policies.
- Explain why cleanup and pending-start cancellation must tolerate repeated callbacks.

## Topics in detail

### 1. Connection is only the first lifecycle

A socket can be connected while the application has not admitted a player. A player can be admitted while waiting for a representation to spawn. A representation can remain visible briefly during cleanup. Name these states instead of using one isConnected flag for all of them. Otherwise a premature input handler may manipulate an object that has no valid match membership.

### 2. Match phases and guarded transitions

Use Waiting, Starting, Playing, Finished, and Cleaned as a teaching model. A transition has a trigger, a guard, an owner, and effects. For Waiting → Starting, the trigger might be both players ready; the guard verifies two current members; the server owns the change. Starting is useful because a countdown can still be cancelled if membership changes.

### 3. Disconnect policies are game rules

A disconnect during Waiting can simply free a slot. During Playing, the beginner policy ends the match with a disconnected outcome. A different game could pause or allow a grace period, but that requires additional identity, timing, and fairness rules. Do not accidentally let callbacks decide the policy differently on different clients.

### 4. Late join and reconnect are distinct

Late join introduces a new participant after the session started. Reconnect attempts to restore a previous participant. The beginner lab rejects active-match late joins and treats a new connection as a new session. Resuming old ownership securely is deferred; accepting a familiar display name is not proof of the returning player's identity.

### 5. Cleanup includes work that has not fired yet

Remove membership, objects, queued input, timers, subscriptions, and pending callbacks as appropriate. If a countdown callback survives a disconnect, it may start an invalid match later. Use cancellation or a lifecycle generation guard. Calling cleanup twice should not release someone else's new slot or award a second result.

## Worked walkthrough

Both players are ready, so the server schedules a two-second start countdown. After one second, B disconnects. The server cancels the pending start and returns A to Waiting. If the old timer still fires, it must check that its generation and membership assumptions remain valid and do nothing. A replacement B joining later belongs to a new transition attempt.

## Demonstration and guided practice

1. Build the state table before running the game and predict each disconnect outcome.
2. Disconnect at Waiting, Starting, Playing, and Finished in separate runs.
3. Repeat connect/disconnect ten times and count members, objects, timers, and callbacks that should remain.

## Common misunderstandings

- “A reconnecting username is the same player.” Names are not authentication.
- “Destroying the paddle completes cleanup.” Queued work and membership can outlive the visible object.

## Check your understanding

**1. Why do transition guards matter if only the server changes state?**

Expected reasoning: Server callbacks still arrive at different times; an earlier condition may no longer hold when delayed work executes.

**2. What makes cleanup idempotent?**

Expected reasoning: Repeating it produces the same cleaned result without harming newly created state or repeating effects.

## Independent exercise

Write a transition matrix with one row for each phase and columns for join, ready, disconnect, win, and shutdown. Implement the chosen beginner policy and include a stale-countdown reproduction in your evidence.

## Connections and reading

Framework lifecycle material supplies callbacks. I02 introduces session generations; A08 extends the same ideas across multiple services. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
