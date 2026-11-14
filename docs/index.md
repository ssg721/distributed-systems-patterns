---
hide:
  - navigation
  - toc
---

<div class="dsp-hero" markdown>

<div class="dsp-hero__text" markdown>

<p class="dsp-eyebrow">A practical guide · free and open source</p>

# Distributed Systems Patterns

<p class="dsp-lead">Learn the patterns behind distributed systems by watching simple designs break, one failure at a time.</p>

[Start with consensus :lucide-arrow-right:](01-coordination-and-consistency/01-consensus/01-evolution.md){ .md-button .md-button--primary }
[Why this guide](#why){ .md-button }

</div>

<div class="dsp-hero__art" aria-hidden="true">
<svg viewBox="0 0 320 280" role="img" xmlns="http://www.w3.org/2000/svg">
  <g class="dsp-links">
    <line x1="160" y1="56" x2="262" y2="124"/>
    <line x1="160" y1="56" x2="224" y2="226"/>
    <line x1="160" y1="56" x2="96" y2="226"/>
    <line class="dsp-link--cut" x1="160" y1="56" x2="58" y2="124"/>
  </g>
  <g class="dsp-beats">
    <line x1="160" y1="56" x2="262" y2="124"/>
    <line x1="160" y1="56" x2="224" y2="226"/>
    <line x1="160" y1="56" x2="96" y2="226"/>
  </g>
  <line class="dsp-partition" x1="84" y1="48" x2="126" y2="112"/>
  <g class="dsp-node dsp-node--leader"><circle cx="160" cy="56" r="26"/><text x="160" y="61">L</text></g>
  <g class="dsp-node dsp-node--ack"><circle cx="262" cy="124" r="22"/><text x="262" y="129">✓</text></g>
  <g class="dsp-node dsp-node--ack"><circle cx="224" cy="226" r="22"/><text x="224" y="231">✓</text></g>
  <g class="dsp-node"><circle cx="96" cy="226" r="22"/><text x="96" y="231">·</text></g>
  <g class="dsp-node dsp-node--down"><circle cx="58" cy="124" r="22"/><text x="58" y="129">✕</text></g>
  <text class="dsp-caption" x="160" y="272">3 of 5 agree, so the write commits</text>
</svg>
</div>

</div>

## Why this guide { #why }

<p class="dsp-section-lead">Most system design courses teach you one app at a time. This guide teaches you the few hard pieces that every app keeps needing.</p>

<div class="dsp-compare" markdown>

<div class="dsp-compare__card" markdown>

<p class="dsp-compare__label">The usual way</p>

Take one problem, like "Design WhatsApp" or "Design Uber", and solve it from start to end.

This is good practice. But there are hundreds of such problems, and nobody can remember the full solution for each one.

</div>

<div class="dsp-compare__card dsp-compare__card--ours" markdown>

<p class="dsp-compare__label">The approach here</p>

Take one **hard problem** that many apps share, and understand it properly: why it is hard, which pattern solves it, and which well-known software uses that pattern.

Learn it once, and use it in every app you build.

</div>

</div>

### Every app has only a few hard parts

Look closely at any real design and you will notice something. Most of the application is straightforward: APIs, screens, tables in a database, a few background jobs. Only a few parts are really hard, and those parts decide whether the system works or fails.

These hard parts are not special to your app. The same problems come up again and again, in apps that look nothing like each other:

| Where you see it | The hard part | The pattern | Who uses it |
|------------------|---------------|-------------|-------------|
| A UPI payment page | The user taps **Pay** twice, or the app retries after a timeout. The money must move only once. | Idempotency keys | Stripe's API |
| A job scheduler | Two servers both think they are the leader, and the billing job runs twice. | [Consensus](01-coordination-and-consistency/01-consensus/01-evolution.md), leases | etcd, ZooKeeper, Kafka |
| A food delivery order | Order, payment and restaurant are in different services. If one step fails, the others must be undone. | Sagas, transactional outbox | Temporal, Debezium |
| A public API | One client sends too many requests and slows down everyone else. | Rate limiting | Envoy, NGINX |
| Any app with crores of users | The data no longer fits in one database. | Sharding, consistent hashing | Cassandra, DynamoDB |
| A cab or delivery app | Find the drivers near me, quickly. | Geohashes, quadtrees | Redis, Elasticsearch |
| A shared document | Two people edit the same line at the same time. | CRDTs, operational transformation | Google Docs, Yjs, Automerge |

So this guide does one thing: it **collects these hard pieces in one place**. For each piece, you learn three things:

1. **The problem.** What goes wrong, and why the simple fixes do not work.
2. **The software.** Which well-known systems use this piece, and how. Once you know this, you can pick the right tool instead of building it yourself.
3. **The reuse.** How to apply the same piece in your own designs.

### How to use this guide

- **Read for the problem, not for the app.** The bank transfer or job scheduler in a lesson is only an example. Remember the problem; it will come back in a different app.
- **Note which software uses each piece.** Next time you see etcd, Kafka or Redis in an architecture, you will know exactly what job it is doing there.
- **When you face a new design, split it first.** Separate the straightforward parts from the hard parts. For each hard part, ask: "Which piece from this guide solves this?" Most of the time, the answer is already here.

You don't need to be an expert. If you have built a backend service and used a database, you have everything you need to start.

## The roadmap { #roadmap }

<p class="dsp-section-lead">Six parts. Each one starts from a single question that every distributed system has to answer.</p>

<div class="dsp-parts" markdown>

<div class="dsp-part dsp-part--1" markdown>

<div class="dsp-part__head" markdown="span">:lucide-handshake:{ .dsp-part__icon } <span class="dsp-part__num">Part 1</span> <span class="dsp-status dsp-status--live">In progress</span></div>

### [Coordination and consistency](01-coordination-and-consistency/README.md)

How do several machines act like one correct machine, when any of them can crash and the network can lose or delay messages?

<p class="dsp-chips" markdown="span">[Consensus](01-coordination-and-consistency/01-consensus/01-evolution.md){ .dsp-chip .dsp-chip--done } <span class="dsp-chip dsp-chip--next">Leases and fencing tokens</span> <span class="dsp-chip">Quorum reads and writes</span> <span class="dsp-chip">Logical clocks</span> <span class="dsp-chip">Two-phase commit vs sagas</span> <span class="dsp-chip">Transactional outbox and CDC</span> <span class="dsp-chip">Idempotency keys</span></p>

</div>

<div class="dsp-part dsp-part--2" markdown>

<div class="dsp-part__head" markdown="span">:lucide-database:{ .dsp-part__icon } <span class="dsp-part__num">Part 2</span> <span class="dsp-status">Planned</span></div>

### Data distribution and storage

How do you spread data across machines, keep extra copies, and still find any record quickly?

<p class="dsp-chips" markdown="span"><span class="dsp-chip">Consistent hashing</span> <span class="dsp-chip">Sharding</span> <span class="dsp-chip">Replication models</span> <span class="dsp-chip">LSM trees vs B-trees</span> <span class="dsp-chip">Event sourcing</span> <span class="dsp-chip">Snowflake IDs</span></p>

</div>

<div class="dsp-part dsp-part--3" markdown>

<div class="dsp-part__head" markdown="span">:lucide-dices:{ .dsp-part__icon } <span class="dsp-part__num">Part 3</span> <span class="dsp-status">Planned</span></div>

### Probabilistic and specialized data structures

How do you answer questions about huge data sets with very little memory, when "almost exact" is good enough?

<p class="dsp-chips" markdown="span"><span class="dsp-chip">Bloom filters</span> <span class="dsp-chip">HyperLogLog</span> <span class="dsp-chip">Count-Min Sketch</span> <span class="dsp-chip">Merkle trees</span> <span class="dsp-chip">Geohashes and quadtrees</span></p>

</div>

<div class="dsp-part dsp-part--4" markdown>

<div class="dsp-part__head" markdown="span">:lucide-gauge:{ .dsp-part__icon } <span class="dsp-part__num">Part 4</span> <span class="dsp-status">Planned</span></div>

### Reliability and traffic control

How do you stop one slow or overloaded component from taking the whole system down with it?

<p class="dsp-chips" markdown="span"><span class="dsp-chip">Rate limiting</span> <span class="dsp-chip">Circuit breakers and retries</span> <span class="dsp-chip">Backpressure</span> <span class="dsp-chip">Gossip</span> <span class="dsp-chip">Caching</span></p>

</div>

<div class="dsp-part dsp-part--5" markdown>

<div class="dsp-part__head" markdown="span">:lucide-radio:{ .dsp-part__icon } <span class="dsp-part__num">Part 5</span> <span class="dsp-status">Planned</span></div>

### Real-time and messaging

How do you get events to the right place, quickly, without losing any or delivering them twice?

<p class="dsp-chips" markdown="span"><span class="dsp-chip">Fan-out</span> <span class="dsp-chip">WebSockets and SSE</span> <span class="dsp-chip">Queues and dead-letter queues</span> <span class="dsp-chip">Delayed jobs</span></p>

</div>

<div class="dsp-part dsp-part--6" markdown>

<div class="dsp-part__head" markdown="span">:lucide-users-round:{ .dsp-part__icon } <span class="dsp-part__num">Part 6</span> <span class="dsp-status">Planned</span></div>

### Collaboration

How can many people edit the same document at once, even offline, and still end up with the same result?

<p class="dsp-chips" markdown="span"><span class="dsp-chip">CRDTs vs operational transformation</span></p>

</div>

</div>

<p class="dsp-legend"><span class="dsp-chip dsp-chip--done">Available</span> <span class="dsp-chip dsp-chip--next">Coming next</span> <span class="dsp-chip">Planned</span></p>

## Start here: Consensus

<p class="dsp-section-lead">The foundation of almost everything else. Read the three pages in order, or jump to whichever question you have.</p>

<div class="grid cards dsp-path" markdown>

-   :lucide-history:{ .lg .middle } **1. How consensus evolved**

    ---

    From Paxos to PBFT to Raft: which problem each protocol solved, what it left open, and where it runs today.

    [:lucide-arrow-right: Read the history](01-coordination-and-consistency/01-consensus/01-evolution.md)

-   :lucide-book-open:{ .lg .middle } **2. Why consensus? A story**

    ---

    Start with one bank transfer and three simple designs. Break each one, and rebuild Raft from the pieces.

    [:lucide-arrow-right: Read the story](01-coordination-and-consistency/01-consensus/02-why-consensus.md)

-   :lucide-building-2:{ .lg .middle } **3. Consensus in real systems**

    ---

    How etcd, Kafka, CockroachDB, Spanner, DynamoDB and others use consensus, and how little of their data they put through it.

    [:lucide-arrow-right: Tour the systems](01-coordination-and-consistency/01-consensus/03-real-world-systems.md)

</div>

<div class="dsp-cta" markdown>

**New here?** The consensus history is the best place to start.

[Start reading :lucide-arrow-right:](01-coordination-and-consistency/01-consensus/01-evolution.md){ .md-button .md-button--primary }
[:fontawesome-brands-github: View on GitHub](https://github.com/ssg721/distributed-systems-patterns){ .md-button }

</div>
