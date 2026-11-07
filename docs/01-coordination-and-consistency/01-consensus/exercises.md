# Consensus: Design Exercises

Realistic systems where consensus shapes the design. Try a design for each before reading the worked solution (added in L13). For each one, decide: which facts need agreement, what system provides it, what stays out of it, and what happens on each kind of failure.

### 1. A scheduler that never double-runs
Your company runs 50,000 scheduled jobs: reports, billing runs, cleanups. Today a single VM runs them, and when it died last month, billing ran six hours late. Make it highly available without ever running a billing job twice.

### 2. A shared lock service
Several services need mutual exclusion on resources ("only one process may migrate tenant 42's data"). Design a lock service they can all use. Locks must survive a server crash, and a lock holder that freezes for 30 seconds must not be able to corrupt anything.

### 3. A strongly consistent key-value store
A store for account balances: 2 TB of data, 50,000 writes per second, three availability zones in one region. No acknowledged write may ever be lost, and reads must see the latest write.

### 4. Config and feature flags
5,000 service instances read feature flags. A change must reach all of them within seconds, and no instance may ever see version 7 of the config and then version 6.

### 5. Collision-free IDs
A short-URL service generates 20,000 IDs per second across 30 app servers that scale up and down. IDs must never collide.

### 6. Going multi-region
Take exercise 3, but users are in India, Europe and the US, and the business wants to survive a full region outage. What does each write now cost, and what would you change?
