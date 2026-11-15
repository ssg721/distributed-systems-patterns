# How consensus evolved: Paxos, PBFT, Raft

> **how do several machines agree on one thing when some of them may fail?** Each protocol on this page is an answer to a version of that question.

You don't need to know any of these algorithms to read this page. The goal is the shape of the story: which problem each protocol solved, what it left unsolved, and where you will find it running today. When you want to see *why* any of this is needed, built up from a single bank transfer, read [Why consensus?](02-why-consensus.md).

## The story at a glance

| Year | What happened | The question it answered |
|------|---------------|--------------------------|
| 1989–98 | **Paxos** is written, ignored, then published | How do we agree safely when machines crash? |
| 1999 | **PBFT** | How do we agree when machines can lie, fast enough to use? |
| 2006–11 | Chubby, *Paxos Made Live*, ZooKeeper | What does it take to run this in production? |
| 2014 | **Raft** | Can consensus be easy to understand and build? |
| 2014–19 | Tendermint, HotStuff | Can Byzantine agreement scale to blockchains? |


## Paxos: the safe core (1989–2001)

### The question

Machines crash and restart, and messages get lost, delayed or duplicated, but nobody lies. Can a group still agree on a value, and never contradict itself, no matter how badly timed the failures are?

1. **The Echo File System**: In the late 1980s, researchers at Digital Equipment Corporation’s (DEC) Systems Research Center were building a fault-tolerant file system called Echo. They claimed they had designed a protocol that could maintain consistency through arbitrary non-Byzantine failures (Crash Faults or Fail-Stop/Fail-Recovery failures) as long as a majority of processors were working. Leslie Lamport set out to prove them wrong by showing that reaching consensus reliably in an asynchronous network was fundamentally impossible—only to accidentally discover a protocol that made it possible instead.   
Microsoft
2. **The Fictional Greek Island**: Amused (and perhaps slightly frustrated) by how difficult it was for other engineers to understand distributed consensus, Lamport wrote the algorithm up as an elaborate archaeological spoof. He framed it as a historical paper detailing the parliamentary system of an imaginary Greek island called Paxos, where legislators wandered in and out of the chamber ("peripatetic propensity") and communicated only via unreliable messengers.   
Wikipedia
3. **The Publication Delay**: He submitted the paper in 1990. Reviewers found the Greek legislative allegory baffling and refused to accept it unless he removed the fiction. Lamport refused to change the story, so the paper sat in limbo for years, eventually getting published as "The Part-Time Parliament" in 1998 (though it had circulated internally as a DEC report since 1989).   
Microsoft
4. **Paxos Made Simple**: Because people still struggled to cut through the Greek island metaphor to understand the raw computer science, Lamport finally yielded years later and published "Paxos Made Simple" in 2001, stripping away all the legislators, ledgers, and island lore to explain the algorithm plainly.


### The idea in plain words

1. **The Core Idea: Agreement Without a Boss**
   Imagine a group of friends trying to decide on a restaurant for dinner. Anyone can suggest a place, but they need a foolproof way to make sure everyone agrees on one choice, even if people are texting at different times, losing cell service, or trying to change the plan. That is what Paxos solves.
2. The Two Phases
   Every time Paxos wants to agree on a single decision (or "slot" in a log), it uses a two-step process:
   **Step 1: The "Prepare" Phase (Checking the Room)**
   A proposer steps up and shouts: "I want to pitch an idea using Proposal #5. Everyone promise me you won't accept any older proposals, and tell me if you already agreed to anything else!"
   If a majority of people agree to this, the proposer has successfully taken control.
   **Step 2: The "Accept" Phase (Locking It In)**
   The Golden Rule: If anyone in that majority tells the proposer, "Hey, we already accepted Proposal #3 earlier," the proposer is not allowed to push its own new idea anymore. It must take Proposal #3 and push that instead.
   If nobody accepted anything yet, the proposer can push its own idea. Once a majority votes "Yes" to accept it, the decision is officially locked in (chosen).
   
3. **Why Does It Never Fail? (The Magic of Majorities)**
   The safety of Paxos relies entirely on math and overlapping groups:
   Overlapping Majorities: If you have 5 people, a majority is 3. Any two groups of 3 people will always share at least one overlapping person.
   Because of this overlap, any new proposer is guaranteed to bump into someone who already knows what the previous proposer did. That shared person acts as a bridge, ensuring a new leader can never accidentally overwrite a decision that was already made.
   
4. Moving From One Decision to a Whole Log (Multi-Paxos)
   Basic Paxos only solves one decision at a time. Doing Phase 1 (the Prepare step) for every single transaction would be painfully slow.

### Multi-Paxos fixes this by electing a stable leader:
   The leader runs Phase 1 just once to take charge.
   After that, for every new piece of data or transaction that comes in, it skips Phase 1 entirely and instantly jumps to Phase 2 (a quick single round-trip vote).

### What it got right

Safety never depends on timing. Several proposers can collide, messages can arrive in any order, and nodes can crash at the worst possible moment, and Paxos will still never choose two different values. Collisions only slow it down: two proposers can keep outbidding each other with higher ballots. That's a liveness problem, and it's solved in practice by having a single leader.

### What it left open

The paper described how to agree on *one value*. Everything a production system needs, such as the log, how to pick and replace the leader, adding or removing machines, snapshots, and how clients retry, was left to implementers. Each team filled those gaps differently, which is the problem Raft later set out to fix.

### Where you find it

Researchers at DEC used Paxos in the Petal storage system and Frangipani file system. Google built its Chubby lock service, Megastore and Spanner on it. Amazon DynamoDB runs Multi-Paxos inside every partition, and Apache Cassandra uses Paxos for its lightweight transactions.

!!! info "The protocol that got there first: Viewstamped Replication"
    In 1988, a year before Paxos was first written up, Brian Oki and Barbara Liskov at MIT published **Viewstamped Replication (VR)**. It was described as a primary-backup system: a primary runs requests and backups follow it, and time is divided into numbered *views*, each with one primary. When a primary fails, a view change picks a new one. At its core is essentially the same agreement idea as Paxos, discovered independently. Paxos became the famous name, but VR is the closest ancestor of Raft, and Liskov's later work on Byzantine faults, PBFT, grew directly out of it.

---

## PBFT: when machines can lie (1999)

### The question

Paxos assumes a faulty machine stops or stalls. But machines can also misbehave: a bug sends wrong data, corrupted memory flips a value, an attacker takes over a node. A machine that sends *different* answers to different peers is called **Byzantine**. Solutions to the Byzantine Generals problem existed, but they either assumed messages arrive within known time limits, which an attacker can easily break, or were far too slow to use.

### The story

In 1999, Miguel Castro and Barbara Liskov published **Practical Byzantine Fault Tolerance (PBFT)**. "Practical" was the point. It was the first Byzantine protocol that stays safe in an asynchronous network like the internet and is fast enough for real work. They built a Byzantine-tolerant version of the NFS file system with it and measured it at only about 3% slower than standard, unreplicated NFS.

### Why it needs 3f + 1 machines

Crash-tolerant protocols need `2f + 1` machines to survive `f` failures. PBFT needs `3f + 1`. Here's the intuition:

- With `n` machines and up to `f` faulty, you can only safely wait for `n − f` replies. The missing `f` might just be slow honest machines.
- Among the replies you *did* get, up to `f` may come from liars.
- For the honest replies to outnumber the liars, you need `n − f − f > f`, which gives **`n > 3f`**.

So tolerating one liar takes 4 machines, and tolerating two takes 7.

### The idea in plain words

PBFT follows the same pattern as VR. A **primary**, the leader of the current numbered view, assigns each request a sequence number. The replicas then go through three rounds of messages, *pre-prepare*, *prepare* and *commit*, and wait for `2f + 1` matching messages at each step. Any two groups of `2f + 1` out of `3f + 1` overlap in at least `f + 1` machines, so the overlap always includes at least one honest machine, the same majority-overlap trick as Paxos, scaled up for liars. If the primary is slow or caught lying, replicas run a **view change** to replace it. Messages are authenticated, so a liar can't pretend to be someone else.

### What it left open

Every replica talks to every other replica, so messages grow with the square of the cluster size, and replacing a faulty primary costs even more. That's fine for 4 or 7 machines, but painful for hundreds. And most companies don't need it at all: machines inside your own datacenter are trusted, so the crash model is cheaper, needing fewer machines and fewer messages.

### Where you find it

PBFT's biggest impact came where trust really *is* split between organizations: blockchains. **Tendermint** (2014, now maintained as CometBFT) adapted PBFT-style voting to proof-of-stake chains such as Cosmos. **HotStuff** (2019) cut the communication so that it grows linearly with the cluster size even when the leader changes, and it became the basis of Facebook's Libra project (LibraBFT, later DiemBFT) and its successors such as Aptos. Bitcoin is a different branch altogether. It works with open, anonymous membership and reaches agreement probabilistically through proof of work, rather than through voting rounds.

---

## Paxos meets production (2006–2011)

### The question

Paxos was proven correct. Could teams actually ship it?

### The story

In 2006, Google described **Chubby**, a lock service where a "cell" of five replicas uses Paxos to agree on its state. Google's file system (GFS) and Bigtable used Chubby to elect their masters. The next year, the Chubby team published ***Paxos Made Live***, a candid account of what it took. A page of pseudocode had turned into several thousand lines of C++. They had to handle disk corruption, master leases, changing group membership, snapshots, and a testing regime built to inject failures. Every gap the paper left open had to be filled with their own design, which they could no longer formally prove correct.

At Yahoo, the team building **ZooKeeper**, an open-source coordination service later used across the Hadoop ecosystem and by Kafka, chose not to use Paxos directly. They designed **Zab**, a primary-backup broadcast protocol organized around numbered *epochs*, that fit how ZooKeeper sends a stream of state changes from one primary.

### What it left open

By the early 2010s, consensus was clearly essential infrastructure, but every serious implementation was a custom extension of a hard-to-read paper. It was difficult to teach, difficult to verify and easy to get subtly wrong.

---

## Raft: designing for understanding (2014)

### The question

Could a consensus algorithm be as safe and efficient as Multi-Paxos, but **easy to understand, and complete enough to build directly from the paper?**


At Stanford, Diego Ongaro and John Ousterhout made understandability the primary design goal, an unusual choice for an algorithms paper. Their paper, *In Search of an Understandable Consensus Algorithm*, won Best Paper at USENIX ATC 2014. They tested the claim directly. In a study with 43 students at two universities who learned both algorithms, 33 answered questions about Raft better than questions about Paxos.

### The idea in plain words

Raft provides the same guarantees as Multi-Paxos. The difference is in its structure:

- **Decomposition.** The problem is split into three parts you can learn separately: **leader election**, **log replication**, and **safety**.
- **A strong leader.** Log entries flow only from the leader to followers, never the other way.
- **Fewer possible states.** Logs never have holes. Time is divided into numbered **terms**, with at most one leader per term. Elections use randomized timeouts so split votes resolve quickly.
- **The whole system, not just the core.** The paper covers membership changes, log compaction and client interaction, the exact gaps *Paxos Made Live* had to fill in on its own.

If you've read [Why consensus?](02-why-consensus.md), you've already met every core piece: majority commit, terms, the rule that you don't vote for a candidate with a less up-to-date log, and rejecting messages from an older term.

### Where you find it

Raft became the default choice for new systems. etcd adopted it even before the paper was published, and through etcd it runs every Kubernetes cluster. It's also in Consul and Vault, CockroachDB, TiKV and TiDB, ClickHouse Keeper, and Kafka, whose KRaft mode replaced ZooKeeper entirely in Kafka 4.0 (March 2025). MongoDB's replica-set elections use a protocol based on Raft.

### What it left open

Raft didn't make consensus *safer*; it made it *buildable and teachable*. It also keeps the classic costs: all writes go through one leader, and every commit waits for a round trip to a majority. Large systems get around this by running thousands of small Raft groups side by side, as described in [Consensus in real systems](03-real-world-systems.md).

---

## Where it went next

The family keeps growing along two branches.

**Crash-tolerant (trusted machines).** *EPaxos* (2013) removes the single leader, so any replica can commit commands that don't conflict. *Flexible Paxos* (2016) showed that the quorum used to elect a leader and the quorum used to replicate entries only need to overlap with *each other*, not every majority with every other, which lets you tune for faster writes or faster recovery. In production, the dominant trend is many small Raft or Paxos groups, one per shard.

**Byzantine-tolerant (untrusted machines).** This branch is mostly driven by blockchains: Tendermint and HotStuff, mentioned above, and newer protocols that pipeline rounds and rotate leaders frequently.

```mermaid
flowchart LR
    SMR["State machine replication<br/>(1978)"] --> VR["Viewstamped Replication<br/>(1988)"]
    SMR --> PX["Paxos<br/>(1989, published 1998)"]
    BG["Byzantine Generals<br/>(1982)"] --> PBFT
    VR --> PBFT["PBFT<br/>(1999)"]
    PX --> MP["Multi-Paxos<br/>Chubby, Spanner, DynamoDB"]
    PX --> ZAB["Zab<br/>ZooKeeper (2011)"]
    VR --> RAFT["Raft<br/>(2014)"]
    MP --> RAFT
    PX --> EP["EPaxos, Flexible Paxos"]
    PBFT --> TM["Tendermint<br/>(2014)"]
    PBFT --> HS["HotStuff<br/>(2019)"]
```

*The lines show the most direct influences, not every connection. Most of these protocols borrowed from several ancestors.*

---

## Side by side

| | Fault model | Machines to survive `f` faults | Leader? | Known for | Where you'll meet it |
|---|---|---|---|---|---|
| **Paxos / Multi-Paxos** | Crash | `2f + 1` | Optional in Paxos; stable leader in Multi-Paxos | The first widely known safe core; hard to read | Chubby, Spanner, DynamoDB, Cassandra LWT |
| **Viewstamped Replication** | Crash | `2f + 1` | Yes (primary per view) | Got there first; full system design | Mostly as Raft's ancestor |
| **Zab** | Crash | `2f + 1` | Yes (per epoch) | Ordered stream of state changes from a primary | ZooKeeper |
| **Raft** | Crash | `2f + 1` | Yes (per term) | Designed to be understood and built | etcd, Consul, CockroachDB, TiKV, Kafka |
| **PBFT** | Byzantine | `3f + 1` | Yes (primary per view) | First practical Byzantine protocol | Research systems; ancestor of blockchain BFT |
| **HotStuff** | Byzantine | `3f + 1` | Yes, rotating | Linear communication, even across leader changes | Diem/Aptos family of blockchains |

---

## Three things to remember

1. **Every protocol makes the same deal.** FLP says agreement can't be guaranteed to finish, so every protocol is always safe and only makes progress when the network cooperates.
2. **The big fork is crash versus lies.** If your machines are trusted, use a crash-tolerant protocol: `2f + 1` machines and majorities. If they aren't, you need Byzantine tolerance: `3f + 1` machines and two-thirds quorums, at a much higher cost.
3. **Raft's contribution was understanding, not safety.** It made the ideas Paxos and VR proved buildable and teachable. That's why it's the one you'll meet most often.

---

## Read the originals

1. [In Search of an Understandable Consensus Algorithm](https://raft.github.io/raft.pdf), Ongaro and Ousterhout, 2014.
2. [Paxos Made Simple](https://lamport.azurewebsites.net/pubs/paxos-simple.pdf), Lamport, 2001. Short, and much easier.
3. *Paxos Made Live: An Engineering Perspective*, Chandra, Griesemer and Redstone, 2007. What production adds.
4. [Practical Byzantine Fault Tolerance](https://pmg.csail.mit.edu/papers/osdi99.pdf), Castro and Liskov, 1999.
5. *Viewstamped Replication Revisited*, Liskov and Cowling, 2012. VR, rewritten clearly.
6. *The Part-Time Parliament*, Lamport, 1998. The original, for the history.
