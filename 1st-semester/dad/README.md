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

### Broadcasters

**URB (Uniform Reliable Broadcast):**
* URB is a communication primitive that ensures reliable message delivery to all correct processes in a distributed system.
* It is often used as a building block for other distributed systems protocols.

**Atomic Broadcast or Total Order Broadcast:**
* Atomic Broadcast is a protocol that ensures that messages are delivered to all nodes in the same order, and they are either delivered to all or none.
* It provides a strong form of message ordering and reliability.

**FIFO Broadcast:**
* FIFO Broadcast ensures that messages are delivered in the same order they were sent to all correct processes.
* It is a weaker form of ordering compared to Atomic Broadcast.

### Consensus

* Consensus is a fundamental problem in distributed systems where nodes need to agree on a single value.
* Paxos and Multi-Paxos are consensus algorithms used to solve this problem.

* **Paxos:**
  * Paxos is a consensus algorithm that helps nodes agree on a value even in the presence of failures and network partitions.

* **Multi-Paxos:**
  * Multi-Paxos extends Paxos to handle multiple consensus instances efficiently, often used in state machine replication.

* **Coordination Services (Chubby and Zookeeper):**
  * Coordination services like Chubby (Google) and Zookeeper (Apache) provide distributed locking and coordination for managing distributed systems and ensuring synchronization.

* **State Machine Replication:**
  * State Machine Replication is a technique to achieve fault tolerance by replicating a state machine across multiple servers.

* **Lease-based Replication:**
  * Lease-based replication is a method to manage leadership and failover in distributed systems, ensuring that only one leader operates at a time.

### Database Replication

* Database replication involves replicating a database across multiple nodes for load balancing, fault tolerance, and improved performance.

* **Uses Paxos** as a building block;
* Assumes **full replication** (all replicas have the entire database);
* **Support parallelism** because transactions can be executed at any replica   (multi-master approaches);
* Requires **Paxos to totally order transactions**;
* **Blocking** (due to Paxos).

* **Chain Replication:**
  * Chain Replication is a replication technique where messages pass through a chain of nodes, with one node as the head and another as the tail, providing fault tolerance.

* **Primary-Backup Replication:**
  * Primary-Backup Replication is a replication technique where one node is the primary and the others are backups, and the primary is responsible for handling all requests.

* **Multi-Master Replication:**
  * Multi-Master Replication is a replication technique where all nodes are masters, and they can all handle requests, which are then propagated to the other nodes.

### Group Communication

* Group Communication involves sending messages to a group of nodes, and it can be used for various purposes, including fault tolerance and data distribution.

* **View Synchrony:**
  * View Synchrony refers to the concept of maintaining a consistent view of the system configuration, which is essential for distributed systems' stability and coordination.

### Spanner

* **Uses Paxos** as a building block;
* Supports **partial replication**;
* Support **parallelism** because transactions that only access one partition can be executed in parallel;
* In each partition, update transactions need to be executed by the Paxos leader;
* **Total order is achieved via “collective agreement”** protocol executed by the leaders of each Paxos group involved in the transaction;
* **Blocking** (due to Paxos).