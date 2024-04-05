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
  * `MPI_THREAD_SERIALIZED`: Multiple threads can make MPI calls, but they must be **serialized**;
  * `MPI_THREAD_MULTIPLE`: Multiple threads can make MPI calls at the same time, with **no restrictions** - this requires very careful programming.

---

## Load Balancing

* **Load balancing** is the process of **distributing the workload evenly** across all the processors in a parallel system;
* **Static load balancing** is when the workload is distributed at the **beginning** of the computation and **does not change**;
  * Can be done using optimization techniques: round-robin, simulated annealing, genetic algorithms, etc; 
  * It has some **limitations** like:
    * Processors may have **different speeds**;
    * Computation effort of each task may not be **known a-priori**;
* **Dynamic load balancing** is when the workload is distributed **dynamically** during the computation;
  * Overcomes the limitations of static load balancing, however, has the penalty of **overhead**, due to **task management**;
  * Can be **centralized**: master thread is responsible for distributing the tasks - **work pool** model (master-slave);
    * Master can become a **bottleneck**, if it can only issue one task at a time;
  * Can be **decentralized**: all processes are equal and can exchange tasks;
    * **Fully distributed work pool** model - each process starts with a given number of tasks, but may send or receive tasks from other processes if needed;
      * **Received-initiated** - process requests tasks from other processes if it has no more tasks to execute;
      * **Send-initiated** - process sends tasks to other processes if it has more tasks than it can execute.


---

## Termination Detection

* **Termination detection** is the process of determining when a parallel computation has **finished**;
* In **static load balancing** it is easy to determine when the computation has finished, since all tasks are known in advance;
* In **dynamic load balancing**:
  * **Centralized** - the master process can determine when the task pool is empty and the slaves have finished their tasks;
  * **Decentralized** - it is more complex, since processes can send and receive tasks at any time;
    * **Acknowledgement-based** - each process has two states: **active** and **inactive**;
      * A process starts inactive and becomes active when receives a task;
      * When it receives a task, the sender becomes his **parent**;
      * Every time it receives a task, it sends an **acknowledgement** to the sender;
      * Process becomes inactive when:
        * It has no more tasks to execute;
        * It has received all the acknowledgements from its children;
        * It transmitted all the acknowledgements to its sender;
      * When the **first process** becomes inactive, the computation has finished.
    * **Ring-termination** - each process sends a **token** to its neighbor;
      * Process becomes **white** when they terminate;
      * When P0 becomes white, it sends a **white token** to P1;
      * A process Pi becomes **black** if it sends a message to Pj (j < i);
      * When a black process becomes white (terminates), it **sends the token to the next process** - if the process is black, the token becomes black, otherwise, it keeps the color;
      * When P0 receives a black token, it sends a white token to P1;
      * **When P0 receives a white token, it knows that all processes have terminated**.