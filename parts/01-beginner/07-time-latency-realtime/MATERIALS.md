# B07 Teaching Materials — Time, Latency, and Real-Time Constraints

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Measure RTT using a single local clock and describe the symmetry assumption behind RTT/2.
- Separate simulation, snapshot, and rendering frequencies.
- Explain interpolation delay, extrapolation error, and prediction as different mechanisms.
- Observe buffer underrun under controlled jitter without claiming that smoothing fixes authority.

## Topics in detail

### 1. Time spent traveling versus time spent waiting

A response arrives after travel, server scheduling/work, and the return journey. An echo measurement using send and receive timestamps from the same local monotonic clock measures that round trip. The result is useful, but dividing by two is only a rough one-way estimate when paths and processing are sufficiently balanced. Subtracting unrelated machine clocks is not a safe replacement.

### 2. Jitter, loss, and bandwidth

Jitter means delay varies, so regularly sent updates may arrive in bursts. Loss may cause absent datagrams or, on an ordered reliable stream, delayed application delivery while recovery occurs. Bandwidth describes how much data a path can carry over time. A wide pipe can still have a long propagation delay; a short path can still become congested.

### 3. Three independent rates

At 60 simulation ticks per second, the server advances rules about every 16.67 ms. At 20 snapshot sends per second, it publishes about every 50 ms. A client rendering 120 frames per second draws about every 8.33 ms. Most render frames therefore have no newly arrived snapshot. The display must choose how to represent the gap.

### 4. Interpolation uses known endpoints

Keep two authoritative samples and draw a point between them at a selected render time. If position is 2 at 100 ms and 3 at 150 ms, a render time of 125 ms gives 2.5 using linear interpolation. Waiting to have a later sample adds display delay. Buffering helps regularity but cannot create a missing future sample during an arbitrarily long outage.

### 5. Extrapolation and prediction make guesses

Extrapolation extends observed motion beyond the latest sample, for example using the last velocity. Prediction applies local input before authoritative confirmation. Both can be wrong. Reconciliation is the process that repairs predicted state using authoritative information. At this level, observe those differences; implementing a smoothing function is not yet implementing reconciliation.

## Worked walkthrough

Suppose snapshots are sent at 100, 150, and 200 ms but arrive with varying delay. A display intentionally rendering 100 ms behind its estimated server timeline can interpolate between already-received samples. If delivery pauses for 250 ms, that buffer eventually runs out. Compare holding the last position with a bounded extrapolation: holding freezes, while extrapolation may continue through a wall incorrectly.

## Demonstration and guided practice

1. Measure baseline echo RTT, then add the recorded delay profile and compare the result.
2. Display tick, snapshot-arrival, and render counters; change rendering rate and observe that authoritative tick rate is unchanged.
3. Enable interpolation, then introduce a gap longer than its buffer. Record visual smoothness, added delay, and underruns separately.

## Common misunderstandings

- “RTT/2 is the true one-way latency.” It depends on symmetry and processing assumptions.
- “Smooth movement proves correct simulation.” A renderer can smoothly display an incorrect guess.

## Check your understanding

**1. Why does interpolation usually show slightly older state?**

Expected reasoning: It needs samples surrounding the chosen render time, so it waits behind the newest estimated authoritative time.

**2. Will sending more snapshots always help?**

Expected reasoning: No. Additional traffic/work can increase congestion and queueing, worsening freshness.

## Independent exercise

Create a table for three settings: no buffer, a short buffer, and a longer buffer. Under the same impairment, record RTT, approximate visual delay, and observed underruns. Explain which experience each setting favors.

## Connections and reading

NETCODE interpolation explains the visual trade-off. I08 formalizes simulation scheduling; I09 implements prediction and reconciliation. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
