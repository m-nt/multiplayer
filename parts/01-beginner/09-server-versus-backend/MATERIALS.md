# B09 Teaching Materials — Game Server versus Backend Services

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Assign live rules, identity, history, and matchmaking to appropriate responsibilities.
- Separate ephemeral memory from durable storage.
- Create a match-result handoff that does not run inside every tick.
- Represent backend failure without pretending the match outcome never happened.

## Topics in detail

### 1. Live simulation and long-lived services

The server's tick updates ball and paddle state repeatedly. An account service may need to remember a player across months and many matches. These workloads have different timing and lifetime needs. Calling an account API for every movement would make the simulation depend on unrelated service latency.

### 2. Persistence has a failure boundary

A value in an in-memory dictionary disappears when that process loses its memory. A durable database record has a different recovery promise, dependent on its configuration and committed transaction. For now, a fake result sink teaches a boundary only. Label it non-durable so students do not mistake a successful function call for persistent storage.

### 3. Results are produced by the authority

At match completion, form a record containing match identity, participants, final scores, and outcome reason. The producer is the authoritative server, not a client UI reporting what it displayed. The result's identity will later help recognize retries. A disconnect outcome may contain different semantics from a normal win, so represent it explicitly.

### 4. Handoff instead of tick blocking

The server can queue a completed result for a separate submission operation. It should not wait for a slow API inside the movement/collision loop. This requires visible pending or failed submission status, not silent discard. At beginner level, a callback or fake sink is enough to observe the distinction; durable queue recovery arrives later.

### 5. Logical boundaries do not demand separate machines

The result receiver and live game may run locally during teaching. Keeping an explicit contract still helps test failure and change implementation later. A diagram of independent responsibilities is not an instruction to buy one machine per box. Choose physical deployment after the behavior and lifecycle are understood.

## Worked walkthrough

A match ends 5–3 and the server constructs result m12 with outcome Completed. The fake backend delays five seconds. The client can show the authoritative final score while the system marks result submission Pending. If the fake fails, show Failed/PendingRetry according to the lab policy; do not change the score to 0–0 or claim the record is durable.

## Demonstration and guided practice

1. Replace a fast result sink with a delayed sink and inspect which loop is blocked.
2. Force the sink to fail and trace final-score state separately from submission state.
3. Restart the fake receiver and demonstrate that an in-memory result history does not survive.

## Common misunderstandings

- “A backend is always a separate computer.” It is first a responsibility boundary.
- “The UI shows the final score, so the result is saved.” Display and durable commit are different events.

## Check your understanding

**1. Why does the server submit results instead of either player?**

Expected reasoning: It owns the accepted outcome and can later authenticate its authority to the backend.

**2. Can a match finish while storage is unavailable?**

Expected reasoning: It can reach a gameplay outcome, but the platform must apply an explicit pending/failure policy for durability.

## Independent exercise

Define a result record and three sink behaviors: success, delay, and failure. Capture the game's phase and submission status for each. Explain exactly which behavior the fake does not prove about durability.

## Connections and reading

I11 builds real identity and transactional storage. A12 studies what happens when processes fail around the durable handoff. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
