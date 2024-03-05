# Dependability Fundamentals

> A system is **dependable** if it is able to deliver service that can justifiably be **trusted**.

* Dependability converges with **reliability**, **availability**, **safety**, and **security**;
  * Non-malicious faults are only a subset of all possible faults;
  * Security research focused on confidentiality, but needed to pay more attention to availability and integrity.

Dependability concepts:

### Attributes

* **Availability**: readiness for correct service;
* **Reliability**: continuity of correct service;
* **Safety**: absence of catastrophic consequences on the user(s) and the environment;
* **Confidentiality**: absence of unauthorized disclosure of information;
* **Integrity**: absence of improper system state alterations;
* **Maintainability**: ability to undergo modifications and repairs.

### Threats

* **Faults**: defects in the system;
* **Errors**: incorrect internal state that may lead to service failure;
* **Failures**: departure from correct service.

```plaintext
Faults --activation--> Errors --propagation--> Failures
```

### Means

* **Fault prevention**: avoiding the occurrence of faults;
* **Fault removal**: removing the faults that are present;
* **Fault forecasting**: estimating the expected frequency of faults;
* **Fault tolerance**: masking the effects of faults.

---

## Blockchain

* **Blockchain**: distributed ledger that records transactions across many computers;
* **Append-only** sequence of blocks;
* **Tamper-proof** (immutable) blocks agreed upon by **consensus**;
* Widely known for being the technology behind Bitcoin, but can be used for many other applications.
* **BFT (Byzantine Fault Tolerance)** consensus, running in a **closed membership** network.

---

## Cryptography 

* **Information**: ordered collection of binary symbols, with a meaning;
* **Entity**: party participating in a protocol;
* **Attacker**: entity that tries to violate the security of the system;
* **Cryptographic key**: parameter used in cryptographic algorithms;

Some cryptographic primitives:

* **Symmetric encryption**: same key for encryption and decryption;
  * Key known only to the communicating parties and not sent over the network;
  * **Symmetric cipher** and **Message Authentication Code (MAC)**;
  * **Message authentication codes**: cryptographic checksum that allows the receiver to verify the integrity of the message;
* **Block ciphers and padding**: fixed-size input to fixed-size output;
  * AES, DES, 3DES, CTR, CBC, ECB, etc.;
* **Hash functions**: fixed-size output from variable-size input; it is infeasible to find two inputs that hash to the same output;
* **Asymmetric encryption**: different keys for encryption and decryption;
  * Key pairs: public and private - public key is known to everyone, private key is known only to the owner;
  * **Asymmetric cipher** and **Digital Signature Algorithm (DSA)**;
  * **Digital signatures**: cryptographic scheme that allows a party to sign a message.

> To check more about cryptography and computer security, see the [Computer Security](https://github.com/andre-j3sus/isel-leic-notes/tree/main/5th-semester/seginf) notes that I wrote during my bachelor's degree.