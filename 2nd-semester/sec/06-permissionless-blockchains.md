# Permissionless Blockchains

### Distributed Ledgers

* A **ledger** is a record of transactions;
  * **Tamper-proof** - once a transaction is recorded, it cannot be changed;
  * **Ordered** - transactions are recorded in a specific order.
* A **distributed ledger** is a ledger that is shared across multiple participants;
  * **Permissioned** - fixed set of participants;
  * But in an open internet environment, membership is open and dynamic - **permissionless**.

### Blockchain

* A **blockchain** is a list of events, or transactions, that were recorded in a distributed ledger. 
* **Append-only** and **tamper-proof**;
* In a permissionless blockchain, the participants are anonymous and the blockchain is maintained by a **consensus protocol** - considering that the number of attackers do not exceed 1/3 of the network;
* **Sybil Attack** - an attacker creates multiple identities to control the network.
  * Entities != identities;
  * It is easy to create multiple public/private key pairs or IP addresses.

---

## Protocols for Permissionless Blockchains

### Proof-of-Work (PoW)

* Proof-of-Work is a process that requires **some work to be done** before a block can be added to the blockchain;
* The work is **computationally intensive** and **time-consuming**;
* The work is **easy to verify**;
* This limits the rate at which blocks can be added to the blockchain, and thus the rate at which new coins can be created;
* In bitcoin, **miners** compete to solve a cryptographic puzzle, they aggregate transactions in blocks, allowing for better throughput;
  * They compete to get block appended to the blockchain, and they are rewarded with new coins.

### Proof-of-Stake (PoS)

* **Stake** - the amount of coins a participant has;
* Use stake in the system as a measure of influence;
* The more stake a participant has, the more likely it is to be chosen to create a new block;
* Problems:
  * Rich get richer;
  * Proved to converge only under certain conditions.

### Proof-of-Space (PoSpace)

* Use the amount of space a participant has as a measure of influence;
* The more space a participant has, the more likely it is to be chosen to create a new block;
* Problems:
  * Requires additional techniques - small PoW, PoET, etc.

### Proof-of-Elapsed-Time (PoET)

* Relies on a trusted execution environment (TEE) to elect a leader that will choose the next block;
* The leader is chosen randomly, but the TEE ensures that the leader is chosen fairly;
* Problems:
  * Needs to rely on trusted hardware.

### Committee-based Consensus

* A committee of nodes is chosen to create a new block;
* The committee relies on classical consensus to agree on the next block;
* Problems:
  * Committee members can behave arbitrarily and are prone to attacks.
