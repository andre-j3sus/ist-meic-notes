# [Parallel and Distributed Computing](https://fenix.tecnico.ulisboa.pt/disciplinas/CPD23/2023-2024/2-semestre)

## Recap

* Computer Architecture;
* Analysis of Algorithms;

## Syllabus

* [Parallel Architectures](01-parallel-architectures.md): Shared and Distributed Memory;
* Program Analysis: Data and Functional Parallelism;
* OpenMP - Shared Memory Parallelism;
* MPI - Distributed Memory Parallelism;
  * Partitioning, Communication, Agglomeration, Mapping, and Load Balancing;
* Analysis of Parallel Programs;
  * Performance Metrics, Debugging and Fundamental limitations;
* Case Studies;

---

### Speedup

The main objective is to **reduce execution time** by **parallelizing** the execution of a program:

* **Speedup**: Ratio of the execution time of the sequential program to the execution time of the parallel program;
  * $S = \frac{T_serial}{T_parallel}$;
* The **greater** the **speedup**, the **better** the **parallelization**;
* The **ideal speedup** is the **number of processors**, but we expect to have a **lower speedup** due to **communication overhead** and **synchronization**;
* **Amdahl's Law**: The speedup of a program using multiple processors in parallel computing is limited by the time needed for the sequential fraction of the program;
  * $S = \frac{1}{f + \frac{1 - f}{p}}$;
  * $f$: Fraction of the program that is sequential;
  * $p$: Number of processors;
  * The **speedup** is **limited** by the **sequential fraction** of the program;

### Difficulties

* Data **dependencies**;
* **Race conditions**;
* **Synchronization**;
* **Deadlocks**;
* **Live-locks**;