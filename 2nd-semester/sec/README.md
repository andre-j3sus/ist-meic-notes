# [Highly Dependable Systems](https://fenix.tecnico.ulisboa.pt/disciplinas/SDTF23/2023-2024/2-semestre)

## Syllabus

1. [Dependability Fundamentals](01-dependability-fundamentals.md) - main concepts, notion of blockchain, and a refresher on security and cryptography;
2. [Basic Abstractions](02-basic-abstractions.md) - processes, communication, and consensus;
3. [Broadcast Primitives](03-broadcast-primitives.md) - best-effort, reliable, byzantine reliable broadcast;
4. [Read/Write Protocols](04-read-write-protocols.md) - atomic and regular registers;
5. [Consensus Protocols](05-consensus-protocols.md) - paxos (crash model) and byzantine fault-tolerant consensus;
   1. [Istanbul BFT](05.1-istanbul-bft.md) - byzantine fault-tolerant consensus in practice;
6. [Blockchain Fundamentals](06-blockchain-fundamentals.md) - distributed ledgers, PoW, and PoS;
   1. [Bitcoin](06.1-bitcoin.md) - PoW, transactions, and blocks;
   2. [Ethereum](06.2-ethereum.md) - PoS, account model, gas, EVM;
   3. [Solidity](06.3-solidity.md) - smart contracts;
7. [Trusted Hardware](07-trusted-hardware.md) - smart cards, TPMs, SGX, TEEs and TDEs;

---

## Key Concepts

### Dependability and Security Fundamentals

* **Dependability**: a service is dependable if it is able to work in a way that is justifiably trusted;
  * Attributes:
    * **Availability** - readiness for correct service - **five nines** - 99.999% availability means 5 minutes of downtime per year - **high availability**;
    * **Reliability** - continuity of correct service;
    * **Safety** - absence of catastrophic consequences for the system;
    * **Confidentiality** - no unauthorized third party observes information;
    * **Integrity** - absence of improper system modifications;
    * **Maintainability** - ability to undergo modifications/repairs;
  * Threats:
    * **Fault** - hypothesized cause of an error; e.g. bug in the code;
    * **Error** - activated by a fault, and if propagates it can lead to a system failure; e.g. code with bug executed;
    * **Failure** - service deviates from correct service; e.g. node crashed;
  * Means:
    * **Fault prevention/avoidance** - prevent occurrence of faults;
    * **Fault prediction/forecasting** - predict components that can originate faults;
    * **Fault removal** - reduce number and severity of faults - testing, verification, fault injection, ecc;
    * **Fault treatment** - avoid service failures in the presence of faults.

* **Cryptographic primitives**:
  * **Symmetric primitives** - symmetric cipher (confidentiality) and **MACs** (integrity and authenticity);
    * Use of a symmetric/secret key;
  * **Asymmetric primitives** - asymmetric cipher (confidentiality) and **digital signatures** (more costly, but more safe - also ensure **non-repudiation**; also can be verified by third parties);
    * Need of a PKI, with public and private keys;
    * Nowadays, signatures are much faster than in 1999 due to **faster processors** and the use of methods like **elliptic curves**;
  * **Hash**:
    * **One-way** - its computational infeasible to revert an hash;
    * **Collision resistant** - its computation infeasible to find a pair of different plain texts with the same hash. 

> **Safety** - nothing bad should happen; if a **finite trace** does not obey the property, than **no extension** of that will obey;
>
> **Liveness** - something good will eventually happen; if a finite trace does not obey the property, than it can be **extended** in order to obey it.

### Basic Abstractions

* **Fair loss links**:
  * **FLL1 - Fair loss**: if a message is sent infinitely often by a correct process pi to a correct process pj, then pj will infinitely deliver the message - liveness;
  * **FLL2 - Finite duplication**: if a message is sent a finite number of times from a correct process pi to pj, then pj delivers it a finite number of times - safety;
  * **FLL3 - No creation**: no message is delivered unless it was sent - safety;
* **Stubborn links**: add while true in the send, to send the message infinitely;
  * **SL1 - Stubborn delivery**: if a message is sent by a correct process pi to a correct process pj, then it is delivered by pj an infinite number of times - liveness;
  * **SL2 - No creation**;
* **Perfect links**: add delivery set, and only deliver once;
  * **PL1 - Validity**: If a correct process pi sends a message to a correct process pj, then the message is eventually delivered by pj - liveness;
  * **PL2 - No duplication**: No message is delivered by the same process more than once - safety;
  * **PL3 - No creation**;
* **Authenticated Perfect Links**: use MACs to sign the message in the send, and verify in the deliver;
  * **APL 1 to APL 3 are the same to PL 1 to PL3**;
  * **APL 4 - Authenticity**: if a correct process pj delivers a message from a correct process pi, then the message it was sent from pi to pj.

* **Perfect Failure Detector**:
  * **Strong Completeness** - eventually, every process that crashes is permanently suspected by every correct process;
  * **Strong Accuracy** - no correct process is suspected (no process is suspected unless it has crashed);
* **Eventually Perfect Failure Detector**:
  * **Strong Completeness**;
  * **Eventual Strong Accuracy** - eventually no correct process is suspected;
  * Implemented with heartbeats and timeouts, in a **eventually synchronous system**;
  * Used in broadcasts or consensus protocols.

* **Leader Election**:  
  * **Eventual detection** - either there is no correct process, or eventually some correct process is elected as leader;
  * **Accuracy** - if a process is leader, then all previous leaders are faulty;
* **Eventual Leader Election**:
  * **Eventual accuracy** - there is a time after which every correct process trusts some correct process;
  * **Eventual agreement** - if a correct process trusts a correct process, then every correct process trusts the same process;
* **Byzantine Leader Election** - processes **complain** about the leader, and if more than f processes complain, then the leader is suspected;
  * **Eventual succession** - if more thant f processes suspect a process, then eventually every correct process suspects it;
  * **Putsch resistance** - a correct process does not trust a leader unless at least one correct process has complained about the previous leader;
  * **Eventual agreement** - if a correct process trusts a correct process, then every correct process trusts the same process;

### Broadcast Primitives

* **Best-effort Broadcast** - uses **perfect links** and inherits the properties;
* **Reliable Broadcast**:
  * **RB1 to RB3 are the same to BEB1 to BEB3**;
  * **RB4 - Agreement**: if a correct process delivers a message m, then every correct process delivers m;
  * **Eager Reliable Broadcast** - uses **BEB** and when deliver is called, if the message is not in the delivered set, then deliver it and broadcast again;
* **Byzantine Consistent Broadcast**:
  * If sender is correct, then every correct process delivers the message sent;
  * If sender is faulty, then every correct process delivers the **same** message, if they deliver any;
  * **BCB1 - Validity**;
  * **BCB2 - No duplication**;
  * **BCB3 - Integrity** - if a correct process delivers a message m with sender p, and p is correct, then m was previously broadcast by p;
  * **BCB4 - Consistency** - if a correct process delivers a message m, then every correct process delivers m;
  * Uses **byzantine quorums** $Q = 2f + 1$, or generically for $n > 3f$, $Q = \frac{n+f}{2}$;
  * **Authenticated Echo Broadcast** - **quadratic** number of messages;
    * 1st round - sender broadcasts the message;
    * 2nd round - every process resends m in an **ECHO** message - deliver only when received a quorum of ECHO messages;
  * **Signed Echo Broadcast** - **linear** number of messages;
    * Uses **digital signatures** - more powerful than MACs, because a third party can verify the signature;
    * 1st round - sender broadcasts the message with its signature;
    * 2nd round - every process **signs** a statement in the message and sends it back to the sender - when the sender receives a quorum of signatures, it broadcasts the message with the quorum of signatures;
* **Byzantine Reliable Broadcast**:
  * If a correct process delivers a message m, then every correct process delivers m, independently of the sender being correct or faulty;
  * **BRB1 to BRB4 are the same to BCB1 to BCB4**;
  * **BRB5 - Totality** - if some message is delivered by a correct process, then every correct process delivers **some message** - **Totality + Consistency = Agreement**;
  * **Double Authenticated Echo Broadcast** - **cubic** number of messages;
    * 1st round - sender broadcasts the message;
    * 2nd round - every process broadcasts an **ECHO** message to all processes;
    * 3rd round - when an achieves a **quorum** of ECHO messages, it broadcasts a **READY** message - deliver only when received a **quorum** of READY messages;
    * **Amplification** - when receiving **f+1** READY messages, the process broadcasts a **READY** message - needed to ensure **totality**;

> **Regular quorums** intersect in at least one replica ($n/2 + 1$), and **Byzantine quorums** intersect in at least one correct replica ($2f + 1$). $2Q -N \geq f + 1$ and $Q <= N - f$.

### Read/Write 

* **Regular register**:
  * **Liveness** - if a correct process invokes an operation, then the operation eventually completes;
  * **Safety** - reads must return the last value written, if there is no concurrent write; or the last value or any value concurrently written;
  * Uses Best-effort Broadcast and perfect links;
* **Atomic register**:
  * **Linearizability**;
  * Any operation has a **serialization point** - the point in time when the operation is considered to be completed;
  * Reads return the value of the last write operation (serialization point) that precedes its own serialization point;
* **Byzantine regular register** - use perfect authenticated links, byzantine quorums, and when a client writes, it piggybacks a **signature**, and when reading, a client discards incorrectly signed values (no message forgery);

### Consensus Protocols

* Uniform Consensus - in crash model - **Paxos** implements this;
  * **Termination** - correct processes eventually decide;
  * **Validity** - any decided value was proposed by some process;
  * **Integrity** - no process decides twice;
  * **Agreement** - no two correct processes decide differently;
* **Byzantine Fault Tolerant Consensus**:
  * **Weak Byzantine Consensus** - **weak validity** - if all processes are correct, and some process decides v, then v was proposed by some process;
    * If some processes are faulty, any value can be decided;
  * **Strong Byzantine Consensus** - **strong validity** - if all correct processes propose the same value, then no correct process delivers other value; in the presence of faulty processes, a correct process decides the value proposed by some correct process, or a special value **⊥**;
  * **IBFT** is a practical implementation of Byzantine Fault Tolerant Consensus;

### Blockchain Fundamentals

* **Distributed Ledger** - a database that is shared and synchronized across multiple sites, institutions, or geographies, accessible by multiple people or parties;
* **Bitcoin**:
  * Uses **PoW** - **Proof of Work** - to add a block to the blockchain, a miner must solve a cryptographic puzzle, that is computationally expensive - miner that solves it first, adds the block, and receives a **reward**;
    * The miner tries to solve `SHA256(previousBlochHash||digestTxs||pubKey||nonce) < D`, and to adjust the difficulty, the network changes the value of D;
  * **Block** contains a list of transactions, a reference to the previous block, and a **nonce**;
  * Properties of PoW:
    * **Tamperproof** - changing a block requires changing all subsequent blocks, which is computationally expensive;
    * **Incentive** - miners are rewarded for adding blocks;
    * **Prevents Sybil attacks** - a single entity cannot control the network with more than 50% of the computational power, because it would be too expensive;
  * A block is considered **confirmed** after 6 blocks are added on top of it - decreasing the probability of the suffix chain being replaced;
  * **Mining pools** - miners join forces to increase the probability of solving the puzzle, and share the reward;
    * **Feather forking attack** - mining pool can censor transactions, and then add a block with the censored transactions to the blockchain - to prevent this, the network should have a **decentralized mining pool**;
  * A block is considered finalized if its **deep enough** in the blockchain, and the probability of being replaced is very low - however, this has latency;
  * **Double spending attack** - an attacker sends a transaction to a merchant, and at the same time sends the same transaction to another address controlled by the attacker - the attacker can control the network to add the block with the transaction to the merchant, and then add a block with the transaction to the attacker's address - to prevent this, the merchant should wait for the transaction to be confirmed, and the attacker should control more than 50% of the computational power;
  * **Selfish mining** - a miner can keep a block secret, and when another miner finds a block, the selfish miner can publish its block, and the network will choose the longest chain;
  * **UTXO model** - Unspent Transaction Output - a transaction is a list of inputs and outputs, and the inputs are references to previous outputs; **keep track of unspent outputs**;
    * Is not scalable, because the number of UTXOs grows with the number of transactions - **Utreexo** is a solution to this problem - **merkle tree** - kept by **full nodes** , and **compact state nodes** only keep the **root** of the tree;

> #### Consensus vs PoW
>
> * **Consensus** focus more on **safety**;
> * **PoW** focus more on **liveness**:
>   * **✅ Termination** - eventually a block will be added;
>   * **❌ Integrity** - a block can be decided twice, because can change due to a fork;
>   * **❌ Agreement** - due to forks, different nodes can have different views of the blockchain;
>   * **❌ Validity** - a block can be added without being valid, because the PoW is not related to the block content - **only weak validity is guaranteed**.


* **Ethereum**:
  * Uses **PoS** - **Proof of Stake** - to add a block to the blockchain, a validator must have a stake in the network, and the probability of adding a block is proportional to the stake - **stake is the non-counterfeitable resource**;
  * **Smart Contracts** - self-executing contracts with the terms of the agreement between buyer and seller being directly written into lines of code - **turing complete**;
  * **Account model** - instead of UTXO, Ethereum uses accounts;
    * **Externally Owned Accounts** - controlled by private keys, and can send transactions;
    * **Contract Accounts** - controlled by code, and can send transactions;
  * **Gas** - the cost of executing a transaction;
    * If gas runs out, the transaction is reverted and the gas is **not refunded**;
    * If there is leftover gas, it is **refunded**;
  * **EVM** - Ethereum Virtual Machine - a Turing complete machine that executes smart contracts;
  * **Solidity** - a high-level language to write smart contracts;
  * **Reentrancy attack** - an attacker can call a contract that calls back the attacker's contract, and the attacker can call the contract again, and so on - to prevent this, use **non-reentrant locks**;

### Trusted Hardware

* **Smart Cards** - a secure and tamper-resistant device that can store and process information; 
  * **ROM** - card OS;
  * **RAM** - volatile memory;
  * **EEPROM** - **cryptographic keys, PINs, biometric data, balance, etc** - non-volatile memory that is mostly read-only, only can be written a few times;
  * **Side-channel attacks** - attacker can measure power consumption, heat, etc, to extract information - **power analysis attack** consists of measuring the power consumption of the card while executing a cryptographic operation, and with that, the attacker can extract the key - a **countermeasure** is to add random artificial noise;
* **TPMs** - Trusted Platform Module - a secure **crypto-processor** that can store cryptographic keys and perform cryptographic operations - **ensure that machine is in a trusted state**;
  * Used to **secure boot** - ensure that the machine boots from a trusted source;
  * **PCR** - Platform Configuration Registers - a set of registers that store the hash of the boot process;
  * Criticized for owner losing control over the machine;
* **TEE** - Trusted Execution Environment - a secure area of the main processor that can run code in isolation;
  * Allow for strong **confidentiality and integrity** guarantees;
  * **SGX Enclaves** - Software Guard Extensions - a secure area of the main memory that can store code in **isolation** (data and code cannot be seen or modified by the OS or other applications), **attestation** (means to prove that the code is running in an enclave), and **sealing** (encrypt data with the enclave's key, and only the enclave can decrypt it);
  * **Replay attack** - is a vulnerability in SGX data sealing, because an attacker can change the sealed data to a previous state, and the enclave will decrypt it, and the application will use the old data;

_Remote attestation is a mechanism in which a host authenticates it's hardware/software configuration to a remote host._