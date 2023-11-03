# Spanner

> _Spanner is a scalable, globally-distributed database designed, built, and deployed at Google._

* Large-scale database;
* Supports **strict-serializability**;
* Supports **partial replication**;
* Uses a **multi-version approach** - allows for **non-blocking reads**.

## Partial Replication

* Nodes are not required to have a full copy of the database;
  * Pro: **scalability**;
  * Con: remote reads during transactions;
* Database divided in multiple **partitions**;
* Each partition is managed by a different **virtual server**, which is a set of **paxos replicas**.

### Replicated Servers

* During steady state, **transactions are executed by the leader of each Paxos**;
* If the leader updates the state of the virtual server, it runs Paxos to ensure that the change is **committed in a majority of replicas**;

---

## Consistency

* Spanner offers **strict serializability** - the result of any execution is the same as if the transactions of all clients were executed in some serial order;
  *  When a transaction commits, it becomes visible to all subsequent transactions.

### Timestamps

* Update transactions are assigned a **timestamp** at commit time;
* Timestamps are used to identify the version of the objects;
* Spanner timestamps are derived from **synchronized clocks**; Google implemented their own **TrueTime API**, a clock synchronization service;
* When a process reads the synchronized clock, it gets the **current time and the current clock synchronization error**.

#### TrueTime API:

* `tt.now()` - `TTinterval` - returns the current time and the current clock synchronization error;
* `tt.after(t)` - `boolean` - returns `true` if the current time is after the time `t`;
* `tt.before(t)` - `boolean` - returns `true` if the current time is before the time `t`.

---

## Multi-versioning

* Spanner keeps **multiple versions of each object**;
* There is a **total order of versions** of each object, corresponding to the order in which the transactions commit;

### Read Snapshot

* All transactions read the database at a given time, defined when the **transaction starts**;
* To achieve strict-serializability, read snapshot must be in the future of all transactions that commit before the read starts;
* The client also can opt to read from an **older snapshot**;
* **The read snapshot is defined when the transaction starts**.

### Commit Timestamp

* **Commit timestamp != read timestamp**;
* Commit timestamp is decided when the transaction commits;
* Must be larger than timestamps of all transactions that commit before it;

---

## Concurrency Control

* Spanner uses a **mix of pessimistic and optimistic concurrency control**;
* Transactions read from the read snapshot;
* **All updates are buffered in memory and applied at commit time**;
* Before committing, the transaction checks if the read set is valid (no other transaction has committed a write to the same object);
  * if the read set is valid, the transaction commits;
  * if the read set is not valid, the transaction aborts and restarts.

> Update transactions **obtain a read lock** on all objects they read - deadlocks are avoided by aborting younger transactions in case of conflicts.
>
> If another transaction commits and changes the value, the transaction aborts and restarts, so, this means that if the transaction reaches commit time, this means that the read set is valid.

### Commit

* Commits require **coordination between all partitions that the transaction accessed** - one of the partitions is chosen as the **coordinator**;
* The coordinator **sends the write set to all partitions**;
* Each create tentative versions of the objects, using their local time, and **send the tentative versions to the coordinator**;
* The coordinator **chooses the largest timestamp** among the tentative versions and sets this as the **commit timestamp**;
* All partitions **re-adjust the final time of the transaction**;
* **Transactions are applied in order of the final timestamp**;
* After deciding the final timestamp, the coordinator waits until **all clocks of all participants are in the future of commit time** - ensuring that the participants will not commit a transaction "in the future";
* After this, it sends the final commit timestamp to all participants, which **commit the transaction**.

<!--TODO: Study Collective agreement total order protocol - spanner uses this algorithm to define the total order of transactions-->

#### Blocking Reads

* If a transaction reads an object that was written by a transaction that has not yet committed, the transaction **blocks** until the other transaction commits or aborts;
  * If the final timestamp of the other transaction is in the future of the read snapshot, the transaction reads the previous value;
  * If the final timestamp of the other transaction is in the past of the read snapshot, the transaction **waits** until the other transaction commits or aborts, and if commited, reads that new value;
* So, to execute a transaction that needs to be executed with strict-serializability, the transaction must be executed with a read snapshot in the future of all transactions that commit before it - this can be achieved by using `TT.now().latest` since nodes use **synchronized clocks**;
* An advantage for **clients that do not need strict-serializability** is that they can read from an **older snapshot, without blocking**.
