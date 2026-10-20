# B01 Teaching Materials — What Makes a Game Multiplayer?

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Identify processes, their separate memory, and the messages connecting them.
- Trace input → authoritative decision → replicated state → displayed frame.
- Distinguish a local visual change from an accepted game-state change.
- Explain why one game's state can continue while another client's display stops.

## Topics in detail

### 1. A running game is a process with private state

A project folder is not the running game. When you launch a build, the operating system starts a process with its own memory. Launching the same build twice creates two independent copies of variables such as ballPosition and score. Changing a variable in one copy does not change the other. Start by drawing two boxes containing different score values; a network message is the explicit bridge between them.

### 2. What must agree, and what may differ

The two players need to agree on who scored, whether a round is active, and where authoritative collisions occur. They do not need identical camera positions, audio volume, or every rendered pixel. A client may display a slightly older ball position because information takes time to arrive. Agreement therefore means a common decision history and a defined way of presenting it, not that both screens are identical at every instant.

### 3. Client and server are responsibilities

A client samples its player's controls and presents the match. The authoritative server runs the rules and decides shared outcomes. A host can run both roles in one process, but the responsibilities remain different. When drawing the host, put a visible boundary between the client role and server role. Otherwise a successful local function call can hide the network exchange that a remote player needs.

### 4. Intent, decision, and replica

An input such as axis = 1 expresses what the player wants to do. The server converts it into movement according to speed, elapsed simulation step, court boundaries, and match phase. The resulting position is a decision. Each client receives a replica of that position for display. A client changing its own replica to 999 does not become a legitimate movement decision.

### 5. Why independent simulations drift

Two machines may sample input at different moments, process different numbers of frames, or receive different collision inputs. Small discrepancies can affect the next collision and then the score. Networking architecture decides how to resolve this. In this course, one authoritative simulation resolves the disagreement and distributes its result; clients do not vote on which score they prefer.

## Worked walkthrough

Use a paddle initially at 2.0 court units, speed 6 units/second, and a 1/60-second server step. Client A submits axis 1. The server computes 2.0 + 6 × 1/60 = 2.1, clamps to the court, and publishes 2.1 with a tick identifier. Client B might still display 2.0 until the update arrives. That temporary difference is not two valid authoritative positions. Ask students to point to the exact step where the requested move becomes the accepted move.

## Demonstration and guided practice

1. Launch the prepared demo twice and identify each process/log. Change one local-only display label; expect no change in the other process.
2. Trace a single input with a sequence label through sender, server application, and receiver. Expect distinct log entries, not one shared function stack.
3. Pause a remote client's presentation while leaving the server running. Expect the server tick and other client to continue; resume and inspect updated state.

## Common misunderstandings

- “Same code means shared state.” Each process owns separate memory.
- “The server is the computer with the monitor turned off.” Server describes a role; headless execution is a separate choice.

## Check your understanding

**1. Must cameras be synchronized?**

Expected reasoning: Only if gameplay explicitly depends on a shared camera. Ordinary local viewing preferences do not belong in the authoritative match state.

**2. What is missing if both players see their own paddle move but not the other's?**

Expected reasoning: Trace the communication and replication path; local input/rendering can work without transmitting or applying remote state.

## Independent exercise

Draw one complete input journey using five labeled boxes: client input, outbound message, server rule, snapshot, remote display. Add one failure at each boundary and predict which player notices it. Submit the diagram and a short trace from the supplied demo.

## Connections and reading

Continue to B02 for hosting arrangements and B04 for deciding exactly what belongs in a snapshot. MIRROR provides the framework authority reference. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
