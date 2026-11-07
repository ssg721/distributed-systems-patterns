# 1. Coordination & Consistency

These patterns answer one question: **how do several machines act as if they were one, correct machine, when any of them can crash and the network can lose or delay messages?**

## How the topics build on each other

| # | Concept | The question it answers | Builds on |
|---|---------|-------------------------|-----------|
| 01 | Consensus (Raft / Paxos) | How do replicas agree on one ordered history? | (foundation) |
| 02 | Leases + fencing tokens | How do I give exactly one node "ownership", and stop an old owner that doesn't know it lost it? | 01 |
| 03 | Quorums (R + W > N) | How do I get consistency-ish guarantees without a leader? | 01 (majority math) |
| 04 | Logical clocks | How do I order events or detect conflicts without trusting wall clocks? | — |
| 05 | 2PC vs Saga | How do I make a change across several services or databases atomic? | 01, 04 |
| 06 | Transactional outbox + CDC | How do I update my DB *and* publish an event without losing one of them? | 05 |
| 07 | Idempotency keys | How do I make retries safe? | 06 |

Consensus comes first because almost everything else either relies on it (leases, lock services, metadata stores) or is defined by how it differs from it (leaderless quorums, sagas).
