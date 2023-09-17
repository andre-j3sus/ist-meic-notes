# [Consensus](https://w3.cs.jmu.edu/kirkpams/OpenCSF/Books/csf/html/DistConsensus.html#:~:text=Consensus%20in%20a%20distributed%20system,to%20determine%20a%20variable's%20value.)

> _**Consensus** is the process of **agreeing on one result among a group of participants**. This problem becomes difficult when the participants or their communication medium may experience **failures**._

* Set of **N** processes;
* Each process **proposes** a value;
* All correct processes **decide** on the same value;
* The output value must be a **proposed value**;
* The output value must be any of the proposed values - **all inputs are equally good**.

## Properties of Consensus

* **Termination**: all correct processes eventually **terminate/decide**;
* **Uniform Agreement**: all correct processes **decide on the same value**;
* **Integrity**: the value decided has been **proposed by some process**.

---

## Problems/Challenges

### Bogus Consensus

* Assume you have a URB (Uniform Reliable Broadcast) primitive with the following interface:
  * `broadcast(m)`: broadcasts message `m` to all processes;
  * `deliver(m)`: delivers message `m` to the application;
* and the following properties:
  * **Validity**: if a correct process broadcasts a message `m`, then it eventually delivers `m`;
  * **No duplication**: no message is delivered more than once;
  * **No creation**: if a process delivers a message `m`, then `m` was previously broadcast by some process;
  * **Agreement**: if a correct process delivers a message `m`, then all correct processes eventually deliver `m`.

Now assume the following algorithm - **Bogus Consensus**:

```
When propose(value):
  URB.broadcast(value)

When deliver(value_i):
  setOfValues.add(value_i)

When |setOfValues| == N:
  decide(MIN(setOfValues)) // MIN or other deterministic function
```

**Problem**: if a process fails (and never broadcasts), then the other processes will never decide - will **block forever**.

### Bogus with Perfect Failure Detector

> A Perfect Failure Detector is a failure detector that **eventually** **detects** every process crash.

```
alive = {p1, p2, ...} // initially |alive| = N

When fail(p_i):
  alive.remove(p_i)

When propose(value):
  URB.broadcast(value)

When deliver(value_i):
  setOfValues.add(value_i)

When |setOfValues| == |alive|:  // |alive| is the number of processes alive
  decide(MIN(setOfValues))      // MIN or other deterministic function
```

**Problem**: if a process proposes(broadcasts) a value and then crashes, while the URB is still executing:
* a process may receive the value, before the failure is detected;
* another process may detect the failure before receiving the value.

These processes will **decide on different values**.

---

### Terminating Reliable Broadcast, URB and Perfect Failure Detector

> A Terminating Reliable Broadcast  is a communication primitive used to disseminate a message among a set of processes in a reliable way.

* There is a special process that is the **sender** `s`;
  * `s` broadcasts a message `m`;
  * initiates the algorithm issuing `send(m)`;
  * other nodes initiate the algorithm issuing `wait()`;
* All correct process **eventually** execute `output(value)`;
  * where `value` is the message `m` sent by `s`;
  * or `null` if `s` crashes before sending `m`.

```
proposed = false

When TRB.wait():
  do nothing

When TRB.send(m):
    URB.broadcast(m)

When URB.deliver(m) and not proposed:
  proposed = true
  consensus.propose(m)

When fail(s) and not proposed:
  proposed = true
  consensus.propose(null)

When consensus.decide(value):
  TRB.output(value)
```

---

### Leader based Consensus with Perfect Failure Detector (P)

> A leader based consensus algorithm is an algorithm that **elects a leader** and then **uses the leader to decide**.

Works in **epochs/rounds** -  in each epoch there is a different leader;
  * Epoch `i` is led by process `pi`;
  * Epoch `i` starts only if epoch `i-1` has terminated - all leaders from previous epochs have failed.

#### Execution

1. The **leader** `pi` of epoch `i` **sends** its value `vi` to all processes;
2. When a process receives a value `vi` from the leader `pi`, it **adopts** `vi` as its value (forgetting any previous value) and sends back and **acknowledgement** to `pi`;
3. The leader waits until it receives an **acknowledgement** from **all correct processes** - all correct processes have adopted the leader's value;
4. The leader then sends a second message that **commits** its proposed value;
5. When a process receives a **commit** message from the leader, it **decides/outputs** on the leader's value.

---

### Perfect Failure Detector (P)

* Very hard to implement in a **real system**;
  * requires use of real-time OS and real-time network, plus bound and predictable loads;
* If processes may suffer arbitrary delays, it may be **impossible to distinguish between a process that is slow and a process that has crashed** -  **Unreliable Failure Detector**.

---

## Impossibility of Consensus (Fischer, Lynch and Paterson)

> There is no **deterministic** protocol that solves consensus in an asynchronous system where even a single process may suffer a crash fault

* **Proof by contradiction**:
  * Assume there is a protocol that solves consensus;
  * Show that the protocol **violates one of the three properties** of consensus.

#### Example 1

* Two processes `p1` and `p2` propose initially 0;
* `p2` crashes as soon as the execution starts;
* By the validity property, the correct process `p1` must decide on 0, at some instant `t1`.

#### Example 2

* Two processes `p1` and `p2` propose initially 0;
* `p1` crashes as soon as the execution starts;
* By the validity property, the correct process `p2` must decide on 0, at some instant `t2`.

#### Example 3

* Process `p1` proposes initially 0, and `p2` proposes initially 1;
* Messages between `p1` and `p2` are delayed, so that `p1` receives `p2`'s proposal only after it has decided on 0, and `p2` receives `p1`'s proposal only after it has decided on 1.

The solution for this is that the leader should be able to make a decision without receiving acknowledgements from all processes, just a **majority**.

When a new leader is elected, it should **enquire** the other processes to check what the previous leaders have done.

The solution to this is the **Paxos** algorithm.

---
---

## [Paxos](https://lamport.azurewebsites.net/pubs/paxos-simple.pdf)

...