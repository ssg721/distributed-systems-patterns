# Distributed Systems Patterns

A practical guide to the patterns that make distributed systems work: what problem each one solves, how it works underneath, how real systems use it, and what it costs.

## Approach

Every pattern is explained from the problem up. We start with a concrete system that breaks, try the obvious fixes, see why they fail, and arrive at the pattern. The goal is to be able to re-derive a pattern and weigh its trade-offs in a design you haven't seen before, not to recall a definition.

## Structure

```
NN-concept/
├── README.md              # Overview: the problem, where it sits in real systems, learning path
├── real-world-systems.md  # How production systems use the pattern, and the lesson from each
├── lessons/               # One lesson per file
├── exercises.md           # Realistic design scenarios that apply the pattern end to end
└── code/                  # Small Go programs to watch it run and break
```

Every lesson follows the same shape:

1. **The problem**: a concrete situation where things go wrong
2. **How it works**: the mechanism, built up one step at a time
3. **Walkthrough**: a trace through a real scenario, failures included
4. **In production**: how real systems (etcd, Kafka, Spanner, ...) use and tune it
5. **Trade-offs**: what it costs, and what the alternatives give up
6. **Think it through**: questions that need reasoning, not recall

## Roadmap

### 1. [Coordination & Consistency](01-coordination-and-consistency/README.md) (current)

| # | Concept | Status |
|---|---------|--------|
| 01 | [Consensus (Raft / Paxos)](01-coordination-and-consistency/01-consensus/README.md) | In progress |
| 02 | Leases + fencing tokens | Not started |
| 03 | Quorum reads/writes (R + W > N) | Not started |
| 04 | Logical clocks (Lamport, vector clocks, HLC) | Not started |
| 05 | 2PC vs Saga | Not started |
| 06 | Transactional outbox + CDC | Not started |
| 07 | Idempotency keys | Not started |

### Later

2. Data distribution & storage (consistent hashing, sharding, replication models, LSM vs B-tree, event sourcing/CQRS, Snowflake IDs)
3. Probabilistic & specialized data structures (Bloom filter, HyperLogLog, Count-Min Sketch, Merkle trees, geohash/quadtree, tries & inverted indexes)
4. Reliability & traffic control (rate limiting, circuit breakers & retries, backpressure, gossip, caching strategies)
5. Real-time & messaging (fan-out, WebSockets/SSE, queues & DLQs, delayed jobs, chunked uploads)
6. Collaboration (CRDT vs OT)
