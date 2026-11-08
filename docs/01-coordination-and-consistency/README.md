# Part 1: Coordination and consistency

These patterns answer one question: **how do several machines act as if they were one correct machine, when any of them can crash and the network can lose or delay messages?**

## How the topics build on each other

| # | Pattern | The question it answers | Builds on |
|---|---------|-------------------------|-----------|
| 01 | [Consensus (Paxos, PBFT, Raft)](01-consensus/01-evolution.md) | How do machines agree on one ordered history? | (the foundation) |
| 02 | Leases and fencing tokens | How do I give exactly one node ownership, and stop an old owner that doesn't know it lost it? | 01 |
| 03 | Quorum reads and writes | How do I get consistency without a leader? | 01 (majority math) |
| 04 | Logical clocks | How do I order events or detect conflicts without trusting wall clocks? | — |
| 05 | Two-phase commit vs sagas | How do I make a change across several services or databases atomic? | 01, 04 |
| 06 | Transactional outbox and CDC | How do I update my database *and* publish an event without losing one of them? | 05 |
| 07 | Idempotency keys | How do I make retries safe? | 06 |

Consensus comes first because almost everything else either relies on it (leases, lock services, metadata stores) or is defined by how it differs from it (leaderless quorums, sagas).

## Consensus, in three pages

1. [How consensus evolved](01-consensus/01-evolution.md): Paxos, PBFT and Raft, and the problem each one solved.
2. [Why consensus? A story](01-consensus/02-why-consensus.md): one bank transfer, three broken designs, and the rules that fix them.
3. [Consensus in real systems](01-consensus/03-real-world-systems.md): how etcd, Kafka, CockroachDB, Spanner and others use it.
