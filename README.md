# Distributed Systems Patterns

This guide explains the patterns that make distributed systems work. For each pattern, it shows the problem, the mechanism, the use in real systems, and the cost.

**Read the guide online:** https://ssg721.github.io/distributed-systems-patterns/

Each pattern starts with a problem: a real system that fails, and the obvious fixes that also fail. Then the guide shows the pattern that works. Last, it shows how production systems use the pattern, for example etcd, Kafka, Spanner and CockroachDB. Each system gives a lesson for your own designs.

## Contents

**1. Coordination and Consistency**

| Concept | Status |
|---------|--------|
| [Consensus: Raft and Paxos](docs/01-coordination-and-consistency/01-consensus/README.md) · [in real systems](docs/01-coordination-and-consistency/01-consensus/real-world-systems.md) · [design exercises](docs/01-coordination-and-consistency/01-consensus/exercises.md) | In progress |
| Leases and fencing tokens | Planned |
| Quorum reads and writes | Planned |
| Logical clocks | Planned |
| Two-phase commit (2PC) and Saga | Planned |
| Transactional outbox and change data capture (CDC) | Planned |
| Idempotency keys | Planned |

The [home page](docs/index.md) shows the full roadmap.

## Run the site on your computer

The guide uses [Zensical](https://zensical.org) to build the site from the Markdown files in `docs/`.

1. Create a Python virtual environment, and activate it:

    ```bash
    python3 -m venv .venv && source .venv/bin/activate
    ```

2. Install the dependencies:

    ```bash
    pip install -r requirements.txt
    ```

3. Start the local server:

    ```bash
    zensical serve
    ```

4. Open http://localhost:8000 in your browser.

When you push to `main`, GitHub Actions builds the site again and publishes it.
