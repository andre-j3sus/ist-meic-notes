# [Reconfigurable SMR and Reconfigurable Registers](https://lamport.azurewebsites.net/pubs/reconfiguration-tutorial.pdf)

## Reconfigurable SMR

- SMR runs a sequence of instances of Paxos;
- So far we have assumed that the set of processes that runs all instances is always the same;
- But it can make sense to **change the set of processes that execute Paxos**, for instance, to **add more machines**, or to **remove a broken machine**.

**Key Challenge**: If the set of acceptors changes in instance `i`, all future instances should be executed using the new configuration.

### Simple Solution

- Assume that **proposers and acceptors are also learners**, and that we are running a **slow multi-paxos** where you do not start a new instance until the previous one has finished;
- Reconfiguration can be trivially implemented by using a **special command that defines the new configuration**.

### Not So Simple Solution

- Imagine that you want to run **multi-paxos at full speed**;
- A proposer may propose for instances `i+1` and `i+2`, before learning the value of instance `i` (configuration change);
- This may be confusing, because the proposer may not know the configuration of instance `i+1` and `i+2`.

There are 3 different alternatives to solve this problem:

- **The delayed Stop Sign**;
- **Padding**;
- **The Brick-Wall**.

---

### The delayed Stop Sign

- A reconfiguration command is only **effective** `a` instances after it is accepted;
  - `a` defines the **depth of the consensus pipeline**.
- A node does not propose for instance `i` until it has learned the value of instance `i-a`;
- When a reconfiguration command is proposed, the proposer can immediately propose `null` for the next `a` instances - **stop sign**;
- **Any command decided after the stop command has no effect.**

### Padding

- A proposer proposes a reconfiguration command for instance `i`, and simultaneously proposes `null` for **all instances** after `i`;
- When all future instances have decided null, the state machine has stopped null.

### The Brick-Wall

- A version of Paxos that treats a stop command in a special way: **Stoppable Paxos**;
- The read and write phases explicitly check for a **stop command**;
- There can be at most one stop command accepted in each instance, because **no other command can be accepted after a stop command**;
- When a new leader performs the read phase (prepare), it needs to look for a stop command in all previous instances.

---

---

## [Reconfigurable Registers](https://iditkeidar.com/wp-content/uploads/files/ftp/AKMMS-reconfigure.pdf)

...

---

---

## State Transfer

- When a **new node joins a configuration**, it needs to **obtain the state at the end of the previous configuration** before it starts executing request from the new configuration;
- For instance, in **Paxos**, a new node in a configuration needs to **learn all the commands executed in previous configurations**, before starting to execute commands from the new configuration;
- **State transfer** can be performed by **transferring the log of commands** or by **sending the full state of the application**.
