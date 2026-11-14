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
[See the roadmap](#roadmap){ .md-button }

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

Every system that runs on more than one machine eventually hits the same problems: machines crash, networks drop or delay messages, and a node that is slow looks exactly like a node that is dead. A small set of patterns, such as consensus, fencing tokens, quorums and idempotency, exists to handle those problems. This site explains them so you can **understand them, remember them, and recognise them in the systems you use every day.**

You don't need to be an expert. If you've built a backend service and used a database, you have everything you need.

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

## How every topic is told

<p class="dsp-section-lead">Patterns are easy to recite and hard to apply. So each topic is built from the problem up, not from the definition down.</p>

<div class="dsp-steps" markdown>

<div class="dsp-step" markdown>
<span class="dsp-step__num">1</span>

**A concrete situation**

A real system, such as a bank transfer or a job scheduler, with a real failure.
</div>

<div class="dsp-step" markdown>
<span class="dsp-step__num">2</span>

**The obvious fixes break**

Each attempt fails in one specific, memorable way.
</div>

<div class="dsp-step" markdown>
<span class="dsp-step__num">3</span>

**The pattern, rule by rule**

Every rule fixes one of the breaks you just saw.
</div>

<div class="dsp-step" markdown>
<span class="dsp-step__num">4</span>

**The pattern in production**

Where etcd, Kafka, Spanner and others use it, and the lesson for your own designs.
</div>

</div>

The aim is that you could re-derive the pattern on a whiteboard, and spot when a design needs it, long after you've forgotten the exact wording.

<div class="dsp-cta" markdown>

**New here?** The consensus history is the best place to start.

[Start reading :lucide-arrow-right:](01-coordination-and-consistency/01-consensus/01-evolution.md){ .md-button .md-button--primary }
[:fontawesome-brands-github: View on GitHub](https://github.com/ssg721/distributed-systems-patterns){ .md-button }

</div>
