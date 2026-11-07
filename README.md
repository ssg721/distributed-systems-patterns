# Distributed Systems Patterns

A practical guide to the patterns that make distributed systems work: what problem each one solves, how it works underneath, how real systems use it, and what it costs.

**Read it online:** https://ssg721.github.io/distributed-systems-patterns/

Every pattern is explained from the problem up: a concrete system that breaks, the obvious fixes that fail, and the pattern that works. Each one then shows how production systems such as etcd, Kafka, Spanner and CockroachDB use it, and what lesson to carry into your own designs.

## Contents

**1. Coordination & Consistency**

- [Consensus (Raft / Paxos)](docs/01-coordination-and-consistency/01-consensus/README.md) · [in real systems](docs/01-coordination-and-consistency/01-consensus/real-world-systems.md) · [design exercises](docs/01-coordination-and-consistency/01-consensus/exercises.md)
- Leases + fencing tokens *(coming)*
- Quorum reads/writes *(coming)*
- Logical clocks *(coming)*
- 2PC vs Saga *(coming)*
- Transactional outbox + CDC *(coming)*
- Idempotency keys *(coming)*

The full roadmap is on the [home page](docs/index.md).

## Running the site locally

The site is built with [Zensical](https://zensical.org) from the Markdown in `docs/`.

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
zensical serve
```

Then open http://localhost:8000. Every push to `main` rebuilds and publishes the site through GitHub Actions.
