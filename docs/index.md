# Distributed Systems Patterns

This guide explains the patterns that make distributed systems work. For each pattern, it shows the problem, the mechanism, the use in real systems, and the cost.

## Approach

Each pattern starts with a problem. First, you see a real system that fails. Then you try the obvious fixes, and you see why they also fail. Last, you get to the pattern that solves the problem.

The goal is not to remember a definition. The goal is to derive the pattern again, and to compare its trade-offs in a new design.

## Structure

```
NN-concept/
├── README.md              # Overview: the problem, the use in real systems, the study plan
├── real-world-systems.md  # How production systems use the pattern, and the lesson from each system
├── lessons/               # One lesson in each file
├── exercises.md           # Design problems from real systems, from start to end
└── code/                  # Small Go programs: run them, then make them fail
```

Each lesson has the same 6 parts:

1. **The problem**: a real situation in which a system fails.
2. **How it works**: the mechanism, step by step.
3. **Walkthrough**: a real scenario from start to end, with failures.
4. **In production**: how real systems, for example etcd, Kafka and Spanner, use and tune the pattern.
5. **Trade-offs**: the cost of the pattern, and what the alternatives give up.
6. **Think it through**: questions that you must reason through, not answer from memory.

## Roadmap

### 1. [Coordination and Consistency](01-coordination-and-consistency/README.md)

This part is in progress.

| # | Concept | Status |
|---|---------|--------|
| 01 | [Consensus: Raft and Paxos](01-coordination-and-consistency/01-consensus/README.md) | In progress |
| 02 | Leases and fencing tokens | Not started |
| 03 | Quorum reads and writes: R + W > N | Not started |
| 04 | Logical clocks: Lamport clocks, vector clocks and hybrid logical clocks (HLC) | Not started |
| 05 | Two-phase commit (2PC) and Saga | Not started |
| 06 | Transactional outbox and change data capture (CDC) | Not started |
| 07 | Idempotency keys | Not started |

### Later parts

2. **Data distribution and storage:** consistent hashing, sharding, replication models, log-structured merge (LSM) trees and B-trees, event sourcing and command query responsibility segregation (CQRS), Snowflake IDs.
3. **Probabilistic and special data structures:** Bloom filters, HyperLogLog, Count-Min Sketch, Merkle trees, geohashes and quadtrees, tries and inverted indexes.
4. **Reliability and traffic control:** rate limiting, circuit breakers and retries, backpressure, gossip, caching strategies.
5. **Real-time systems and messaging:** fan-out, WebSockets and server-sent events (SSE), queues and dead-letter queues (DLQ), delayed jobs, chunked uploads.
6. **Collaboration:** conflict-free replicated data types (CRDT) and operational transformation (OT).
