# [Cloud Computing and Virtualization](https://fenix.tecnico.ulisboa.pt/disciplinas/AVExe23/2023-2024/2-semestre)

## Syllabus

1. Introduction;
   1. [Introduction to Cloud Computing and Virtualization](./01-1-introduction-to-cloud-computing-and-virtualization.md) - cloud computing, virtualization techniques and abstraction levels;
   2. [Core Emulation Techniques](./01-2-core-emulation-techniques.md) - interpretation and translation techniques;
   3. [System Virtualization](./01-3-system-virtualization.md) - ISA virtualizability, memory virtualization, paravirtualization and hardware support;
2. [Infrastructure-as-a-Service](./02-infrastructure-as-a-service.md) - overview of IaaS platforms, SLA, scaling architectures;
   1. [Amazon Web Services](./02-1-amazon-web-services.md) - EC2, CloudWatch, Auto Scaling, Elastic Load Balancing;
   2. [Microsoft Azure](./02-2-microsoft-azure.md) - Fabric Controller, Hyper-V;
   3. [OpenStack](./02-3-openstack.md) - Nova, Neutron, Swift;
3. Platform-as-a-Service;
   1. [High-Level Language VMs](./03-1-high-level-language-vms.md) - architecture overview, JVM, bytecode, security, garbage collection and JIT;
   2. [Cloud PaaS](./03-2-cloud-paas.md) - Google App Engine, Microsoft Azure, messaging services (Amazon SQS, Microsoft Azure Queues, Google Cloud Pub/Sub);
   3. [Function-as-a-Service](./03-3-function-as-a-service.md) - overview, platforms (AWS Lambda, Google Cloud Functions, Azure Functions, IBM OpenWhisk), design and implementation challenges, advanced FaaS apps (ExCamera and Lambada) and FaaS optimizations;
4. [Cloud Storage](./04-cloud-storage.md) - Object Storage (Amazon S3, Azure Blob Storage), File Storage (Google File System, Hadoop Distributed File System), Table Storage (Amazon DynamoDB, Google BigTable), NoSQL (Google Datastore);
5. Big-Data Processing
   1. [MapReduce](./05-big-data-processing-mapreduce.md) - MapReduce programming model, examples (Word Count, Page Rank), MapReduce in Hadoop architecture (master, worker nodes, splits, tasktracker and jobtracker);
   2. [Dataflows and Stream Processing](./05-2-dataflows-and-stream-processing.md) - Spark (RDDs, Spark Streaming), Flink;
6. [Cloud Datacenters Design and Operation](./06-cloud-datacenters-design-and-operation.md).

## Recap

Here are some notes from two courses I took in my bachelor's degree that are related to this course:

- [Systems Virtualization Techniques](https://github.com/andre-j3sus/isel-leic-notes/tree/main/5th-semester/tvs);
- [Cloud Computing](https://github.com/andre-j3sus/isel-leic-notes/tree/main/6th-semester/cn);

## Labs/Tools

1. [Pin](./labs/01-pin.md) - dynamic binary instrumentation tool;
2. [AWS](./labs/02-aws.md) - Amazon Web Services;

---

---

# Key Concepts

## Cloud Computing and Virtualization

### Introduction to Cloud Computing and Virtualization

- **Cloud computing**: computing as an utility, on-demand, pay-as-you-go (small granularity), elastic, scalable, ...;
  - **Deployment models**:
    - **Public cloud**: services are offered over the public internet and available to anyone who wants to purchase them;
    - **Private cloud**: services are maintained on a private network and are protected by a firewall;
    - **Community cloud**: services are shared by several organizations and support a specific community that has shared concerns;
    - **Hybrid cloud**: services are distributed among some combination of private, public, and community cloud;
  - **Advantages**: shared resources, eliminates initial investment, pay-as-you-go (cost reduction), elasticity, portability;
  - **Disadvantages**: availability of service, vendor lock-in, data security, resource management;
- **Virtualization**: abstraction of computer resources, decoupling of software from hardware, multiple OS on the same hardware, ...;
  - Different from **abstraction**, because virtualization does not necessarily hide the underlying details;
  - There is a **virtual guest** and a **real host**;
  - **Computer system interfaces**:
    - **ISA**: instruction set architecture; division between **hardware** and **software**; apps only run on the same ISA - allows for **IaaS**;
    - **ABI**: application binary interface; division between **application** and **OS**; apps only run on the same ISA and OS - allows for **PaaS**;
    - **API**: application programming interface; division between **application** and **library**; apps only run on the same ISA, OS and library - allows for **SaaS**;

### Core Emulation Techniques

- **Emulation** is the process of implementing the interface and functionality of one system on another system - **ISA emulation** is a key component of virtualization;
- **Interpretation** is the simplest form of emulation, where the source code is transformed into an **intermediate form** and executed by the host system;
  - **Decode-and-dispatch interpreter** - for each instruction, fetch, decode and dispatch it to an interpreter routine;
  - **Threaded interpretation** - **avoid inefficient control flow**;
    - **Indirect threaded interpretation** - **reduce number of branches** by duplicating decode-dispatch code, at every instruction;
    - **Basic pre-decoding** - **pre-decode** instructions and **save it** in an intermediate form - dispatch table;
    - **Direct threaded interpretation** - replace instruction code with pointers to interpreter routines;
- **Translation** is a technique to convert the binary code of a program from one ISA to another, instead of having intermediate code - **remove interpretation overhead**;
  - **Simple binary translation** - translate instructions from source to target ISA; using **state mapping** by mapping registers ad memory;
  - **Incremental pre-decoding and translation** - initially interpret and dynamically perform translation; if the instruction is already translated, execute it directly; if not, interpret it and translate it - **translation block**;
  - **Control transfer optimization** - optimize control transfer instructions by **replacing** them with **direct branches** to other translated blocks;

<p align="center">
  <img src="./imgs/predecoding_vs_binary_translation.png" alt="Pre-decoding vs Binary Translation" width="500px">
  <br>
  <em>Pre-decoding vs Binary Translation</em>
</p>

|                              | **Decode-and-Dispatch** | **Indirect Threaded Interpretation** | **Basic Pre-decoding** | **Direct Threaded Interpretation** | **Binary Translation** |
| ---------------------------- | ----------------------- | ------------------------------------ | ---------------------- | ---------------------------------- | ---------------------- |
| **Memory Requirements**      | ✅Low                   | ✅Low                                | ❌High                 | ❌High                             | ❌High                 |
| **Start-up Performance**     | ✅Fast                  | ✅Fast                               | ❌Slow                 | ❌Slow                             | ❌Very Slow            |
| **Steady-state Performance** | ❌Slow                  | ❌Slow                               | ✅Medium               | ✅Medium                           | ✅Fast                 |
| **Code Portability**         | ✅High                  | ✅High                               | ✅High                 | ✅Medium                           | ❌Low                  |

### System Virtualization

- **VMM** (Virtual Machine Monitor) - software layer that abstracts the hardware and provides a virtual machine interface - **schedule** and **manage** allocation of resources for VMs;
  - Point of **control** for **shared physical resources**; - **approach is to intercept all privileged instructions and all accesses to privileged resources**;
  - **Type 1** - runs directly on the hardware, with no OS;
  - **Type 2** - runs on a host OS;
- **CPU Virtualization** - only **VMM runs on system mode**, **guest OS runs on user mode**;

  - Core emulation techniques to **virtualize** the **ISA**;
    - **Well behaved ISA** - **efficiently virtualizable**; trap occurs naturally when emulation is needed, handler jumps to appropriate interpreter routine;
    - **Ill behaved ISA** - **not efficiently virtualizable**; trap does not occur naturally, need of **code discovery**;
  - Instructions:
    - **Privileged instructions** - **trap-and-emulate** if user mode, does not trap in system mode - all guest-OS is forced to run in user mode and trap;
    - **Not privileged instructions** - do not trap;
    - **Sensitive instructions** - interact with HW; **control sensitive** - change the config of the system; **behavior sensitive** - change the behavior of the system;
    - **Innocuous instructions** - neither control nor behavior sensitive;
    - **Critical instructions** - **sensitive instructions** that are **not privileged**
  - **Theorem 1** - **efficient VMM construction**: **sensitive instructions is a subset of privileged instructions**; there can be **any number of innocuous instructions**; there must not be **any critical instructions**.
    - However, its still possible to virtualize the system with critical instructions: **hybrid** approach - some instructions must be specially emulated, using **binary translation**;

- **Memory virtualization**

---

## Infrastructure-as-a-Service
