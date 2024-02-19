# Basic Abstractions of Dependable Consensus 

> **Complexity** is masked via abstractions for building dependable services.

A distributed systems model is a combination of **processes**, **communication channels**, and **timing assumptions**. We also need to consider **failure detection** and **leader election**.

## Processes

* **Processes**: entities that execute code and communicate with each other;
* A distributed system is made of a finite set of **N processes**, denoted by `p1, p2, ..., pN`, or `p, q, r, ...`;
* Each process has a unique **identifier**, and know each other's identifiers;
* A process executes a **sequential program**, and executes a set at every tick of its local clock; a step consists of:
  * Receiving a message;
  * Executing a local computation;
  * Sending a message;
* Syntax:

```
upon event <Event1, attr1, attr2, ...> do
  trigger <Event2, attr1, attr2, ...>;
  ...
```

> ### Liveness and Safety
>
> * **Liveness**: something good eventually happens;
>   * Given a trace (sequence of events) that does not obey the property, it can be extended to a trace that does;
> * **Safety**: something bad never happens.
>   * Given a trace that does not obey the property, it can never be extended to a trace that does.

---

## Fault Assumptions

* A process is **correct** if it does not fail throughout its execution;
* There are two kinds of faults mainly considered:
  * **Omissions**: a process fails to send a message, or a message is lost;
    * **Crash-stop**: a process that omits a message to a process, crashes;
  * **Arbitrary**: a process sends a message that was not supposed to, or sends a message with the wrong content - **Byzantine** faults.
    * We assume a maximum number of `f` processes can be faulty, and `N = 3f + 1`.

---

## Abstracting Communication

* Processes communicate by message passing through communication channels;
* Messages are uniquely identified by a **sender ID** and a **sequence number**.

### Fair Lossy Links

* **Fair-loss**: if a message `m` is sent infinitely often from a correct process `pi` to a correct process `pj`, then `m` is delivered infinitely often by `pj`;
* **Finite duplication**: if a message `m` is sent a finite number of times from a correct process `pi` to a correct process `pj`, then `m` is delivered a finite number of times by `pj`.
* **No creation**: no message is delivered unless it was sent.

### Stubborn Links

* **Stubborn delivery**: if a correct process `pi` send a message `m` to a correct process `pj`, then `pj` delivers `m` infinitely often;
* **No creation**: no message is delivered unless it was sent.

### Perfect (or Reliable) Links

* **Validity**: if a correct process `pi` sends a message `m` to a correct process `pj`, then `pj` eventually delivers `m`;
* **No duplication**: no message is delivered more than once;
* **No creation**: no message is delivered unless it was sent.

### Authenticated Perfect Links

* Now we move to the Byzantine model main challenge: Byzantine processes may forge messages, pretending to be other processes. We need to ensure that the sender of a message is who it claims to be;
* Rely on **Message Authentication Codes (MACs)** to ensure that a message was sent by the claimed sender;
* **Reliable delivery**: if a correct process `pi` sends a message `m` to a correct process `pj`, then `pj` eventually delivers `m`;
* **No duplication**: no message is delivered more than once;
* **Authenticity**: if a correct process `pi` sends a message `m` to a correct process `pj`, then `pj` is sure that `m` was sent by `pi`.

---

## Timing Assumptions

* **Synchronous**:
  * **Processing**: the time it takes for a process to execute a step is bounded and known;
  * **Delays**: there is a known upper bound on the time it takes for a message to be delivered;
  * **Clocks**: the drift between clocks is bounded and known;
* **Eventually synchronous**: the time assumptions hold eventually;
* **Asynchronous**: no timing assumptions are made.

---

## Failure Detection

* A failure detector is defined by events and properties:
  * **Events**: `<crash, p>` - process `p` is suspected to have crashed;
  * **Properties**:
    * **Completeness**: are all crashed processes eventually suspected?
    * **Accuracy**: can a correct process be suspected?
* **Perfect failure detector**:
  * **Strong completeness**: if a process `p` crashes, then every correct process eventually suspects `p`;
  * **Strong accuracy**: no correct process is suspected.
* **Eventually perfect failure detector**:
  * **Strong completeness**;
  * **Eventually strong accuracy**: no correct process is suspected eventually;
  * Implemented by a **heartbeat** mechanism - processes periodically send messages to each other with a timeout; if a process does not receive a heartbeat from another process, it suspects it has crashed.

---

## Leader Election

* **Leader**: a process that is responsible for coordinating the others;
* Identifies a correct process;
* Properties;
  * **Eventual detection**: every correct process is eventually elected as leader;
  * **Accuracy**: if a process is a leader, then all previously elected leaders have crashed.

### Byzantine Leader Election

* **Eventual succession**: if more than `f` correct processes that trust some process `p` complain about `p`, then `p` is eventually not elected as leader;
* **Putsch resistance**: a correct process does not trust a new leader unless at least one correct process has complained against the previous leader;
* **Eventual agreement**: there is a time after which no two correct processes trust different processes.

---

## Distributed Systems Models

Some relevant combinations:

* **Fail-stop**: crash failure model, perfect links, perfect failure detector;
* **Fail-noisy**: crash failure model, perfect links, eventually perfect failure detector;
* **Fail-silent**: crash failure model, perfect links;
* **Fail-silent arbitrary**: arbitrary failure model, authenticated perfect links;
* **Fail-noisy arbitrary**: arbitrary failure model, authenticated perfect links, eventually perfect failure detector.