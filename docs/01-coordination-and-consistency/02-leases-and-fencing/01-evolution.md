# The Evolution of Distributed Coordination: How Leases and Fencing Conquered the Zombie Node

When you study system design today, concepts like leases and fencing tokens look like clean, elegant lines of code in a textbook. It is easy to take them for granted, assuming engineers designed them perfectly from day one.

In reality, these patterns were forged through years of painful production outages, split-brain scenarios, and silent data corruption. Understanding why they evolved the way they did is one of the best ways to train your engineering intuition for distributed systems.

## Phase 1: The Illusion of Eternity (Plain Locks with No Expiry)

In the early days of distributed computing, engineers tried the most intuitive approach to mutual exclusion: distributed locks.

**The Design:** A client requests a lock from a central lock server. The server grants it. The client does its critical work and sends a "release" message when finished.

**The Fatal Flaw:** What happens if the lock holder crashes, loses its network connection, or gets stuck in an infinite loop before releasing the lock?

**The Result:** Permanent deadlock. The resource remains locked forever, and the entire system grinds to a halt because no one can tell the difference between a "slow" process and a "dead" one.

## Phase 2: Time as an Ally (Leases, 1989)

To solve the dead-client problem, computer scientists Cary Gray and David Cheriton published a landmark SOSP paper in 1989 introducing the concept of a lease. Their paper used leases to keep cached files consistent between a file server and its clients. The same idea works for locks and leadership.

**The Breakthrough:** Instead of an indefinite lock, the lock server grants a time-bounded privilege: "You are the leader for the next 10 seconds."

**How it Worked:** If the client dies or gets disconnected, the lock server simply waits out the 10-second timer and automatically reclaims the lock. Deadlocks were replaced by a predictable, bounded waiting period.

**The Insight:** This innovation didn't require perfectly synchronized wall-clock time across the globe; it only relied on bounded clock drift—an assumption that hardware clocks don't speed up or slow down infinitely relative to one another.

## Phase 3: The Ghost in the Machine (The Pause Anomaly)

Leases solved crashes, but distributed systems soon ran into a subtler, terrifying enemy: the zombie process.

**The Flaw:** A lease only keeps you safe if the leader stops acting the moment its lease expires. But modern software runs in environments with garbage collection (GC) stop-the-world pauses, virtual machine live-migrations, kernel swapping, or sudden network partition queues.

**The Scenario:**

1. Leader A holds a 10-second lease.
2. Leader A hits a heavy 15-second GC pause. Its CPU freezes; it has no idea time is passing.
3. The 10-second lease expires. The coordination service gives the lease to Leader B.
4. Leader B starts writing new data and making critical decisions.
5. Leader A wakes up from its GC pause, completely unaware it has been replaced, and executes a write to the shared storage as if it were still the leader.

**The Time-of-Check to Time-of-Use Bug:** Checking "Is my lease still valid?" right before a network call doesn't protect you, because a pause can strike between the check and the actual write. This wasn't just theoretical; as noted in Martin Kleppmann’s Designing Data-Intensive Applications, real-world distributed stores like Apache HBase suffered from precisely this anomaly before proper safeguards were put in place.

## Phase 4: Brute-Force Hardware Fencing (STONITH)

Before software guardrails became standard, High Availability (HA) clusters solved the zombie problem with hardware-level violence: STONITH ("Shoot The Other Node In The Head").

**The Approach:** When a new leader takes over, it physically power-cycles the old node using remote management cards (IPMI/iDRAC) or cuts off its access to the storage area network (SAN) using SCSI persistent reservations or I/O fencing.

**Why it Evolved:** It successfully prevented split-brain corruption by ensuring the old node literally could not write anything anywhere.

**The Drawback:** It needs a fencing device for every node, such as a management card, a power switch or storage that can cut a node off. So it only works on machines you control.

**Still in Use:** STONITH did not go away. HA clusters built on Pacemaker depend on it today, and Red Hat [does not support](https://access.redhat.com/node/109603) a Pacemaker cluster that runs with `stonith-enabled=false`.

## Phase 5: Elegant Software Fencing (Monotonic Fencing Tokens)

Modern distributed coordination systems—like Apache ZooKeeper, Google Chubby, and etcd—added an elegant software alternative to hardware executioners: fencing tokens.

**The Mechanism:** Every time a lease or lock is granted, the coordination service increments a monotonic counter (e.g., transaction ID, zxid, or epoch number). If Node A held lease #5 and got paused, and Node B takes over as lease #6, every write request carries that token number.

**The Storage Check:** The storage engine or database maintains a record of the highest fencing token it has ever successfully processed. When a write arrives with token #5 while the storage has already seen #6, it instantly rejects it.

**The Division of Labor:**

- The lease decides who should be acting.
- The fencing token ensures that if a stale leader wakes up from a zombie state, the storage layer ignores it.

**The Catch:** The token only works if the storage checks it on every write and rejects older tokens. If the storage cannot do this check, the stale write still goes through, and you are back to the problem of Phase 3.

| Concept | What It Does | Primary Failure Mode It Solves |
|---------|--------------|--------------------------------|
| Plain Locks | Grants exclusive access until released. | Two nodes using the same resource at the same time. |
| Leases | Grants exclusive access for a fixed time window. | A crashed holder keeping the lock forever. |
| Fencing Tokens | Attaches an increasing sequence number to every write. | Zombie/paused leaders writing stale data (split-brain). |

## Read the originals

1. [Leases: An Efficient Fault-Tolerant Mechanism for Distributed File Cache Consistency](https://doi.org/10.1145/74850.74870), Gray and Cheriton, 1989. The paper that introduced leases.
2. *The Chubby Lock Service for Loosely-Coupled Distributed Systems*, Burrows, 2006. Describes sequencers, which are Chubby's fencing tokens.
3. [How to do distributed locking](https://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html), Kleppmann, 2016. The pause problem and fencing tokens, step by step.
