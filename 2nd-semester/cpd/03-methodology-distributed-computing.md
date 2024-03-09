# Methodology for Distributed Computing

## Programming Distributed-memory Systems

There are multiple overheads in parallel programming, such as:

* Task creation/finalization;
* Data transfer;
* Communication/synchronization;
* Load balancing;

|       | Shared-memory                                       | Message-Passing                   |
| ----- | --------------------------------------------------- | --------------------------------- |
| Tasks | **Dynamic** creation                                | **Static** (active until the end) |
| Data  | Data partition is not an issue, but data sharing is | Data partition is **crucial**     |

In both cases, the programmer must:

* Minimize synchronization points;
* Be careful about load balancing.

**Message-Passing** is the most common approach for distributed-memory systems - data is partitioned and each processor has its own memory.

---

## Foster’s Design Methodology

> **Foster’s Design Methodology** is a methodology for designing parallel algorithms and programs.

The methodology is based on the following steps:

1. **Partitioning**: Divide the problem into sub-problems - small **primitive tasks**;
2. **Communication**: Identify the communication between sub-problems;
3. **Agglomeration**: Group sub-problems to reduce communication overhead;
4. **Mapping**: Assign sub-problems to processors.

### Partitioning

* There are multiple strategies: 
  * **Data decomposition**: identify the data on which computations are performed and partition it; it can be based on:
    * **Input data**: applicable if each output is computed as a function of the input; 
    * **Output data**: applicable if each element of the output is computed independently - e.g., matrix multiplication;
  * **Recursive decomposition**: divide the problem into sub-problems until they are small enough to be solved sequentially - **divide and conquer**;
* Checklist:
  * `>10P` primitive tasks than `P` processors;
  * Minimize redundant computations and redundant data storage;
  * Primitive tasks are roughly the same size;
  * Number of tasks grows with the problem size.

### Communication

* Identify the communication pattern among primitive tasks:
  * **Local communication**: values shared by a small number of tasks;
  * **Global communication**: values shared by all tasks;
* Checklist:
  * Communication balanced among tasks;
  * Each task communicated with a small number of tasks;
  * Tasks can perform their communication concurrently.

### Agglomeration

* Strategies:
  * Group tasks that have high communication with each other;
  * Group sender tasks and group receiving tasks;
  * Group tasks to allow re-use of sequential code;
* Checklist:
  * Locality has been maximized;
  * Replicated computations take less time than the communications they replace;
  * Amount of replicated data is small enough to allow algorithm to scale;
  * Tasks are balanced in terms of computation and communication;
  * Number of tasks grows naturally with the problem size;
  * Number of tasks is small, but at least as great as the number of processors;
  * Cost of modifications to sequential code is small.

### Mapping

* Strategies:
  * **Maximize processor utilization**: assign tasks to processors to minimize idle time - **load balancing**;
  * **Minimize inter-processor communication**: assign tasks to processors to minimize communication overhead.