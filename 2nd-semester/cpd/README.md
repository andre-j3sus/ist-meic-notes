# [Parallel and Distributed Computing](https://fenix.tecnico.ulisboa.pt/disciplinas/CPD23/2023-2024/2-semestre)

## Syllabus

1. [Parallel Architectures](01-parallel-architectures.md): Shared and Distributed Memory;
2. [Parallel Programming with **OpenMP**](02-parallel-programming-openmp.md);
3. [Methodology for Distributed Computing](03-methodology-distributed-computing.md) - Foster's Methodology;
4. [**MPI** Basics](04-mpi-basics.md);
5. [Analysis of Parallel Programs](05-analysis-parallel-programs.md) - performance analysis, matrix-vector multiplication;
6. [**MPI** Advanced Topics](06-mpi-advanced-topics.md) - OpenMP and MPI hybrid programming, load balancing and termination detection;
7. [Parallel Numerical Algorithms](07-parallel-numerical-algorithms.md);
8. [Search, Monte Carlo and Sorting Algorithms](08-search-monte-carlo-sorting.md).

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