# Advanced MPI

### Asynchronous Collectives

* Recent versions of MPI support asynchronous collective operations:

```c
MPI_Isomething(..., MPI_Request *request);
```

* Blocking and non-blocking do not mix, for example:

```c
if (rank == 0)
    MPI_Reduce(...);
else
    MPI_Ireduce(...);
```

* But for **point-to-point** operations, they can be mixed:

```c
MPI_Isend(...);
MPI_Recv(...);
```

_to be continued..._

---

## Hybrid Programming with OpenMP and MPI

* **Hybrid programming** combines **shared memory** and **distributed memory** programming models;
* Using both approaches can lead to **better performance**, since the program is executed in parallel in both levels - **several threads in each node and several nodes**;
* OpenMP adds **fine granularity** are allows increase and/or **dynamic load balancing**;
* A common strategy is to **decompose with MPI first** and then use OpenMP to **parallelize the computation of each node**;
* A common execution scenario:
  * A single MPI process is launched per node;
  * Each process spawns several threads;
  * At some **global synchronization point**, the master thread on each node communicates with the master thread on the other nodes;
* To use **OpenMP** and **MPI** together, we change `MPI_Init` to `MPI_Init_thread`:

```c
int provided;
MPI_Init_thread(&argc, &argv, MPI_THREAD_FUNNELED, &provided);
```

* The `provided` variable will contain the level of thread support provided by the MPI implementation;
* The possible values are:
  * `MPI_THREAD_SINGLE`: **Single-threaded** - only one thread will execute;
  * `MPI_THREAD_FUNNELED`: The main thread will make MPI calls, but other threads will not - the **default**;
  * `MPI_THREAD_SERIALIZED`: Only one thread will make MPI calls **at a time**;
  * `MPI_THREAD_MULTIPLE`: Multiple threads can make MPI calls at the same time, with **no restrictions** - this requires very careful programming.

---

## Load Balancing

_to be continued..._

---

## Termination Detection

_to be continued..._