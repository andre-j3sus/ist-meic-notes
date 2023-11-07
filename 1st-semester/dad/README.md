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

* **Linearizability:** if a write operation completes before a read operation starts, then the read operation returns the value written by the write operation;
  * includes notion of time;
* **Serializability:** transactions are executed in a serial order;
  * does not include notion of time;
* **Strict Serializability:** transactions are executed in the same order, and if a write operation completes before a read operation starts, then the read operation returns the value written by the write operation.

### Consensus

Consensus is a fundamental problem in distributed systems where nodes need to **agree on a single value**;

* **Paxos** is a consensus algorithm that helps nodes agree on a value **even in the presence of failures and network partitions**;
  * Steps: 1. Prepare, 2. Promise, 3. Accept!, 4. Accepted;
* **Multi-Paxos** extends Paxos to handle **multiple consensus instances efficiently**, often used in state machine replication;
  * The leader sends multiple prepare messages to different instances in a single round - `Prepare([i, n], <bullet num>`;
  * If the prepare is accepted, then it just needs to send the Accept! message;
  * This optimization **avoids phase 1 of paxos, so 2 communication steps (1 RTT) are saved**.

### Coordination Services

Coordination services like Chubby (Google) and Zookeeper (Apache) provide **distributed locking and coordination** for managing distributed systems and ensuring synchronization.

* **Chubby:**
  * Supports **locks** that can be used for **coordination**;
  * Locks are **leased** and **renewed** periodically - if the lease expires, the lock is released;
  * **Locks are replicated** across multiple nodes for fault tolerance, using **Paxos**;
  * Reads and writes are **linearizable** - **only directed to the leader**;
  * Clients **cache** the lock state for **performance** - they only need to contact the server when the lock is not cached or the lease expires - **if it expires, and Chubby cannot contact the client, the client needs to invalidate its own cache**.

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
  * `m` must be delivered in the same view it was sent;
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
    * in an instance, if a stop command is accepted, **no other command can be accepted**.
    * if a leader finds out in the read phase that it should adopt a **stop for instance `i` with timestamp `ts`** and some other command (different from stop), in some instance **`j > i`, with timestamp `ts'>ts`, then it ignores the stop command**, because it cannot have been decided in instance `i` yet.

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
  * Send transactions via **TOB** to other nodes - `TOB(m)`;
  * Each node **executes transactions in the same order** and then commits them - `Exec(m)` and then `Commit(m)`;
* **Multi-Master:** all nodes are masters, and they can all handle requests, which are then propagated to the other nodes.
  * **Global certification**: only one node executes the transaction, then propagates it using **TOB** to the other nodes;
    * The other nodes certify the transaction and then **commit** it;
    1. `Exec(m)` by the master;
    2. `TOB(m)` by the master;
    3. `Certify(m)` by all nodes;
    4. `Commit(m)` or `Abort(m)` by all nodes.
  * **Local certification**: only one node executes the transaction and then propagates it using **TOB** to the other nodes;
    * **Only the node that executed the transaction certifies it** and then **commits** it (sending via **URB** to the other nodes, so they can commit it too).
    1. `Exec(m)` by the master;
    2. `TOB(m)` by the master;
    3. `Certify(m)` by the master;
    4. `Commit(m)` or `Abort(m)` by the master and `URB(m)` by the master;
    5. `Commit(m)` or `Abort(m)` by the other nodes.

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
  * Advantages: **efficient routing** and **efficient search**;
  * Disadvantages: **complexity** and **scalability** - **routing tables grow with the number of nodes**;
* **Unstructured** - the placement of content is completely **random** - **each node has a list of neighbors** that it can use to route messages to the destination node.
  * Advantages: **simplicity** and resilience to **node failures**;
  * Disadvantages: **probabilistic search** and **inefficient routing**.

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
  * Conceals **DHT** (Distributed Hash Table) with **DOLR** (Distributed Object Location Resolution);
  * 160-bit GUIDs - replicated resources are published under the same GUID;
  * Replicas can be placed close to frequent users, to improve performance.

* **Dynamo**
  * **Key-value store** that uses **consistent hashing** to **partition data** across multiple nodes;
    * Storage nodes organized in a Chord-like ring;
  * Keys replicated across multiple nodes;
  * No Paxos for writes - **eventually consistent** - **last writer wins**;
  * Uses **gossip-based protocol** to **propagate updates**;
  * Each item is stored in the **home node**, and in the next `N-1` successor nodes - **preference list**;
    * Put operation directed to the home node - acts as the **coordinator**;
    * Read operations are executed at a read quorum - **R** nodes, and the most recent version is returned; if two versions are found, both are returned, and the client may attempt to resolve the conflict.


### Papers

From classes:

* [Paxos Made Simple](https://www.microsoft.com/en-us/research/uploads/prod/2016/12/paxos-simple-Copy.pdf);
* [Chubby](https://static.googleusercontent.com/media/research.google.com/en//archive/chubby-osdi06.pdf);
  * [Paxos Made Live](https://www.microsoft.com/en-us/research/uploads/prod/2016/12/paxoslive-1.pdf);
* [Zookeeper](https://www.usenix.org/legacy/event/usenix10/tech/full_papers/Hunt.pdf);
* [Reconfiguring a State Machine](https://lamport.azurewebsites.net/pubs/reconfiguration-tutorial.pdf);
* [Reconfiguring Replicated Atomic Storage - A Tutorial](https://iditkeidar.com/wp-content/uploads/files/ftp/AKMMS-reconfigure.pdf);
* [Spanner](https://static.googleusercontent.com/media/research.google.com/en//archive/spanner-osdi2012.pdf);
* [Cure: Strong Semantics Meets High Availability and Low Latency](https://pages.lip6.fr/Marc.Shapiro/papers/Cure-final-ICDCS16.pdf);
* Eiger - [Stronger Semantics for Low-Latency Geo-Replicated Storage](https://www.cs.cmu.edu/~dga/papers/eiger-nsdi2013.pdf);
* [Dynamo](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf).

From presentations:

* [Chain Replication for Supporting High Throughput and Availability](https://www.cs.cornell.edu/home/rvr/papers/OSDI04.pdf);
* [Sparkle: Speculative Deterministic Concurrency Control for Partially Replicated Transactional Stores](https://www.computer.org/csdl/proceedings-article/dsn/2019/005700a164/1cI6ijtMUMg);
* [High Throughput Replication with Integrated Membership Management](https://www.usenix.org/system/files/atc22-fouto.pdf);
* [Megastore: Providing Scalable, Highly Available Storage for Interactive Services](https://www.cidrdb.org/cidr2011/Papers/CIDR11_Paper32.pdf);
* [Antipode: Enforcing Cross-Service Causal Consistency in Distributed Applications](https://www.dpss.inesc-id.pt/~rodrigo/antipode-sosp2023.pdf);
* [Transactional Causal Consistency for Serverless Computing](https://dl.acm.org/doi/pdf/10.1145/3318464.3389710);
* [HyParView: a membership protocol for reliable gossip-based broadcast](https://asc.di.fct.unl.pt/~jleitao/pdf/dsn07-leitao.pdf).