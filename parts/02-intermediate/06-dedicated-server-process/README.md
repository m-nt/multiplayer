# I06 — Dedicated Game Server Architecture

[Part overview](../README.md) · [Previous](../05-protocol-and-schemas/README.md) · [Next](../07-go-server-and-client-adapter/README.md)

## Practicality and scope

Keep a framework-based dedicated build before replacing the server, so process boundaries are learned separately from a new language.

**Time:** 2–3 hours. **Prerequisite:** I05 and its passing lab.

**By the end:** Run a server with no local player and control it through configuration and shutdown.

**Boundary:** One match per process; fleet automation comes later.

## Teaching notes

A headless process omits rendering; it still needs simulation, collision, networking, and configuration. A server-only startup path must not access cameras, local input, audio, or client UI. Removing all physics because it sounds graphical would remove gameplay rules.

Configuration includes listen address, port, match identity, and logging. Fail fast on invalid values or a port conflict. 'Process exists,' 'listener open,' and 'ready for a match' are distinct states.

Graceful shutdown stops new admission, applies the chosen active-match policy, attempts bounded cleanup, closes connections, and exits. An infinite result retry is not a graceful shutdown strategy.

## Worked example

Start two copies on the same port. The second should fail clearly before registering as ready, rather than appearing healthy while accepting no players.

## Guided lab

1. Build/run the framework's server-only target without a local client.
2. Move endpoint and match ID to configuration.
3. Connect two standalone clients and complete a match.
4. Test port conflict and termination with an active match.

## Evidence required to pass

- No client-only dependency is required at server startup.
- Configuration errors produce a useful non-success exit.
- Shutdown completes within a declared bound and cleans sessions.

## Explain independently

Why is a listening socket insufficient evidence that the game server is ready?

## Reading and preparation

MIRROR: server builds and lifecycle; use documentation for the pinned Unity version during instructor preparation. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
