# B04 Teaching Materials — Game State and Synchronization

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Separate state, input, command, and event using concrete game values.
- Design a snapshot that initializes a late-joining display.
- Assign stable identities and a writer to each replicated object.
- Choose update policies for frequent state and occasional presentation events.

## Topics in detail

### 1. Classifying information by its job

State describes a fact now: score is 3, phase is Playing. Input is a sampled control, such as axis -1. A command asks for an operation, such as Ready. An event reports an occurrence, such as PointAwarded. The same event can change durable state and trigger a local effect, but the effect is not the state itself. A sound cannot reconstruct the score after reconnect.

### 2. Choosing the authoritative state set

Start with the game's rules: ball position/velocity, paddle positions, scores, phase, and round identity. For each field specify one writer and the readers that need it. Leave camera shake, button hover, and local settings outside unless the rules require them. Sending everything makes ownership harder to understand and adds unnecessary traffic.

### 3. Snapshots as labeled observations

A snapshot samples authoritative values at a particular tick. Include its match and round context so data from an older round cannot initialize a new one incorrectly. Clients compare the tick to reject stale state according to the protocol. Sampling several related values coherently avoids a ball from one tick paired with a score from another.

### 4. Identity and object lifetime

A snapshot must refer to the same logical paddle across updates. Stable IDs let the receiver update the existing representation. Array position alone can become ambiguous when players leave. Decide separately when an object appears, changes, and disappears. Repeated state does not mean 'spawn another copy.'

### 5. Initial synchronization versus ongoing changes

A joining client needs a current baseline before incremental changes make sense. Send enough state to display the court and scoreboard without replaying old celebrations. Ongoing updates can later carry changed values, but that requires a known baseline and repair behavior. Complete small snapshots keep the beginner implementation easier to verify.

## Worked walkthrough

At tick 500 the server has scores 3–2 and two paddles identified as left-slot and right-slot. A new viewer receives that snapshot and creates or updates exactly those two representations. The next snapshot changes only positions but still refers to the same IDs. The viewer should show 3–2 immediately without playing five point sounds or spawning two more paddles.

## Demonstration and guided practice

1. Color-code the game's variables as authoritative state, local presentation, input, or event.
2. Connect a viewer after several points and compare event-only initialization with full-state initialization.
3. Apply the same snapshot twice. Expect unchanged authoritative meaning and no duplicate objects or reward effects.

## Common misunderstandings

- “Every repeated message should replay its animation.” State refresh is not necessarily a new event.
- “A delta is enough for a new client.” A delta needs an agreed baseline or a complete initialization path.

## Check your understanding

**1. Should the point sound be in every snapshot?**

Expected reasoning: Usually no; the current score is state, while the sound belongs to a newly observed event with its own duplicate policy.

**2. Why attach a tick to state?**

Expected reasoning: It identifies when the state was sampled and helps prevent an older update from overwriting newer state.

## Independent exercise

Write a snapshot specification with units, object IDs, match/round context, and tick. Test a viewer joining at a nonzero score, receiving a duplicate snapshot, and receiving an old-round snapshot after restart.

## Connections and reading

MIRROR synchronization illustrates an implementation. B10 examines delivery/freshness; I10 introduces baseline-dependent compression. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
