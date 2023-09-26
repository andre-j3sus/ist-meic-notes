# Coordination Services

> _**Coordination services** are a class of distributed systems designed to help applications that want to **avoid implementing Paxos** or other consensus protocols directly. They provide a simple API that allows applications to implement higher-level abstractions such as locks, barriers, and leader election. It must be **highly available** and **fault-tolerant**._

## [Chubby](https://static.googleusercontent.com/media/research.google.com/en//archive/chubby-osdi06.pdf) (Google)

* Small **file system** and **lock service** built on top of Paxos;
* State is kept by a set of replicas to ensure **fault-tolerance**;
* State-updates are performed using **Paxos**, to ensure **consistency**;
* Distributed applications can use Chubby to **coordinate** their activities.

### Main goals

* Act as a lock service;
* Can be used to **elect a master**;
* Replicas and clients can be notified when the master changes;
* Based on the notion of **lease** - a period of time after which a primary must "give up" unless it is able to renew the lease.

### Paxos Implementation

...

### Locks

...

### Dealing with Faulty Clients

...

### Client Caching

...

---

## [Zookeeper](https://www.usenix.org/legacy/event/usenix10/tech/full_papers/Hunt.pdf) (Yahoo!)

...

