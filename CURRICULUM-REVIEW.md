# Curriculum Practicality Review

[Curriculum](README.md)

The original README repeated the same three courses several times. Its 46 distinct lesson topics form a useful progression, but uniform two-hour estimates hide substantial setup and implementation. All topics are retained or redistributed explicitly; every lesson page has its own review and measurable lab.

| Original issue | Revision and practical reason |
|---|---|
| Repeated curriculum and damaged closing text | One navigable index and separate lesson materials |
| Beginner build delayed until lesson 11 | Demo in lesson 1, incremental modifications throughout |
| Too many technology names in beginner 3 | Messages and layers first; transport implementation in intermediate 4 |
| Prediction/reconciliation in beginner 7 | Observe there, implement in intermediate 9 |
| Intermediate 2 repeats beginner score exploit | Add stale ownership and cross-player authorization |
| Five stacks in intermediate 4 | Three workshops; one required WebSocket application |
| Go introduced without preparation | Explicit language bridge and replacement client adapter |
| Server implementation before simulation instruction | Position-only Go server in lesson 7, full rules in lesson 8 |
| Persistence required but insufficiently taught | Transactional duplicate-safe results in intermediate 11 |
| Deployment before complete operational preparation | Secure local integration first; TLS and reachability in intermediate 14 |
| Allocator races before distributed-systems concepts | Move original advanced 10 to advanced 2 |
| Readiness shown after allocation without qualification | Distinguish ready-pool allocation from cold provisioning |
| Shooter lag compensation promised but absent | Optional bounded extension in intermediate 9 |
| Multi-region implies expensive infrastructure | Simulated regions required; paid infrastructure optional |
| Security/observability arrive late | Validate and log early, deepen later |
| Capstones treated as two-hour builds | Multiple integration/review blocks and independent project hours |

## Advanced crosswalk

| Original lesson | Revised lesson |
|---|---|
| 1 Production architecture | 1 Production architecture |
| 10 Distributed systems | 2 Distributed systems |
| 2 Matchmaking | 3 Matchmaking |
| 3 Allocation | 4 Allocation |
| 4 Docker | 5 Docker |
| 5 Orchestration | 6 Orchestration |
| 6 Agones | 7 Agones |
| 7 End-to-end lifecycle | 8 End-to-end lifecycle |
| 8 Multi-region | 9 Multi-region |
| 9 Capacity | 10 Capacity |
| 11–18 | 11–18 with bounded labs |

Beginner and intermediate retain their numbering. Per-lesson reviews explain decisions for all 46 units. Labs remain centered on Pong rather than expanding into an unfinished shooter or MMO.

Custom congestion control, NAT traversal implementation, deterministic lockstep, rollback fighting games, seamless world sharding, host migration, full anti-cheat, multi-region database consensus, and zero-loss live-match recovery are optional specializations. Do not grade them as if they were taught here.
