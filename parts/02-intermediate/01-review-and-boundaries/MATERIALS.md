# I01 Teaching Materials — Review and Redesign the Beginner Architecture

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Identify coupling that prevents isolated rule testing or server-only execution.
- Define simulation, protocol adapter, and presentation responsibilities.
- Specify ownership/lifetime for mutable state instead of introducing arbitrary layers.
- Preserve observable behavior while replacing a network boundary.

## Topics in detail

### 1. Begin with a failing change scenario

A useful boundary solves a concrete problem. Ask what happens if you run collision rules without Unity rendering, replace the transport, or test a match with no connected client. A camera reference inside scoring prevents server-only execution; a socket write inside movement prevents deterministic rule tests. Use those failures to decide where to separate code.

### 2. The simulation contract

Define a step operation with explicit state, validated input, and fixed duration. Its output is the next authoritative state plus accepted domain occurrences, such as a point. It must not require a network connection to calculate the result. This does not require a generic engine: a small function operating on a MatchState is sufficient for the teaching game.

### 3. Adapters translate, not decide the rules

An inbound adapter decodes bytes, resolves the sending session, performs structural checks, and offers a bounded command to the simulation. The simulation still checks time-sensitive permissions before mutation. An outbound adapter serializes a snapshot. A presentation adapter applies accepted state to transforms/UI. Each translates between a boundary and the game contract.

### 4. Ownership is more useful than file count

For every collection, answer who creates, mutates, reads, and releases it. A match owner can maintain paddles and pending inputs; a connection manager owns socket lifetime. Passing a mutable state pointer to several workers destroys that clarity even if the files live in beautifully named folders. Prefer immutable snapshots or copied values at concurrent boundaries.

### 5. Refactor in verifiable increments

Capture fixtures and a baseline match before moving code. Extract one rule or boundary, run its fixture, then rerun the integrated path. Preserve Unity scene references and serialization contracts unless a change is explicitly required. Interfaces are useful where a dependency is replaced, such as a clock or result sink; an interface for every class adds little teaching value.

## Worked walkthrough

Before: a message callback moves a Unity transform, checks a goal, writes a score label, and sends a socket response. After: the callback supplies InputIntent to a match owner; Step produces state and PointAwarded; a snapshot builder creates outbound values; the UI reads the accepted state. Test a goal by calling Step with a known ball state, then separately test that the adapter encodes its snapshot correctly.

## Demonstration and guided practice

1. Mark each line of one existing handler as transport, identity, rules, or presentation.
2. Extract the movement rule and run the court-edge fixture without a connection.
3. Replace the outbound adapter with a recorder; the simulation should still advance and the recorder should capture expected snapshots.

## Common misunderstandings

- “Separation means many services.” These boundaries can remain simple functions/modules in one process.
- “The adapter validated once, so simulation need not recheck.” Membership or phase may change before queued execution.

## Check your understanding

**1. Where should socket retry logic live?**

Expected reasoning: Outside the rule step, in an I/O/lifecycle component with its own time and retry bounds.

**2. What must a test of scoring avoid depending on?**

Expected reasoning: A camera, live socket, arbitrary wall-clock sleeps, or client display callbacks.

## Independent exercise

Refactor one coupled path while preserving baseline behavior. Submit a dependency sketch, an ownership table, one pure rule fixture, and a before/after match trace explaining the observable equivalence.

## Connections and reading

The Go bridge prepares value ownership and tests. I07 applies these boundaries to concurrent workers; I08 defines the exact step schedule. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
