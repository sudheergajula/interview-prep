# 🚀 Distributed Systems: Consistency

## What Is Consistency?
Consistency in distributed systems means that **all replicas (nodes)** present the **same view of data at any moment**, despite concurrent operations or delays. Any read should reflect the current state of the system globally.

---

## Why Data Consistency Matters
- **Trust & Predictability**: Users receive reliable, expected outcomes.
- **Data Integrity**: Prevents corruption from conflicting or stale data.
- **User Confidence**: Consistency builds trust in system accuracy.

---

## Strong Consistency (Linearizability)
Strong consistency ensures:
1. **Immediate Read Visibility**: After a write is acknowledged, **all reads** reflect this latest write—no matter which replica is queried.
2. **Atomic Global Ordering**: All operations appear instantaneous and globally sequential.

**Key points**:
- Reads always get the most recent write or an error.
- Writes are fully propagated and confirmed across nodes before returning success.
- Acts like a single, synchronized system.

---

## 🔄 Consistency Models at a Glance

| Model                    | Description                                                    | Pros                                      | Cons                                                   |
|--------------------------|----------------------------------------------------------------|-------------------------------------------|--------------------------------------------------------|
| **Strong Consistency**   | Reads always see the most recent write globally.               | Predictable, simple to reason about.      | Higher latency; less available under network failures. |
| **Eventual Consistency** | Replicas may briefly differ, then converge.                    | Highly available and scalable.            | Temporary inconsistency; requires conflict handling.   |
| **Sequential / Causal**  | Preserves client or causal order, not global.                  | Easier for clients to understand.         | May allow limited inter-node inconsistencies.          |
| **Read your writes**     | Ensures that writer client always sees latest version of data. | Provide consistent view of data for user. | Maintaining stickiness with session/userid.            |
| **Monotonic Reads**      | Ensures that writer client always sees latest version of data. | Provide consistent view of data for user. | Maintaining stickiness with session/userid.            |


---

## Techniques to Implement Strong Consistency
- **Consensus Protocols** (e.g., Paxos, Raft)
- **Synchronous Replication**: Wait for replicas’ acknowledgment.
- **Quorum-Based Writes/Reads**: Majority rule for updates.
- **Distributed Locking / Two-Phase Commit**

---

## Challenges of Strong Consistency
- **Increased Latency**: Coordination slows operations.
- **Reduced Availability**: Partitions may block operations to maintain consistency.
- **Scalability Limits**: More nodes → more overhead.
- **Implementation Complexity**: Requires robust infrastructure and protocols.

---

## Real-World Examples
- **Banking systems**: Accurate balance updates.
- **Inventory management**: Preventing overselling.
- **Authentication**: Password changes must propagate immediately.
- **Distributed locks or unique ID generation**: Avoiding duplicates or conflicts.

---

## ✅ Example Flow: Strong Consistency in Action
1. User 1 writes `Val1 = 100` via Node C.
2. Node C updates local value, propagates to Nodes A, B, D, then responds success.
3. User 2 reads `Val1` from Node D immediately after.
4. Since Node D is updated, the read returns `100`.

---

## Summary
Strong consistency guarantees every operation reflects the latest system-wide state, enabling trust and correctness at the cost of latency and availability. Its implementation—via consensus, locking, and synchronous replication—requires careful consideration of trade-offs and complexity.

---

```markdown

::contentReference[oaicite:0]{index=0}
