# Database Replication

> _Database replication involves replicating a database across multiple nodes for load balancing, fault tolerance, and improved performance._

## Transactions

- Databases support transactions, which are a sequence of operations that are executed as a single unit of work.
- Bounded by `BEGIN TRANSACTION` and `COMMIT` statements;
- **Atomicity, Consistency, Isolation, Durability (ACID):**
  - **Atomicity:** all operations in a transaction are executed or none are;
  - **Consistency:** a transaction cannot bring the database from one valid state to another;
  - **Isolation:** concurrent execution of transactions leaves the database in the same state that would have been obtained if the transactions were executed sequentially;
  - **Durability:** once a transaction has been committed, it will remain so, even in the event of power loss, crashes, or errors.

---

## Strict Serializability

- Strongest consistency model for transactions;
- Transactions are executed in a serial order, as if they were executed one at a time;
- **Serializability:** the result of any execution is the same as if the transactions were executed in some serial order;
- Most databases do not provide strict serializability, as it is too expensive to implement, so they provide weaker consistency models: **serializability** and **snapshot isolation**.

---

## Concurrency Control

- **Concurrency control** is the process of managing simultaneous operations on a database, to ensure data integrity while processing multiple requests from multiple users;
- Purpose is to resolve conflicts between transactions that access the same database records;
- Can be implemented using **pessimistic** or **optimistic** approaches:
  - **Pessimistic:** assumes that conflicts are likely to happen, and aims to identify/resolve them as soon as possible; **slower**, but avoids conflicts;
  - **Optimistic:** assumes that conflicts are unlikely to happen, and aims to detect them only when they occur; **faster**, but conflicts can happen.

### Pessimistic Concurrency Control

- Assumes that **conflicts are likely to happen**, and aims to identify/resolve them as soon as possible;
- Usually based on **locking**, where a transaction must acquire a lock on a record before accessing it;
- Locking can generate **deadlocks**, where two or more transactions are waiting for each other to release locks, and none of them can proceed; deadlocks can be detected or avoided:
  - **Deadlock detection:** periodically check for deadlocks and abort one of the transactions;
  - **Deadlock avoidance:** use a protocol to ensure that deadlocks cannot happen, usually using **timestamps to order transactions**.

### Optimistic Concurrency Control

- Assumes that **conflicts are unlikely to happen**, and aims to detect them only when they occur;
- Data items have associated **version numbers**, which are incremented on each update;
- Transactions are executed without acquiring locks, and are atomically validated at the end:
  - If no conflicts are detected, the transaction is committed;
  - If conflicts are detected, the transaction is aborted and restarted.

---

## Database Replication

There are multiple approaches to database replication:

- **Primary-backup replication**;
- **Chain replication**;
- **Replicated state machine**;
- **Multi-master approaches**.

### Primary-Backup Replication

> _In primary-backup replication, one node is the primary, and all updates are sent to it; the primary sends updates to the backup nodes, which are read-only._

- **Primary:** receives all updates, and sends them to the backup nodes;
- **Backup:** receives updates from the primary, and applies them to its database;
  - Read operations can be performed on the backup nodes, but they are not allowed to perform write operations;

Update propagation can be done in two ways:

- **Eager:** the primary sends updates to the backup nodes as soon as they are received, and **waits for an acknowledgement** (ACK) from each backup node (or from a majority of nodes);
  - **Updates are not lost, but its slower**;
- **Lazy:** the primary sends updates to the backup nodes, but **does not wait for an ACK**;
  - **Updates can be lost, but its faster**.

This approach becomes very complicated without a perfect failure detector, as the primary node can fail, and the backup nodes must elect a new primary node. **Primary election** needs to be implemented using a consensus algorithm.

### Chain Replication

> _In chain replication, updates are sent to the head node, which sends them to the next node in the chain; the tail node applies the updates to its database. Read operations are performed on the tail node._

- **Head:** receives all updates, and sends them to the next node in the chain;
- **Tail:** receives updates from the previous node in the chain, and applies them to its database;
  - Read operations are performed on the tail node.

Updates are slower than in PB replication, but the system ensures **linearizability**.

### Replicated State Machine

> _Clients broadcast transactions to all nodes, using an atomic broadcast protocol; each node executes the transactions in the same order, and the results are the same._

- All nodes receive all updates, and execute them in the same order;
- **Serial order**;
- This has some performance issues, since commands **cannot be executed in parallel**;
- **Deterministic concurrency control** can be used to improve performance:
  - **Pessimistic:**
    - Locks required by the i-the transaction (based on the total order) are acquired before executing the transaction;
    - Conflicting transactions are executed in serial order;
    - Non-conflicting transactions can be executed out of order;
  - **Speculative:**
    - Transactions are executed speculatively, without acquiring locks;
    - If a conflict is detected, the transaction is aborted and restarted;
    - If no conflict is detected, the transaction is committed.

### Multi-Master Approaches

#### Global Certification

- Transactions can be executed in **any replica using optimistic concurrency control**;
- At commit time, the read and write sets of the transaction are sent to all replicas using an **atomic broadcast**;
- **All replicas validate the transaction** (check if all objects in the read set still have at validation time the same version that was read by the transaction);
- If the transaction is valid, it is committed in all replicas.

<!--TODO: Dealing with large read-sets, Bloom filters-->

#### Local Certification

- Transactions can be executed in **any replica using optimistic concurrency control**;
- At commit time, the **write-set (only)** is sent to all replicas using atomic broadcast;
- All replicas put the transaction in a **pending state**;
- The node that sent the write-set, validates the transaction, since it is the only one that has the read-set;
- If the transaction is valid, it sends a **commit message** to all replicas.

| Global Certification       | Local Certification    |
| -------------------------- | ---------------------- |
| Can be **faster**          | Can be **slower**      |
| Read-sets can be **large** | Can save **bandwidth** |
