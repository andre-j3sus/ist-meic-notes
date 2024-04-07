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
---

## Key Concepts

* **Data dependence**: ordering on a pair of operations that must be preserved in order to maintain correctness;
* **Race condition**: when the result of an execution depends on the timing of two or more events;
* **Synchronization**: mechanism used to sequence control among threads or to sequence accesses to data in parallel;
  * Mutual exclusion areas, semaphores, barriers, etc;
  * Read-modify-write operations modify the memory address **atomically**;
* **Deadlock**: one task waiting on a condition that is never satisfied;
* **Livelock**: two or more tasks are not waiting, but not making progress either;
* **Memory coherence**: all processors see the same value for a memory location - **access behavior of a single memory location**;
  * **Cache coherence** protocols keep track of the state of the shared blocks of data;
    * **Directory-based**: state of each shared block kept in a centralized location; state can be **uncached**, **exclusive** or **shared**;
    * **Snooping**: each cache has the sharing status of each block it stores, and monitors (snoops) the bus for changes; **invalidate protocol** (processor updates the block and sends an invalidate message to all other caches) or **update protocol** (processor updates the block and **broadcasts** the update to all other caches); **invalidate** is more efficient because of the lower bandwidth required;
* **Memory consistency**: all processors see the same order of memory accesses - **interaction between different memory locations**;
  * Sequential consistency: all processors see the same order of writes;
* **Principle of locality**: programs tend to access a small portion of the address space at any given time - **caches are used to exploit this principle**;
  * **Temporal locality**: if a memory location is referenced, it will tend to be referenced again soon;
  * **Spatial locality**: if a memory location is referenced, nearby memory locations will tend to be referenced soon;

> Usually caches are **write-back** (write to cache and later flush to memory), instead of **write-through** (write to cache and memory at the same time).

### Parallel Architectures

There are two main types of parallel architectures:

* **Shared Memory**: all processors share a single address space;
  * **Memory coherence** is guaranteed by the hardware, both in UMA and NUMA architectures;
  * **Memory consistency** is not guaranteed by the hardware, but by the programmer, using synchronization mechanisms;
  * **Uniform Memory Access (UMA)**: all processors have the same access time to memory; only one memory location;
  * **Non-Uniform Memory Access (NUMA)**: processors have different access times to memory; multiple memory locations; **local memory accesses are faster than remote memory accesses**.
* **Distributed Memory**: each processor has its own memory;
  * **Distributed Shared Memory (DSM)**: each processor has its own memory, but can access other processors' memory - simpler programming but **extremely inefficient**;
  * **Message Passing Interface (MPI)**: each processor has its own address space, not accessible by other processors; communication is done through messages.

| **Shared Memory**                 | **Distributed Memory**              |
| --------------------------------- | ----------------------------------- |
| ✅ Sharing data is easy            | ❌ Sharing data is hard              |
| ✅ Simple to program               | ❌ Complex to program                |
| ✅ Tasks are threads (lightweight) | ❌ Tasks are processes (heavyweight) |
| ❌ Limited scalability             | ✅ Scalable                          |
| ❌ Implicit communication          | ✅ Explicit communication            |
| ❌ Complex hardware                | ✅ Simple hardware                   |

> #### Types of Parallelism
>
> * **Functional Parallelism**: different tasks that can be executed in parallel;
> * **Data Parallelism**: same task that can be executed in parallel on different data.
> * **Pipeline Parallelism**: different tasks that can be executed in sequence, but in parallel, synchronized at a given point.

### OpenMP

* Better than **Pthreads** for shared memory programming: **simpler programming model**; similar because the programmer needs to detect dependencies and prevent data races;
* **Parallelization directives**:
  * `#pragma omp parallel`: create a team of threads; all threads execute the block of code and synchronize at the end;
  * `#pragma omp for`: distribute iterations of a loop among threads; synchronization is done automatically at the end of the loop; `nowait` can be used to avoid synchronization - **data parallelism**;
  * `#pragma omp sections`: distribute sections of code among threads; `#pragma omp section` is used to define a section - **functional parallelism**;
  * `#pragma omp single`: only one thread executes the block of code;
    * `copyprivate`: data is private to each thread, but initialized with the value of the original data;
    * `#pragma omp master`: only the master thread executes the block of code;
    * ⚠️ Can cause **deadlocks** if not used correctly, e.g. use a parallel for inside a single or master block;
  * `#pragma omp task`: create a task that can be executed **asynchronously**; the task is added to a queue/pool and executed by a thread - **flexible way for irregular parallelism**;
    * ⚠️ Order not guaranteed;
    * `#pragma omp taskwait`: wait for all tasks to finish;
    * Contrary to other directives, variables inside the task are **firstprivate** by default;
    * Possible to specify `priority`;
    * `depend(in: x, y)`: task depends on the values of `x` and `y` for input;
    * `depend(out: x, y)`: task writes the values of `x` and `y` for output;
    * `depend(inout: x, y)`: task depends on the values of `x` and `y` for input and writes the values of `x` and `y` for output;
* **Synchronization directives**:
  * `#pragma omp critical`: only one thread executes the block of code at a time; a thread waits and only executes the block when no other thread is executing it;
  * `#pragma omp atomic`: the operations inside the code block are executed atomically;
  * `#pragma omp barrier`: all threads wait until all threads reach the barrier;
* **Data environment**:
  * `shared`: data is shared among all threads - **default** (except for the loop index in a parallel for, which is private by default);
  * `private`: data is private to each thread - copy - **undefined** at the beginning and at the end of the parallel region;
  * `firstprivate`: data is private to each thread, but **initialized** with the value of the original data;
  * `lastprivate`: data is private to each thread, but the **value of the last iteration is copied** to the original data;
  * `threadprivate`: data is private to each thread - initial data is undefined; - use `copyin` to initialize the data - data from the master thread is copied to all threads;
  * `reduction`: data is private to each thread, but the final value is computed by reducing the values of all threads, and is written to the global shared variable; e.g. parallelize a sum or a dot product; e.g. `#pragma omp parallel for reduction(+:sum)`;
* **Conditional parallelism**:
  * `if`: only create a team of threads if the condition is true;
* **Load balancing**:
  * `schedule(static, [chunk])`: iterations divided in blocks of size `chunk` and distributed among threads in a round-robin fashion; if `chunk` is not specified, it is automatically calculated: `chunk = ceil(n / p)` - **less overhead**;
  * `schedule(dynamic, [chunk])`: iterations are distributed dynamically among threads; each thread gets a new block of iterations when it finishes the previous one; if `chunk` is not specified, it is `1` - **adapts better to load imbalance**;
  * `schedule(guided, [chunk])`: similar to dynamic, but the block size decreases over time; if `chunk` is not specified, it is `1`;
  * `schedule(runtime)`: schedule is defined at runtime using the environment variable `OMP_SCHEDULE`;
  * `schedule(auto)`: schedule is defined by the compiler;
  * **Larger chunks** reduce overhead, but can cause **load imbalance**; 
  * `collapse(n)`: collapse `n` nested loops into a single loop;
* **Nested parallelism**: nested parallel regions are allowed;
  * `omp_set_nested(1)`: enable nested parallelism.

> `nowait` can be used to avoid synchronization in `#pragma omp for` and other directives, like `#pragma omp sections`, `#pragma omp single`, etc.

> #### Efficiency Rules
>
> * Minimize forks and joins;
> * Maximize private data;
> * Minimize synchronization.

**False sharing**: two processors **share the same cache block**, but **do not share the same memory location** inside the block; if one processor writes to its memory location, the cache block is invalidated in the other processor, even if it is not writing to the same memory location.

### Foster's Methodology

* Used to design parallel algorithms;

1. **Partitioning**: decomposition of the problem into smaller tasks - **primitive tasks** with the same size; several decomposition strategies like **input decomposition** (divide the input data), **output decomposition** (divide the output data), **recursive decomposition** (divide-and-conquer);
2. **Communication**: define the communication between tasks; **local communication** values shared by a small number of tasks, **global communication** values shared by all tasks - important to minimize communication;
3. **Agglomeration**: group tasks into larger tasks to reduce communication overhead; maximize **locality**; group tasks with high communication with each other;
4. **Mapping**: assign a task to a processor; **load balancing** is important to avoid idle processors; **maximize processor utilization**.

### MPI

* `MPI_Init(argc, argv)`: initialize MPI;
* `MPI_Finalize()`: finalize MPI;
* **Communicator** - an object that represents a **set of tasks** and the **communication channels** between them; `MPI_COMM_WORLD` is the default communicator;
  * `MPI_Comm_size(communicator, size)`: get the number of tasks in a communicator;
  * **Rank** - unique identifier of a task in a communicator (integer) - `MPI_Comm_rank(communicator, rank)` to get the rank of a task;
* **Point-to-point communication**:
  * `MPI_Send(buffer, count, datatype, destination, tag, communicator)`: send a message to a task - **blocking**;
  * `MPI_Recv(buffer, count, datatype, source, tag, communicator, status)`: receive a message from a task - **blocking**;
  * **Source** and **destination** are the ranks of the tasks; can be `MPI_ANY_SOURCE` in the receive function;
  * **Tag** is an integer to identify the message; useful to distinguish between different messages from the same source, or to receive messages from different sources, with the same tag; wildcard `MPI_ANY_TAG` can be used;
  * There are some predefined data types, like `MPI_INT`, `MPI_FLOAT`, `MPI_DOUBLE`, etc;
  * `MPI_Status` is a structure that contains information about the message received;
* **Asynchronous communication**:
  * `MPI_Isend(buffer, count, datatype, destination, tag, communicator, request)`: send a message to a task - **non-blocking**;
  * `MPI_Irecv(buffer, count, datatype, source, tag, communicator, request)`: receive a message from a task - **non-blocking**;
  * `MPI_Request` is a handle to the request;
  * `MPI_Wait(request, status)`: wait for the completion of a request; can use `MPI_STATUS_IGNORE` if the status is not needed;
  * `MPI_Waitall(count, requests, statuses)`: wait for the completion of all requests;
  * `MPI_Test(request, flag, status)`: test if a request has completed; `flag` is `true` if the request has completed;
  * `MPI_Testall(count, requests, flag, statuses)`: test if all requests have completed;
  * `MPI_Cancel(request)`: cancel a request;
  * `MPI_Waitany`, `MPI_Testany`, `MPI_Waitsome`, `MPI_Testsome` are also available;
  * `MPI_Probe(source, tag, status)`: blocks until the message is received, but still needs the `MPI_Recv` to receive the message; `MPI_Iprobe` is the non-blocking version;
* **Collective operations**:
  * `MPI_Bcast(buffer, count, datatype, root, comm)` - broadcast data to all processes; root indicates which process sends the data (from `buffer`);
  * `MPI_Scatter(sendbuffer, sendcount, senddatatype, recvbuffer, recvcount, recvdatatype, root, comm)` - scatter data from one process to all processes; root indicates which process sends the data (from `sendbuffer`);
    * `MPI_Scatterv` is the version that allows different sizes of data to be sent to each process;
  * `MPI_Gather(sendbuffer, sendcount, senddatatype, recvbuffer, recvcount, recvdatatype, root, comm)` - gather data from all processes to one process; root indicates which process receives the data (to `recvbuffer`);
    * `MPI_Gatherv` is the version that allows different sizes of data to be received from each process;
    * `MPI_Allgather` and `MPI_Allgatherv` are the versions that send data to all processes;
  * `MPI_Alltoall(sendbuffer, sendcount, senddatatype, recvbuffer, recvcount, recvdatatype, comm)` - send data from all processes to all processes;
    * `MPI_Alltoallv` is the version that allows different sizes of data to be sent and received by each process;
  * `MPI_Reduce(indata, outdata, count, type, op, root, comm)` - performs a reduction;
    * `MPI_Oper` can be `MPI_SUM`, `MPI_PROD`, `MPI_MIN`, `MPI_MAX`...
    * `MPI_Allreduce(indata, outdata, count, type, op, comm)`;
* `MPI_Barrier` is s synchronization barrier.

### Performance Analysis

#### Basic Units

* $n$: problem size;
* $p$: number of processors;
* $\sigma(n)$: **serial** time to solve a problem of size $n$;
* $\phi(n)$: **parallel** time to solve a problem of size $n$;
* $\kappa(n, p)$: **communication** time to solve a problem of size $n$ on $p$ processors;
* $T(n, p)$: **total** time to solve a problem of size $n$ on $p$ processors.
  * $T(n, p) = \sigma(n) + \frac{\phi(n)}{p} + \kappa(n, p)$.
  * $T(n, 1) = \sigma(n) + \phi(n)$ - **sequential execution**.

* **Speedup**: measure how much faster a program runs on `p` processors compared to `1` processor - sequential execution.
  * $\psi(n, p) = \frac{T(n, 1)}{T(n, p)} = \frac{\sigma(n) + \phi(n)}{\sigma(n) + \frac{\phi(n)}{p} + \kappa(n, p)}$;
* **Efficiency**: measure the utilization of the available processors;
  * $\epsilon(n, p) = \frac{\psi}{p}$;
  * $0 \leq \epsilon(n, p) \leq 1$;
* **Amdahl's Law**: speedup is **limited** by the fraction of the program that **cannot be parallelized**;
  * $f$: fraction of the program that cannot be parallelized - $f = \frac{\sigma(n)}{\sigma(n) + \phi(n)}$;
  * $\psi(n, p) \leq \frac{1}{(f) + \frac{1 - f}{p}}$;
  * **Optimistic** - neglects parallelization overheads;
  * **Strong scaling** - **problem size is fixed**, and the number of processors is increased;
  * **Ahmdal's Effect**: $k(n,p)$ has in general lower complexity than $\phi(n) / p$, so as $n$ increases, speedup increases;
* **Gustafson-Barsis' Law**: speedup is **limited** by the fraction of the program that **can be parallelized**;
  * $s$: fraction of the program that can be parallelized - $s = 1 - f = \frac{\sigma(n)}{\sigma(n) + \frac{\phi(n)}{p}}$;
  * $\psi(n, p) \leq p + s(1 - p)$;
  * **Weak scaling** - **problem size is increased** proportionally to the number of processors - **scaled speedup**;
  
> ⚠️ Both **Amdahl's Law** and **Gustafson-Barsis' Law** calculate the same speedup, but from different perspectives.

* **Karp-Flatt Metric**: measures the **scalability** of a parallel algorithm;
  * **Experimentally determined serial fraction** represents the fraction of the original program that cannot be parallelized with respect to the sequential execution time: $e(n, p) = \frac{\sigma(n) + \kappa(n, p)}{\sigma(n) + \phi(n)} = \frac{\frac{1}{\psi(n, p)} - \frac{1}{p}}{1 - \frac{1}{p}}$;
  * If its constant - **large serial fraction**;
  * If increases - **overhead not negligible**.
* **Isoefficiency**: measure the **scalability** of a parallel algorithm;
  * **Scalability**: the ability of a parallel algorithm to solve larger problems in the same time, or the same problem in less time, as the number of processors increases;
  * **Isoefficiency relation**: to maintain the same level of efficiency as the number of processors increases, the problem size must increase such that: $T(n, 1) \geq C \cdot T0(n, p)$, where $C$ is a constant;
  * $C = \frac{\epsilon(n, p)}{1 - \epsilon(n, p)}$;
  * $T0(n, p) = (p - 1) \cdot \sigma(n) + p \cdot \kappa(n, p)$;
  * Simplify until $n \geq f(p)$;
  * **Scalability function**: $\frac{M(f(p))}{p}$ indicates how memory usage per processor must increase to maintain the same level of efficiency.

### Other Stuff

#### Load Balancing

* **Static load balancing**: workload is divided at the **beginning** and **does not change**;
* **Dynamic load balancing**: workload is divided at runtime and can change; 
  * **Centralized**: one process (master) is responsible for assigning tasks from a **queue**: **work poll** model;
  * **Decentralized**: all processes are equal;
    * Can be implemented from extending the work pool with a **tree** model, where a global master distributes tasks to **second-level masters**, that distribute tasks to workers; more levels means more decentralization;
    * **Fully distributed work pool**: each process starts with a given number of tasks, but may send/receive tasks;
      * **Receiver-initiated**: processes request tasks from other processes, when as a few or no tasks are left;
      * **Sender-initiated**: processes send tasks to other processes, when they have too many tasks.

> **Dynamic load balancing** is used when the **workload is not known in advance** or when the **workload changes over time**, however, it has **higher overhead** due to task management.

#### Termination Detection

* **Centralized termination detection**: one process (master) is responsible for detecting termination, once the **pool of tasks is empty**;
* **Distributed termination detection**: each process is responsible for detecting its own termination, following two conditions:
  * **Local termination**: process has finished its work;
  * **Global termination**: all processes have finished their work - **there are no messages in transit**;
    * **Acknowledgement messages** can be used to detect global termination: each process has two states **active** and **inactive**; processes start inactive and become active when they receive the first task - **sender** of the task becomes **parent** of the process; after finishing the task, the process sends an **acknowledgement** message to the parent; process is ready to become inactive when: it has no tasks, it has transmitted all acknowledgements, and all its children are inactive - when the first process becomes inactive, computation is finished.
    * **Ring termination**: each process sends a **token** to the next process, that can be **white**(inactive) or **black**(active); when a process terminates, it becomes white; when P0 becomes white, it sends white token to P1; process Pi becomes black if it sends a message to process Pj and j < i; when P0 receives a black token, it passes a white token; if P0 receives a white token, it means that all processes have terminated. 

### Parallel Numerical Algorithms

* Methods to solve linear systems:
  * **Direct methods**: solution is sought directly, at once;
    * **Gaussian elimination** and **LU decomposition**;
  * **Iterative methods**: give an initial guess and improve it iteratively;
    * Relaxation methods, **Jacobi** and **Gauss-Seidel**;
    * Gauss-Seidel converges faster than Jacobi;
* Linear second-order PDEs:
  * **Finite Difference Method**;
  * **Ghost points** - memory locations used to store redundant copies of data, held by neighboring processors, to facilitate communication between processors;

### Combinatorial Search, Monte Carlo Methods and Parallel Sort

* **Combinatorial Search**: find one or more optimal or suboptimal solutions in a defined problem space;
    * **Decision problems**: determine if there is a solution to a given set of constraints;
    * **Optimization problems**: find the best solution to a given set of constraints;
  * **Backtrack Search**: uses a **depth-first search** strategy to explore the solution space; backtrack occurs when a node has no children, all children have been visited, or a constraint is violated;s
  * **Branch and Bound**: also uses a **depth-first search** strategy to explore the solution space; **pruning** is used to eliminate branches that cannot lead to a better solution; usually used in optimization problems;

* **Monte Carlo methods**: solve a problem using statistical sampling; **randomness** is used to solve deterministic problems; **random sampling** is used to estimate the solution of a problem;

* Parallel Sort
  * **Hyperquicksort**: parallel version of quicksort, where **elements are sorted before broadcasting the pivot**;
    * Sort elements in each process;
    * Select median as pivot element and broadcast it;
    * Each process in the upper half swaps with a partner in the lower half;
    * Recursively sort the two halves;
    * **Problem**: **load imbalance** causes low efficiency;
    * **PSRS** **gets sample values from all processes before choosing a median;**
  * **Parallel Sorting by Regular Sampling (PSRS)** - better than hyperquicksort;
    * Each process sorts its share of elements;
    * Each process selects regular samples of sorted list;
    * One process gathers and sorts samples, chooses pivot values from sorted sample list and broadcast these pivot values;
    * Each process partitions its list based on pivot values;
    * Each process sends partitions to other processes;
    * Each process merges received partitions;
  * **Odd-Even Transposition Sort** - based on bubble sort; consists in two phases:
    * **Even phase**: each process compares its element with the next process and swaps if necessary;
    * **Odd phase**: each process compares its element with the next process and swaps if necessary;
    * Sorting is complete after at most $n$ phases.