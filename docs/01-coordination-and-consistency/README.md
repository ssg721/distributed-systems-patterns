# 1. Coordination and Consistency

These patterns answer one question. **How can many machines operate as one correct machine, when each machine can crash and the network can lose or delay messages?**

## How the topics connect

| # | Concept | The question it answers | Builds on |
|---|---------|-------------------------|-----------|
| 01 | Consensus: Raft and Paxos | How do replicas agree on one ordered history? | None. This is the base. |
| 02 | Leases and fencing tokens | How do you give ownership to exactly 1 node? How do you stop an old owner that does not know that it lost ownership? | 01 |
| 03 | Quorums: R + W > N | How do you get some consistency without a leader? | 01, for the majority math |
| 04 | Logical clocks | How do you put events in order, or find conflicts, without wall clocks? | None |
| 05 | Two-phase commit (2PC) and Saga | How do you make one change across many services or databases atomic? | 01, 04 |
| 06 | Transactional outbox and change data capture (CDC) | How do you update a database and publish an event, and not lose one of the 2? | 05 |
| 07 | Idempotency keys | How do you make retries safe? | 06 |

Consensus is first because most of the other patterns use it, or are defined by how they differ from it. Leases, lock services and metadata stores use consensus. Leaderless quorums and sagas are defined by the difference.
