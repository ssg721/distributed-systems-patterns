# Consensus: Design Exercises

Each exercise is a real system in which consensus decides the design. Try a design for each exercise before you read the solution. Lesson L13 adds the solutions.

For each exercise, decide these items:

- Which facts need agreement.
- Which system gives that agreement.
- Which data stays out of the consensus system.
- What occurs for each type of failure.

### 1. A scheduler that never runs a job twice

Your company runs 50,000 scheduled jobs, for example reports, billing runs and cleanups. Today, 1 virtual machine (VM) runs all the jobs. Last month, that VM stopped, and billing ran 6 hours late. Make the scheduler highly available. It must never run a billing job twice.

### 2. A shared lock service

Many services need mutual exclusion on resources. Example: only 1 process can move the data of tenant 42. Design a lock service that all the services can use. A lock must survive a server crash. If a lock holder freezes for 30 seconds, it must not corrupt data when it starts again.

### 3. A strongly consistent key-value store

Design a store for account balances. The store has 2 TB of data and 50,000 writes each second. It uses 3 availability zones in 1 region. The store must never lose an acknowledged write. Each read must show the latest write.

### 4. Configuration and feature flags

5,000 service instances read feature flags. A change must get to all the instances in a few seconds. An instance must never see version 7 of the configuration and then version 6.

### 5. IDs that never collide

A short-URL service makes 20,000 IDs each second on 30 application servers. The number of servers increases and decreases. 2 IDs must never be the same.

### 6. Expand to many regions

Start with exercise 3. Now the users are in India, Europe and the US. The business must continue to operate if a full region stops. What does each write cost now? What do you change?
