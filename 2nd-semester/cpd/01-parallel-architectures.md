# Parallel Architectures

## Computer Architecture Review

> **Computer Architecture** is the **engineering** of a **computer system** through the careful design of its **organization** and **control structure**.

* An **high-level** language program (e.g., C, C++, Java) is **compiled** **assembly** language, and then **assembled** into **binary** code;
* The programming model for each processor is defined by a set of assembly instructions and a set of registers - the **ISA (Instruction Set Architecture)**;
* Instruction execution can be divided in stages:
  * **Instruction Fetch** - Fetch the instruction from **memory**;
  * **Operand Fetch** - Fetch the operands from **memory**;
  * **Execution** - Execute the instruction;
  * **Write Back** - Write the result to **memory**;
* Each instruction can make more than 3 memory accesses;

### Memory

* Memory should be **fast**, **large**, and **cheap**;
  * Solution: **Memory Hierarchy** - A set of storage devices with different capacities, costs, and access times;

| Memory Level    | Access Time | Capacity |
| --------------- | ----------- | -------- | 
| **Registers**   | < 1 ns      | 1 KB     | 
| **Cache**       | 2 ns        | 1 MB     |
| **Main Memory** | 10 ns       | 1 GB     |
| **Disk**        | 10 ms       | 1 TB     | 

* **Registers** - Fastest, smallest, and most expensive;
  * Store the **operands** and **results** of the instructions;
* **Cache** - Faster, smaller, and more expensive;
  * Store the most frequently used data and instructions;
* **Main Memory** - Slower, larger, and cheaper;
  * Store the data and instructions that are not in the cache;
* **Disk** - Slowest, largest, and cheapest;
  * Store the data and instructions that are not in the main memory;

> **Principle of Locality**: Programs tend to access a small portion of the address space at any instant of time.
>
> * **Temporal Locality**: If an item is referenced, it will tend to be referenced again soon;
>   * Use **cache** for **instructions** and **data** that are **referenced** frequently;
>   * Use **virtual memory** to keep the **most frequently used** data and instructions in **main memory**; 
>
> * **Spatial Locality**: If an item is referenced, items whose addresses are close by will tend to be referenced soon;
>  * Use **cache** for **instructions** and **data** that are **referenced** frequently;
> * Use **virtual memory** to keep the **most frequently used** data and instructions in **main memory**.

---
---

## MIMD Architectures

> **MIMD** (Multiple Instruction, Multiple Data) - Multiple processors execute different instructions on different data;

There are two types of MIMD architectures:

* **Shared Memory** - All processors share a single memory space;
* **Distributed Memory** - Each processor has its own memory space.

| Shared Memory                                                                       | Distributed Memory                                                                     |
| ----------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| ✅Data sharing is **easy** (data divided in private data and shared data)            | ❌ Data sharing is **hard** (higher communication overhead)                             |
| ✅**Easy** to program                                                                | ❌ **Hard** to program                                                                  |
| ✅Tasks are **threads** - **lightweight** processes that share the same memory space | ❌ Tasks are **processes** - **heavyweight** processes that have their own memory space |
| ❌ **Scalability** is **limited**                                                    | ✅ **Scalability** is **unlimited**                                                     |
| ❌ Communication is **implicit** (harder to control)                                 | ✅ Communication is **explicit** (easier to control)                                    |
| ❌ More complex hardware                                                             | ✅ Simpler hardware                                                                     |

---

## Shared Memory Architecture

> **Shared Memory** - All processors share a single memory space;

There are two types of shared memory architectures:

* **Uniform Memory Access (UMA)** - All processors have the same access time to all memory locations;
  * Each core has a **cache**, and the caches can be **write-through** or **write-back**;
    * **Write-Through** - Data is written to the cache and directly to the main memory;
    * **Write-Back** - Data is written to the cache and only to the main memory when the cache line is replaced - **flush** - this is faster, because reduces the number of memory accesses;
* **Non-Uniform Memory Access (NUMA)** - Processors have different access times to different memory locations;
  * Memory is **physically distributed** across the system, and each processor has a **local memory** - local memory access is faster than remote memory access;
  * Distribution of shared address space is managed naturally by the **operating system** - address space is divided into **pages**, and each page is assigned to a **memory bank**.

### Cache Coherence

* Data **replication** is normal and desirable in a **shared memory** system, but it can lead to **inconsistencies** - **cache coherence** is needed;
* **Cache Coherence** - All processors see the same value for a memory location at any instant of time;
* There are two types of cache coherence protocols:
  * **Directory-Based** - The state of each shared memory block is kept in a **centralized location** - the **directory**;
    * **Uncached** - The block is not in any cache;
    * **Shared** - One or more processors have the block in their caches;
    * **Exclusive** - Only one processor has the block in its cache, and it has been modified, so the block in the main memory is **invalid**;
  * **Snooping-Based** - Each cache monitors the bus for **write** and **read** operations and updates its state accordingly;
    * **Invalidate Protocol** - When a processor writes to a block, it sends a **invalidate** message to all other processors - **mostly used** because of the lower bandwidth requirements;
    * **Update Protocol** - When a processor writes to a block, it sends an **update** message to all other processors - **broadcast**.

### Memory Consistency and Coherence

* A memory system is **coherent** if it always reads the most recent value written to the memory position being read - **one memory location**;
* **Memory consistency** is the order in which memory operations appear to be executed - **all memory locations**;
  * **Sequential Consistency** - Every processor sees the same order of memory operations;
    * Each write causes a processor to wait until all processors have been notified of the write;
* **Synchronization** is required to ensure memory consistency;
  * Typically, processors provide read-modify-write instructions, such as **test-and-set**, **compare-and-swap**, and **fetch-and-add**, that are **atomic** - **indivisible**;
  * **Spin Locks** - A processor waits for a lock to be released by spinning in a loop;

---

## Distributed Memory Architecture

> **Distributed Memory** - Each processor has its own memory space;

There are two programming models for distributed memory architectures:

* **Distributed Shared Memory (DSM)** - The memory is distributed, but the programming model is shared memory;
  * Distribution similar to **NUMA** - **segments** of the address space are assigned to different processors;
  * Easier programming, but **extremely inefficient**;
* **Multicomputers** - Each processor has its own **private address space**, not shared with other processors;
  * Data sharing requires explicit messages among processors - **message passing**;

> **Asymmetrical multicomputers** - Some processors are more powerful than others, and they are used to control the others.
>
> **Symmetrical multicomputers** - All processors are equal.

|                        | Single Address Space            | Multiple Address Spaces   |
| ---------------------- | ------------------------------- | ------------------------- |
| **Shared Memory**      | **UMA/NUMA** (OpenMP, Pthreads) | **Message-passing** (MPI) |
| **Distributed Memory** | **DSM** (ArgoDSM)               | **Message-passing** (MPI) |

There are multiple types of **distributed memory** systems:

* **Networks of Workstations (NOW)** - A set of workstations connected by a network;
* **Clusters** - A set of computers connected by a network - **COTS** (Commercial Off-The-Shelf) hardware;
* **Grids** - A set of clusters connected by a network - **HPC** (High-Performance Computing) - **distributed computing**;
* **Commercial Supercomputers** - custom hardware to provide a good balance between **scalability** and **performance**.

### Interconnection Networks and Circuit Switching

There are two types of **interconnection networks**:

* **Packet switching** - Data is divided into packets and sent through the network;
* **Circuit switching** - A dedicated path is established between the source and the destination; 
  * There are different metrics for the evaluation: **Bandwidth**, **diameter**, **cost** and **scalability**;
  * There are different topologies: **Bus**, **Ring**, **Mesh**, **Torus**, **Hypercube**, **Fat-Tree**, **Butterfly**.

### Latency Reduction Techniques

* **Asynchronous message send/receive** - The sender does not wait for the receiver to acknowledge the message;