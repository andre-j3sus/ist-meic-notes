# Introduction to Distributed Systems

> A **distributed system** is a set of autonomous computers (nodes). Each computer is equipped with an **OS** and a **middleware**.

> A **middleware** is a software layer between the OS and the applications. It provides a set of services that simplify the development of distributed applications, including: naming, resource location, remote invocation, consistency, messaging protocols, etc.

### Distributed Systems History

|                   | Early          | Internet-scale       | Contemporary                                                      |
| ----------------- | -------------- | -------------------- | ----------------------------------------------------------------- |
| **Scale**         | Small          | Large                | Ultra-large                                                       |
| **Heterogeneity** | Homogeneous    | Heterogeneous        | Ultra-heterogeneous (radically different styles of architectures) |
| **Openness**      | Not a priority | Open                 | Open                                                              |
| **QoS**           | Not a priority | Significant priority | Critical                                                          |

---

## Centralized vs Distributed Systems

| **Centralized Systems**           | **Distributed Systems**                                       |
| --------------------------------- | ------------------------------------------------------------- |
| Single instance of each component | Multiple autonomous components                                |
| Shared resource for all users     | Not all components are shared                                 |
| Single point of control/failure   | Some components can fail - multiple points of control/failure |
| Scale up (vertical)               | Scale out (horizontal) - higher scalability                   |

---

## Middleware

> A **middleware** is a software layer between the OS and the applications. It provides a set of services that simplify the development of distributed applications, including: naming, resource location, remote invocation, consistency, messaging protocols, etc.

* Applications should interact **transparently** despite their location/distribution;
* Applications should be **independent** from low-level communication details;
* Applications should be more **robust** and **available**;
* Application service should **scale** with the number of clients/requests.

The middleware provides a **much higher abstraction** level than the OS, allowing the development of distributed applications to be much simpler.

### Basic Middleware Services

* Identification/location and loading of **code**;
* Identification/location of **remote objects** and handling of their **references**;
* Remote object **invocation**;
* Notification of **network failures**;

---
---

## Application Model

> Distributed applications can be developed using **various models/patterns**.

The best fitting model depends on several aspects, like:

* Expected load/desired performance;
* Quantity of data to be transmitted over the network;
* Security required for the correct functioning of the application;
* Functionalities supported by the middleware;
* Practical aspects related with specific computing platforms.

#### Models

* Remote Procedure Call (RPC);
* Local mapping of data;
* Local mapping of code;
* Local mapping of mobile code + data;
* Publish/Subscribe.

---

### Remote Procedure Call (RPC)

> The **RPC** model is based on the **client-server** model. The client invokes a procedure on the server, which returns the result to the client.

* The middleware (web services, RMI, etc.) is fully responsible for the execution of the remote procedure calls;
* Also known as **function-shipping**.

---

### Local Mapping of Data

* Assumes existence of a client process that **access data remotely**, that was **previously mapped locally**;
* The client middleware requests data to the corresponding server;
* Server responds with a message that contains the requested data;
* Also known as **data-shipping**.

---

### Local Mapping of Code

* Analogous to the previous model, but **part of the application code is physically maintained at a remote server**, and is mapped (or loaded) locally at the client side;
* Used, for instance, by **Java applets**;
* Also known as **code-shipping**.

> **Applet** is a code fragment that is transferred via network from a computer to another one, in which it is executed.

---

### Local Mapping of Mobile Code + Data

* Analogous to former, but part of the code of an application *travels* across multiple computers in which it is executed;
* An applet can travel only between two computers;
* An agent can travel between multiple computers.

> **Agent** is a code fragment that an be mapped subsequently in multiple computers, and act as either a client of a server.

---

### Publish/Subscribe

* A **subscriber** registers its interest in a specific event;
* A **publisher** publishes an event;

---
---

## Architectural Styles

Distributed applications can adopt various architectures:

* Main-frame;
* Client-Server;
* Client-Server with replicated servers;
* Multi-tiered architectures (e.g. tree-tiered);
* Forward/Reverse Proxy;
* Peer-to-Peer.

---

### Three Tier Client-Server Architecture

* Clear separation of concerns in application logic;
* **1st Level**: Presentation layer - UI;
* **2nd Level**: Application logic, business rules, transactional services;
* **3rd Level**: Data layer - persistence information repository.

This architecture is **very common** in web applications:
* Communication between first and second level is done using **HTTP**;
* Communication between second and third level is done using **SQL**.

---

### Web Proxies

> A **proxy** is a computer that acts as an intermediary between a client and a server.

* **Forward Proxy**: acts on behalf of a client;
* **Reverse Proxy**: acts on behalf of a server.

---

### Peer-to-Peer Architecture

> A **peer-to-peer** architecture is a distributed architecture in which all nodes have the same capabilities and responsibilities.

Every node plays:

* **Client** role: access remote data and functionalities;
* **Server** role: provide data and functionalities to other nodes;
* **Router** role: message forwarding and maintenance of topology and structure of the network.

---
---

## Communication Models

### Synchronous Distributed Systems

* Execution time of each step in a process has lower and upper bounds;
* Each message transmitted in the network is delivered within a maximum time limit;
* The local clock at each process drifts from the real time clock at a speed whose limit is known.

### Asynchronous Distributed Systems

* Difference among the execution speeds of the processes is not known;
* Delays in message transmission are not known;
* The local clock at each process drifts from the real time clock at an unknown speed.

> Asynchronous systems are more **abstract** and **general**.

---

### Performance Metrics

* **Latency**: time between the transmission of a message and its reception;
* **Throughput**: number of packets delivered per unit of time;
* **Bandwidth**: amount of information transmitted per unit of time;
* **Delay Jitter**: variation of latency over time.

---
---

## Failure Models

> A **failure** is the inability of a system or a component to perform its required functions within specified performance requirements.

| Class of Failure          | Affects         | Description                                                                                      |
| ------------------------- | --------------- | ------------------------------------------------------------------------------------------------ |
| **Fail-Stop**             | Process         | Process stops. Other processes may detect it.                                                    |
| **Crash**                 | Process         | Process stops. Other processes may not detect it.                                                |
| **Omission**              | Channel         | Process fails to send a message.                                                                 |
| **Arbitrary (Byzantine)** | Process/Channel | Process/channel behaves arbitrarily: it may send/transmit arbitrary messages at arbitrary times. |



### Omission Failures

* **Process** omission failures - process crashes;
  * Detected with **timeout**;
  * Crash is **fail-stop** if other processes can detect it;
* **Communication** omission failures - message loss;
  * Detected with **acknowledgements**;
  * **Byzantine** failures: messages are corrupted.

---

### Arbitrary (Byzantine) Failures

* **Process** - omit intended processing steps or carry out unwanted ones;
* **Communication** - messages are corrupted, duplicated or not delivered.

---

### Timing Failures

* Applicable in **synchronous** systems;
* Responses may be delayed or not delivered.

---

### Masking Failures

* **Failures** can be **masked** by the middleware;
* **Masking** is the process of hiding failures from the application;
* **Masking** is achieved by **replication**.

---

### Reliability

> **Reliability** is defined in terms of **validity** and **integrity**.

* **Validity**: any message is eventually **delivered** to the destination;
* **Integrity**: the received message is **identical** to the sent message, and is **received only once**.
