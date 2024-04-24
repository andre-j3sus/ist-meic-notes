# Introduction to Cloud Computing and Virtualization

## Cloud Computing

* **Solution to really large scale issues**;
* Highly efficient scalable architectures;
* **On-demand self-service**;
* Computing as a **utility** (like electricity);
  * Reduce CAPEX (Capital Expenditure) and OPEX (Operational Expenditure);
  * Pay-as-you-go - small granularity payments;
* **Elasticity** - ability of dynamically acquiring computing resources;
* Service providers ensure **maintenance, security, and availability**.

### Delivery models

* **Infrastructure as a Service (IaaS)** - rent virtual machines - **System-level Virtualization**;
  * User controls the **OS**, storage, network, applications;
  * User does not control the underlying infrastructure;
  * e.g. Amazon EC2;
* **Platform as a Service (PaaS)** - rent a platform - **Application-level Virtualization**;
  * User does not control the underlying infrastructure;
  * User only controls deployed apps and configuration settings;
  * e.g. Google App Engine (Java, Python apps);
* **Software as a Service (SaaS)** - rent software - **User-level Virtualization**;
  * Only runs applications developed by the provider;
  * e.g. Google Docs, Office 365.

<p align="center">
  <img src="https://kinsta.com/wp-content/uploads/2022/06/1.-SaaS-vs-IaaS-vs-PaaS.png" alt="Cloud Computing Delivery Models" width="500">
</p>

### Deployment models

* **Public Cloud** - available to the general public;
* **Private Cloud** - internal to an organization;
* **Community Cloud** - shared by several organizations;
* **Hybrid Cloud** - combination of the above.

### Advantages

* Resources are **shared**;
* Resources can be **aggregated**;
* Data sharing facilitates collaborative activities;
* Eliminates **initial investment**;
* Cost reduction;
* **Elasticity**;
* **Virtualization** allows for user convenience.

### Challenges

* Availability of services;
* Vendor lock-in (interoperability);
* Security and privacy;
* Data transfer bottlenecks;
* Performance unpredictability;
* Resource management.

---

## Virtualization

* **Portability**, flexibility, reliability, manageability;
* **Isolation**;
* Several levels of virtualization:
  * **Virtual Memory** - memory abstraction; relocation, protection, sharing;
  * **System Virtual Machines** - full virtualization; e.g. VMware, VirtualBox;
    * **Type 1** - hypervisor runs directly on hardware;
    * **Type 2** - hypervisor runs on a host OS;
  * **Process Virtual Machines** - e.g. Java Virtual Machine, .NET CLR;
    * Garbage collection, JIT compilation, sandboxing, interoperability;
  * **Application Virtualization** - e.g. Docker, App-V;
    * Isolation, portability, compatibility, resource management.
  * **Virtual Private Networks** - e.g. OpenVPN, IPsec;
    * Secure communication over public networks.

### Abstraction and Interfaces

* **Abstraction** - hides details;
* **Interface** - defines how to interact with the abstraction;
  * ✅ Decouples the interface from the implementation;
  * ✅ Vendor-independent interfaces;
  * ❌ May reduces interoperability;
  * ❌ Very restrictive;
* The solution is to keep the advantages and avoid the disadvantages: **virtualization** - mapping of interfaces to implementations;
  * **Isomorphism** - same interface, different implementations;
  * Virtual **guest** and real **host**;
* **Computer System Interfaces**:
  * **Instruction Set Architecture (ISA)** - interface between hardware and software;
    * Components: CPU instructions, registers, memory, I/O;
  * **Application Binary Interface (ABI)** - interface between application and OS;
    * Components: system calls, libraries, data types;
  * **API** - interface between application and libraries;
    * Components: functions, data types, constants. 

### Types of Virtual Machines

* **Process VMs** - e.g. Java Virtual Machine;
  * **JIT Compilation** - Java bytecode to machine code;
  * **Garbage Collection** - automatic memory management;
  * **Sandboxing** - restricts the actions of the code;
  * **Interoperability** - e.g. JNI (Java Native Interface).
* **System VMs** - e.g. VMware, VirtualBox;
  * **Type 1** - hypervisor runs directly on hardware;
  * **Type 2** - hypervisor runs on a host OS;
  * **Full Virtualization** - complete simulation of hardware;
  * **Paravirtualization** - modified guest OS;
