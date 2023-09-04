# Group Communication

## Indirect Communication

> Communication between entities in a distributed system through an **intermediary**.

* No direct coupling between sender and receiver;
* Examples:
  * Group communication;
  * Publish/subscribe systems;
  * Message queues;
  * Shared memory.

### Uncoupling

* **Space uncoupling**
  * Sender **do not need to know the identity** of the receiver, and vice-versa;
  * Adds **freedom** to the system: participants may be replaced, updated, replicated, etc.

* **Time uncoupling**
  * Sender and receiver do not need to be **active at the same time**;
  * Tolerates **volatile environments**: participants come, go, fail, etc;
  * **Persistence** of the communication channel.

---

## Replication <!--Improve this-->

> Replication is the process of creating and maintaining **copies** of a resource in order to **improve reliability** and **performance**.

Replication can provide the following benefits:

* **Performance enhancement**;
* **Fault tolerance** - avoid single point of failure;
* **Increased availability**. 

---
---

## Group Communication

> Group communication is a **communication paradigm** in which a group of processes can **communicate** with each other - **multicast** - through a **group communication system**.

...
Processes and groups
Group membership services
JGroups toolkit example
Views and view properties
View synchrony