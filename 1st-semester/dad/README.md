# [Design and Implementation of Distributed Applications](https://fenix.tecnico.ulisboa.pt/disciplinas/PADI/2021-2022/1-semestre)

## Syllabus

1. [Consensus](1-consensus.md);
2. [Coordination Services](2-coordination-services.md);
3. [Group Communication and View-Synchrony](3-group-communication-view-synchrony.md);
4. [Reconfigurable SMR and Reconfigurable Registers](4-reconfigurable-smr-reconfigurable-registers.md);
5. [Database Replication](5-database-replication.md);
6. [Spanner](6-spanner.md);
7. [Transactional Causal Consistency](7-transactional-causal-consistency.md);
8. [P2P Systems](8-p2p-systems.md).

---

## Key Concepts

### Consensus

Consensus is a fundamental problem in distributed systems where nodes need to **agree on a single value**;

* **Paxos** is a consensus algorithm that helps nodes agree on a value **even in the presence of failures and network partitions**;
  * Steps: 1. Prepare, 2. Promise, 3. Accept!, 4. Accepted;
* **Multi-Paxos** extends Paxos to handle m**ultiple consensus instances efficiently**, often used in state machine replication;
  * The leader sends multiple prepare messages to different instances in a single round.

### Coordination Services

Coordination services like Chubby (Google) and Zookeeper (Apache) provide **distributed locking and coordination** for managing distributed systems and ensuring synchronization.

* **Chubby:**
  * Supports **locks** that can be used for **coordination**;
  * Locks are **leased** and **renewed** periodically - if the lease expires, the lock is released;
  * **Locks are replicated** across multiple nodes for fault tolerance, using **Paxos**;
  * Reads and writes are **linearizable** - **only directed to the leader**;
  * Clients **cache** the lock state for **performance** - they only need to contact the server when the lock is not cached or the lease expires - **if it expires, the client needs to invalidate its own cache**.

* **Zookeeper:**
  * Clients can read from any replica, but **writes are forwarded to the leader** - **writes are linearizable**;
  * Clients can **create znodes** (zookeeper nodes) that can be **ephemeral** (deleted when the client disconnects) or **persistent** (deleted when the client deletes it);
  * Does not support **locks** - **clients can create ephemeral znodes to represent locks**;
  * If a client wants to perform **linearizable reads**, it needs to send a **sync** request to the replica - **write null** - to make the replica update its state before reading.

### View-Synchrony

View-Synchrony refers to the concept of **maintaining a consistent view of the system configuration**, which is essential for distributed systems' stability and coordination.

Guarantees:
* **Agreement:** **correct** processes deliver the **same sequence (order) of views and messages**;
* **Uniform Agreement:** if **any** process delivers a view, then **all** correct processes deliver the same view;
* **Integrity:** if `p` delivers `m`, then `m` was sent by `p` in the corresponding view;
* **Validity:** correct processes **always deliver messages send**.

Broadcast protocols:

* **URB (Uniform Reliable Broadcast):** if **any** process delivers a message, then **all** correct processes deliver the message;
* **Regular Reliable Broadcast:** if a **correct** process delivers a message, then **all** correct processes deliver the message;
* **FIFO Reliable Broadcast:** **all messages from the same process are delivered in the same order** by all correct processes;
* **Atomic Broadcast or Total Order Broadcast:** **all messages are delivered in the same order** by all correct processes.

### Stoppable Paxos

Stoppable Paxos is a variant of Paxos that allows **stopping the protocol** and **resuming it later**, to make **reconfiguration easier**.

* Easy approach but **slow**: do not start instance `i` until instance `i-1` is decided, and use a **special command to stop** the protocol;
* Better approaches:
  * **Delayed Stop Sign:** instance `i` can only start when values for instances `i-a` and lower are decided;
  * **Padding:** proposer proposes a reconfiguration for instance `i` and a **special null command for instances higher** than `i`;
  * **Brick-wall:** a new **stop** command is added; 
    * in an instance, if a stop command is accepter, **no other command can be accepted**.
    * _to be continued..._

### Reconfigurable Registers

Reconfigurable Registers are a **generalization of consensus** that allows **updating a register** with a **new value**. The **ABD algorithm** is an example of a reconfigurable register.

* Simple solution that uses Paxos to **totally order reconfiguration commands**;
* A client that learns about a `Ci` before writing in it, **writes in `Ci-1` a pointer to `Ci`**, to make sure that a majority of replicas of `Ci-1` will know about `Ci`; then, it **writes in `Ci`**;
* If later a client reads from `Ci-1` and finds a pointer to `Ci`, it **reads from `Ci`**.

### Database Replication

Database replication involves **replicating a database across multiple nodes** for **load balancing**, **fault tolerance**, and **improved performance**.

* **Primary-Backup:** one node is the primary and the others are backups;
  * reads are **performed by any node**;
  * writes are **performed by the primary** and **propagated to the backups**;
* **Replicated State Machine (RSM):** usage of **Paxos** to **totally order transactions**;
  * Send transactions via **TOB** to other nodes;
  * Each node **executes transactions in the same order**.
* **Multi-Master:** all nodes are masters, and they can all handle requests, which are then propagated to the other nodes.
  * **Global certification**: only one node executes the transaction, then propagates it using **TOB** to the other nodes;
    * The other nodes certify the transaction and then **commit** it;
  * **Local certification**: each node executes the transaction and then propagates it using **TOB** to the other nodes;
    * **Only the node that executed the transaction certifies it** and then **commits** it (sending via **URB** to the other nodes, so they can commit it too).

### Spanner

* **Uses Paxos** as a building block;
* Supports **partial replication**;
* Support **parallelism** because transactions that only access one partition can be executed in parallel;
* In each partition, update transactions need to be executed by the Paxos leader;
* **Total order is achieved via “collective agreement”** protocol executed by the leaders of each Paxos group involved in the transaction;
* **Blocking** (due to Paxos).

### TCC

...

### P2P
