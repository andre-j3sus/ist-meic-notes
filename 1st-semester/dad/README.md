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

### Consistency Models

* **Linearizability:** every operation occurs instantaneously in a linear order;
  * property of a **single operation**;
  * includes notion of time;
* **Serializability:** transactions are executed in a serial order;
  * property of a **set of operations**;
  * does not include notion of time;
* **Strict Serializability:** transactions are executed in the same order in all replicas;
  * property of a **set of operations**;
  * does not include notion of time.

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

* **Distributed database** that **spans multiple datacenters**;
* Each datacenter has **multiple replicas** of each partition, that are **grouped in Paxos groups** - **virtual servers**;
  * Transactions are executed by the **leader** of the Paxos group;
* Supports **external consistency** (linearizability);
* Supports **partial replication**;
* Supports **strict serializability** (transactions are executed in the same order in all replicas);
* Uses a clock synchronization service called **TrueTime API** to **enforce linearizability in an efficient** way;
* Keeps multiple versions of each object - there is a **total order of versions, corresponding to the total order of transactions**.

### TCC

**Transactional Causal Consistency** is a **consistency model** that **combines causal consistency** with **transactions**. *Casual Consistency** is a consistency model that guarantees that if a process reads the latest version of a data item, it will read the latest version of all data items that are **causally related** - i.e., if one data item is updated based on another, the process will read the latest version of both.

* Multi-versioned databases;
  * Each update creates a new version of the data item;
  * Transactions read from a given **snapshot** of the database;

* **TCC Eiger**:
  * Does not support general transactions, **only read-only and write-only transactions**;
  * Uses **logical time** to give a global view of the data store;
  * When an object is read, the partition returns: the **value** of the object, the **commit timestamp** of the object, and the **local clock when the value is read** - this is called the promise (any future version will have a timestamp grater than the promise) - the **version is valid** in the interval `[commit timestamp, promise]`;
  * **Select the read timestamp**: 1. Check which object has the **newest commit timestamp**; 2. Then, among all other reads that have a promise higher than the commit timestamp, it selects the **lowest promise** - this is the **read timestamp**;
    * When an object have a **promise lower than the read timestamp**, the transaction needs to **read the object again**, now specifying the **read timestamp** - second round trip - a **third round** trip can be necessary if in the second round, the value is still **prepared**.
  * Concurrent updates are allowed, but **only one can be committed** - **last writer wins**.

* **TCC Cure**:
  * **General transactions**;
  * Uses **synchronized physical clocks** - clients maintain **vector clocks** to keep track of the causality of transactions;
  * When a transaction starts, it is assigned a **vector timestamp** that defines the **snapshot the transaction will read from** - contains the stable timestamp of all objects;
  * Transactions are committed in one data-center and then propagated to other data-centers, **sending the vector clock** of the transaction - the other data-centers **only commit the transaction after all transactions in its causal past have been applied**;
  * To allow concurrent updates on different data-centers, **Cure allows both transactions to commit and merge the two versions** - **last writer wins** - uses **Conflict-free Replicated Data Types (CRDTs)** to make merging concurrent updates easier.

### P2P

**Peer-to-Peer** systems are distributed systems where all nodes have the same capabilities and responsibilities, and there is no central authority.

|                               | Hybrid Centralization | Partial Centralization | Decentralization           |
| ----------------------------- | --------------------- | ---------------------- | -------------------------- |
| **Unstructured**              | Napster               | Gnutella               | Gnutella (initial version) |
| **Structured Infrastructure** | -                     | -                      | Chord, Pastry, Tapestry    |
| **Structured Systems**        | -                     | -                      | OceanStore                 |

* **Structured** - the overlay network is **organized** in a **structured way** - **each node has a routing table** that allows it to route messages to the destination node;
* **Unstructured** - the placement of content is completely **random** - **each node has a list of neighbors** that it can use to route messages to the destination node.

* **Chord**
  * Mapping of nodes to a **ring** - each node is responsible for a **range of keys**;
  * Key `k` is stored in the **first node with an ID greater than or equal to `k`** - **successor** of `k`;
  * When a node joins the network, certain keys are **reassigned** to the new node, from the **successor** of the new node;
  * When a node leaves the network, its keys are **reassigned** to the **successor** of the node;
  * Uses **finger tables** to route messages to the destination node - **each entry in the finger table is the successor of the node ID plus a power of 2**.

* **Pastry**
  * Nodes have a **128-bit ID**;
  * Messages are routed to the **node with the closest ID to the destination ID**;
  * Each node maintains a **routing table** with GUIDs viewed as **hexadecimal digits**
    * number of rows = number of digits in a GUID;
    * number of columns = 16, one for each hexadecimal digit;
  * When a node joins the network, it **contacts a node with a similar ID** and **copies its routing table**.

* **Tapestry**
  * ...

* **OceanStore**
  * ...

* **Dynamo**
  * ...