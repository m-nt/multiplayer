# B10 Teaching Materials — Performance and Reliability Fundamentals

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Estimate and measure payload traffic for a known update rate and recipient count.
- Distinguish transport delivery, application processing, and durable completion.
- Choose freshness and duplicate policies by message purpose.
- Explain how queue growth creates latency even when data eventually arrives.

## Topics in detail

### 1. A traffic budget starts with units

Calculate bytes per message × messages per second × recipients. Keep server ingress and egress separate. Serialized payload size does not include every transport header, retransmission, encryption record, or link overhead. Compare like with like: a payload counter should be checked against a payload estimate, not presented as complete wire usage.

### 2. Fresh state versus important operations

A newer full paddle snapshot can supersede an older unsent full snapshot. An old final result for match A cannot be discarded merely because a new result for match B exists. Classify whether the information is replaceable, needs an outcome, or reconstructs state. The appropriate delivery policy follows that purpose.

### 3. Order and duplicates are application concerns too

A snapshot labeled tick 40 should not overwrite tick 42. A repeated PointAwarded event should not trigger a second authoritative award. Transport ordering can simplify some cases but does not cover reconnection, retries, or separately routed flows. The application still needs identifiers and rules for old or repeated information.

### 4. Timeouts describe a waiting policy

A timeout bounds how long a caller waits, not necessarily how long the remote operation ran. For a result submission, the remote side may have processed the result before its response was lost. Beginner students should recognize that ambiguity; intermediate students implement recovery using stable operation identity.

### 5. Backlog turns throughput into stale information

If a producer generates 20 snapshots per second and the path can send only 10, a queue can grow even though traffic is flowing. After several seconds the receiver may be watching increasingly old state. Measure queue length or age, and prefer a bounded policy rather than assuming reliable delivery makes every queued snapshot useful.

## Worked walkthrough

At 80 payload bytes, 20 updates/second, and two recipients, egress is 3,200 payload bytes/second. Over 60 seconds the idealized payload total is 192,000 bytes. If your counter is much larger, inspect additional message types and actual encoded lengths before changing transport. If the outgoing queue ages despite the expected byte count, inspect the receiver and send path.

## Demonstration and guided practice

1. Capture a fixed-duration session and compare counters to the estimate.
2. Reduce update frequency while keeping the same render/buffer settings; record both traffic and visual effects.
3. Introduce a slow reader and observe whether queue age increases, even if messages continue arriving.

## Common misunderstandings

- “Reliable means exactly one business effect.” Delivery and effect deduplication are different contracts.
- “Lower bytes is always better.” A smaller/fewer update policy may damage useful precision or freshness.

## Check your understanding

**1. Which snapshot can be replaced safely in the beginner full-state scheme?**

Expected reasoning: An older unsent full snapshot for the same current match context, because the newer one supersedes its state.

**2. Why must the report distinguish payload and wire bytes?**

Expected reasoning: They measure different boundaries and include different overhead.

## Independent exercise

Budget input, snapshots, and final result traffic separately. Measure a 60-second run, explain differences, and choose one reduction whose visual/gameplay impact you can demonstrate rather than assume.

## Connections and reading

TCP provides delivery semantics; I10 expands bounded queues, compression baselines, and interest management. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
