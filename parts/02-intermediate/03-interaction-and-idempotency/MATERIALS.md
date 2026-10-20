# I03 Teaching Materials — Interaction Patterns, Acknowledgements, and Idempotency

[Lesson plan and lab](README.md) · [Part overview](../README.md)

Use this chapter before and during the lesson lab. The explanations and examples are required learning; extension work is explicitly labeled. Code-like examples are pseudocode unless a language is named.

## What you will learn

- Separate transport progress, command acceptance, and durable completion.
- Design stable operation IDs with payload conflict detection.
- Implement a bounded in-memory deduplication state machine.
- Describe what a retry can and cannot guarantee after process restart.

## Topics in detail

### 1. A timeout is an ambiguous outcome

A command can be lost before execution, execute and lose its response, or still be waiting. From the caller's timeout alone those cases look similar. Retrying is reasonable only after defining how repeated attempts relate to one logical operation. Treating every network attempt as a new operation can repeat effects.

### 2. Operation identity versus attempt identity

Give one logical action an operation ID reused on retry. Attempt numbers can change for diagnostics. Scope the ID by authenticated actor and operation kind, so unrelated clients cannot collide deliberately. A movement sequence and a result operation ID have different lifetimes and should not be substituted blindly for each other.

### 3. Store state, not just a seen flag

A deduplication entry may be InProgress, Succeeded with a result, or Rejected with a reason. Atomically establish the entry before concurrent handlers execute the same effect. A duplicate while InProgress receives a defined pending response or waits within a bound; it must not run the action again. Cache completion so a lost response can be recovered.

### 4. Payload identity matters

The same key reused for a different requested action is a conflict. Compare canonical relevant fields or a digest of their normalized representation. Raw JSON strings can differ in harmless whitespace or field order, so define what equality means. Returning an earlier unrelated success for a changed payload would hide a client bug or abuse attempt.

### 5. Retention and restart bound the promise

An in-memory store has finite capacity and loses entries on restart. Define a retry window and behavior when an old key expires. This is acceptable for a bounded classroom session, but cannot promise durable one-time rewards. Later, uniqueness and effects must commit together in persistent storage.

## Worked walkthrough

For Ready operation ready-42, atomically create InProgress for the admitted session, validate Waiting, mark readiness, and store Accepted. Suppress the first response. On retry, return the stored Accepted and do not emit MatchStarted again. Submit ready-42 with a different target: return Conflict. Restarting the in-memory handler removes this history, which is a documented limit rather than an invisible guarantee.

## Demonstration and guided practice

1. Drop responses before execution and after execution in separate runs; compare what the caller can observe.
2. Send two same-key requests concurrently and verify one execution path.
3. Fill the bounded cache and exercise the documented expiry/retry policy instead of letting memory grow indefinitely.

## Common misunderstandings

- “TCP acknowledgements prove the command committed.” They concern transport delivery, not application state.
- “A new ID on retry is safer.” It prevents the receiver from recognizing the same logical operation.

## Check your understanding

**1. What happens if duplicates arrive simultaneously?**

Expected reasoning: The deduplication claim must be atomic or owned by one serialized handler; a non-atomic check-then-set still races.

**2. Can a cached accepted response alone guarantee durable rewards?**

Expected reasoning: No. A crash can separate the effect from the cache unless their persistence is atomic.

## Independent exercise

Implement the four-case matrix: never executed, executed/response lost, simultaneous duplicate, and reused key with changed payload. Document retention, restart behavior, and how the client resolves InProgress.

## Connections and reading

I11 makes deduplication durable; A02 generalizes ambiguous outcomes across independent services. The [lesson plan](README.md) defines implementation scope and pass criteria; the [source guide](../../../SOURCES.md) identifies the primary readings.
