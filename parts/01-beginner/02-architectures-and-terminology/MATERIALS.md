# B02 Teaching Materials — Architectures and Networking Terminology

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Draw dedicated, listen-server, peer, and relay arrangements without conflating topology with authority.
- Compare host departure, connectivity, operating cost, and trust for a concrete game.
- Distinguish a game server from an account or matchmaking backend.
- Explain what a relay can and cannot establish about gameplay correctness.

## Topics in detail

### 1. Separate the topology question from the authority question

Topology asks which processes communicate. Authority asks which participant may make a particular decision. Draw arrows first, then label the writer of each important state value. A peer network can elect one peer to decide a puzzle result. A client-server arrangement can still be insecure if its server blindly accepts a client's claimed score.

### 2. Listen servers and host lifetime

A listen server shares a player's session or process. This is convenient for small cooperative sessions because the host already runs the game. The host's departure removes the authority unless host migration is separately implemented. The host may also have a shorter local path to its server role than remote participants. Document those consequences rather than treating them as accidental bugs.

### 3. Dedicated servers and operational responsibility

A dedicated process runs without a local participant. Players become clients of that process, and someone must provide compute, network access, supervision, and updates. This separates participant control from the server machine but introduces operational work. A dedicated server is not automatically authoritative: its message handlers still determine which values it trusts.

### 4. Peer communication and relays

Peers exchange traffic directly or through intermediaries. Direct inbound connectivity may be difficult when routers/firewalls restrict it. A relay forwards traffic along an available path; it does not inherently run ball physics or validate a hit. Distinguish a relay outage from an authority-process outage. Both can interrupt play, but recovering one does not recover the other's state.

### 5. Backend services and deployment size

A backend can retain identity and result history across matches. A game server usually owns one live match in this course. These can run on the same machine while remaining separate components. Do not create five network services just because a diagram has five responsibilities; first identify lifetimes, failure boundaries, and required scale.

## Worked walkthrough

Consider a two-player cooperative game with no persistent rewards. A listen server may be acceptable if both players agree the session ends when the host leaves. Now add ranked rewards and untrusted participants. The authority process becomes a more sensitive trust boundary, making independent hosting worth evaluating. A relay solves neither reward authorization nor host manipulation; it addresses a different part of the connection path.

## Demonstration and guided practice

1. Annotate the existing host-mode diagram with the score writer and all process lifetimes.
2. Stop a non-host client, then the host, in separate runs. Compare which responsibilities disappear.
3. Redraw the same match with a dedicated process and with a relay in front of a host. Keep authority labels explicit in both drawings.

## Common misunderstandings

- “Peer-to-peer means every peer has equal authority.” Communication and decision rights are separate.
- “A relay removes cheating.” Forwarding packets does not validate their game meaning.

## Check your understanding

**1. Why does dedicated hosting not remove the need for score validation?**

Expected reasoning: The server's implementation may still accept an untrusted client claim; machine placement cannot repair that handler.

**2. What extra feature is required to continue after the host leaves?**

Expected reasoning: A defined authority/state transfer and connectivity process; it does not appear automatically when peers can communicate.

## Independent exercise

Choose a topology for a private cooperative session, a ranked paddle game, and a browser demonstration. For each, list who pays for compute, who owns score, what happens when the authority disappears, and what connectivity assumptions must hold.

## Connections and reading

B06 turns authority labels into checks. I06 implements a dedicated process. MIRROR supplies framework terminology, not a universal architecture prescription. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
