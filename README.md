# Distributed Systems Patterns

Learn the patterns behind distributed systems by watching simple designs break, one failure at a time.

**Read it online:** https://ssg721.github.io/distributed-systems-patterns/

Each pattern starts from a concrete system that fails, tries the obvious fixes, shows exactly why they break, and builds the pattern one rule at a time. Then it shows how production systems such as etcd, Kafka, Spanner and CockroachDB use it, and what lesson to carry into your own designs.

## Contents

**Part 1: Coordination and consistency**

- **Consensus**
    1. [How consensus evolved: Paxos, PBFT, Raft](docs/01-coordination-and-consistency/01-consensus/01-evolution.md)
    2. [Why consensus? A story](docs/01-coordination-and-consistency/01-consensus/02-why-consensus.md)
    3. [Consensus in real systems](docs/01-coordination-and-consistency/01-consensus/03-real-world-systems.md)
- Leases and fencing tokens *(coming next)*
- Quorum reads and writes *(planned)*
- Logical clocks *(planned)*
- Two-phase commit vs sagas *(planned)*
- Transactional outbox and CDC *(planned)*
- Idempotency keys *(planned)*

The full roadmap is on the [home page](docs/index.md).

## Running the site locally

The site is built with [Zensical](https://zensical.org) from the Markdown in `docs/`, configured in `zensical.toml`. Zensical needs Python 3.11 or newer.

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
zensical serve
```

Then open http://localhost:8000.

## Publishing

Every push to `main` builds the site and publishes it to GitHub Pages through `.github/workflows/deploy.yml`.
