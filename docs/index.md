# Distributed Systems Patterns

**Learn the patterns behind distributed systems by watching simple designs break, one failure at a time.**

Every system that runs on more than one machine eventually hits the same problems: machines crash, networks drop or delay messages, and a node that is slow looks exactly like a node that is dead. A small set of patterns, such as consensus, fencing tokens, quorums and idempotency, exists to handle those problems. This site explains them so you can **understand them, remember them, and recognise them in the systems you use every day.**

You don't need to be an expert. If you've built a backend service and used a database, you have everything you need.

---

## Start here: Consensus

The foundation of almost everything else. Read the three pages in order, or jump to whichever question you have.

<div class="grid cards" markdown>

-   **1. How consensus evolved**

    ---

    From Paxos to PBFT to Raft: which problem each protocol solved, what it left open, and where it runs today.

    [:octicons-arrow-right-24: Read the history](01-coordination-and-consistency/01-consensus/01-evolution.md)

-   **2. Why consensus? A story**

    ---

    Start with one bank transfer and three simple designs. Break each one, and rebuild Raft from the pieces.

    [:octicons-arrow-right-24: Read the story](01-coordination-and-consistency/01-consensus/02-why-consensus.md)

-   **3. Consensus in real systems**

    ---

    How etcd, Kafka, CockroachDB, Spanner, DynamoDB and others use consensus, and how little of their data they put through it.

    [:octicons-arrow-right-24: Tour the systems](01-coordination-and-consistency/01-consensus/03-real-world-systems.md)

</div>

---

## How every topic is told

Patterns are easy to recite and hard to apply. So each topic is built from the problem up, not from the definition down:

1. **A concrete situation.** A real system, such as a bank transfer or a job scheduler, with a real failure.
2. **The obvious fixes, and why they break.** Each attempt fails in one specific, memorable way.
3. **The pattern, one rule at a time.** Every rule fixes one of the breaks you just saw.
4. **The pattern in production.** Where etcd, Kafka, Spanner and others use it, and the lesson to take into your own designs.

The aim is that you could re-derive the pattern on a whiteboard, and spot when a design needs it, long after you've forgotten the exact wording.

---

## The roadmap

### Part 1: Coordination and consistency

How do several machines act like one correct machine, when any of them can crash and the network can lose or delay messages?

| # | Pattern | The question it answers | Status |
|---|---------|-------------------------|--------|
| 01 | [Consensus (Paxos, PBFT, Raft)](01-coordination-and-consistency/01-consensus/01-evolution.md) | How do machines agree on one ordered history? | Available |
| 02 | Leases and fencing tokens | How do I stop an old owner that doesn't know it was replaced? | Coming next |
| 03 | Quorum reads and writes | How do I get consistency without a leader? | Planned |
| 04 | Logical clocks | How do I order events without trusting wall clocks? | Planned |
| 05 | Two-phase commit vs sagas | How do I change several services or databases atomically? | Planned |
| 06 | Transactional outbox and CDC | How do I update my database *and* publish an event without losing one? | Planned |
| 07 | Idempotency keys | How do I make retries safe? | Planned |

### Later parts

- **Data distribution and storage:** consistent hashing, sharding, replication models, LSM trees vs B-trees, event sourcing, Snowflake IDs.
- **Probabilistic data structures:** Bloom filters, HyperLogLog, Count-Min Sketch, Merkle trees, geohashes and quadtrees.
- **Reliability and traffic control:** rate limiting, circuit breakers and retries, backpressure, gossip, caching.
- **Real-time and messaging:** fan-out, WebSockets and SSE, queues and dead-letter queues, delayed jobs.
- **Collaboration:** CRDTs vs operational transformation.
