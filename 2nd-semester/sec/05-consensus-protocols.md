# Consensus Protocols

* Each process has an initial value;
* All processes must agree on the same output decision value - **reaching consensus**;
* Must be **safe** despite faults and asynchrony.

> For more information about consensus, see [Consensus](../../1st-semester/dad/1-consensus.md) notes from Design and Implementation of Distributed Applications.

### Uniform Consensus (in the crash model)

* Events:
  * `propose(v)`: propose value `v`;
  * `decide(v)`: decide value `v`;
* Properties:
  * **Validity**: any decided value is a proposed value;
  * **Uniform Agreement**: no two processes decide different values;
  * **Termination**: every correct process eventually decides;
  * **Integrity**: no process decides twice.

To solve this problem, we can use the **Paxos** algorithm.

## [Paxos](https://lamport.azurewebsites.net/pubs/paxos-simple.pdf) -  Consensus in the crash model

* **Paxos** is a family of protocols for solving consensus in a network of unreliable processors;
* Any process can propose a value - first to reach a **majority** wins;
* Associate a timestamp with the value `<seqNum, pid>` to break ties;
* Protocol has two phases:
  * **Phase 1**: **Prepare** - a process sends a `prepare` message to all other processes with a proposal number - receives `promise` messages from a majority of processes;
    * Read the state of other to form a proposal;
  * **Phase 2**: **Accept** - if a process receives a majority of `promise` messages, it sends an `accept` message to all other processes with the proposal number and the value - receives `accepted` messages from a majority of processes;
    * Write the state of others to the local state;
* A **leader election** mechanism is used to avoid conflicts;
* **Multi-Paxos**: a sequence of Paxos instances to agree on a sequence of values;
* Paxos is **safe** since it uses a **majority** to decide.

---

## Byzantine Fault Tolerant Consensus

### Weak Byzantine Consensus

* Termination: every correct process eventually decides (same as uniform consensus);
* Integrity: no process decides twice (same as uniform consensus);
* Agreement: no two correct processes decide different values (same as uniform consensus);
* **Weak Validity**: if **all** processes are correct, and some process decides `v`, then `v` was proposed by some process;
  * If some processes is faulty, **any value may be decided**.

### Strong Byzantine Consensus

* **Strong Validity**: if all correct processes propose the same value `v`, then no correct process decides a value different from `v`;
  * This **does not** imply weak validity.

Weak validity requires that the decided value was proposed by some correct process - we will focus on this property.

### Implementing BFT Consensus

We will use a strategy similar to Paxos:

* **EpochChange**: choose a leader and make sure any previously decided value carries over to the new epoch;
  * Uses [**byzantine leader election**] from the previous lectures:
    * If the algorithm is not making progress, a process broadcasts a `ROUND-CHANGE` message to all other processes;
    * If a process receives more than `f` `ROUND-CHANGE` messages, it broadcasts `ROUND-CHANGE` to all other processes;
    * If a process receives more than `2f` `ROUND-CHANGE` messages, it starts a new round;
* **EpochConsensus**: try to reach decision within an epoch;
  * This may fail, and we may need to change the leader and start a new epoch;
  * Interface:
    * Request: `propose(v)`: propose value `v`; executed only by the leader;
    * Request: `abort()`: abort the current round; 
    * Indication: `decide(v)`: decide value `v`;
    * Indication: `aborted()`: the current round was aborted;
  * Properties:
    * **Validity**: if all processes are correct, and a process decides `v` at timestamp `ts`, then `v` was proposed by a leader with timestamp `ts'` such that `ts' <= ts`;
    * **Uniform Agreement**: no two correct processes decide different values;
    * **Lock-in**: once a process decides `v`, it cannot change its decision;
    * **Termination**: if the leader is correct, and has proposed a value, and no correct process aborts this consensus, then every correct process eventually decides;
  * Has two phases:
    * **Phase 1 - Read**: the leader sends a `READ` message to all other processes with the current state of the consensus - used to check if previous leaders have proposed a value, and to form a proposal;
    * **Phase 2 - Write**: if a process receives a quorum of `WRITE` messages from different processes containing the same proposal, it changes its state and broadcasts `ACCEPT` messages to all other processes;
      * When a quorum of `ACCEPT` messages is received, the process decides the value.

> **Note**: the terms **round** and **epoch** are used interchangeably.
