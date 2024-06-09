# MPI Basics

- **Message Passing Interface** (MPI) is a standard for writing message-passing programs to be run on a parallel computer;
- Send/receive point-to-point messages;
- Messages Many-to-Many;
- Basic MPI application:
  - Data decomposition options;
  - Parallel algorithm development analysis;
  - Benchmarking;
  - Optimizations.

---

## MPI Concepts

For example, consider this simple MPI application:

- 4 tasks, numbered 0 to 3;
- Communication paths between them;
- The set of tasks plus the communication channels is called `MPI_COMM_WORLD`;
- To use MPI, the program must include the `mpi.h` header file;
- **OpenMPI** is a popular implementation of the MPI standard;
  - To compile: `mpicc -o my_program my_program.c`;
  - To run: `mpirun -np 4 my_program`.

Example of a simple MPI program:

```c
#include <stdio.h>
#include <mpi.h>

int main(int argc, char *argv[]) {
  int rank, size;

  MPI_Init(&argc, &argv);
  MPI_Comm_rank(MPI_COMM_WORLD, &rank);
  MPI_Comm_size(MPI_COMM_WORLD, &size);

  printf("Hello from task %d of %d\n", rank, size);

  MPI_Finalize();
  return 0;
}
```

- `MPI_Init` and `MPI_Finalize` are used to initialize and finalize the MPI environment;
- `MPI_Comm_rank` and `MPI_Comm_size` are used to obtain the rank and size of the `MPI_COMM_WORLD` communicator;
- `rank` is the task number and `size` is the number of tasks in the communicator.

---

## Point-to-Point Communication

- **Point-to-point communication** is the most basic form of communication in MPI;
- `MPI_Send` and `MPI_Recv` are used to send and receive messages;
- `MPI_Send(void *buf, int count, MPI_Datatype datatype, int dest, int tag, MPI_Comm comm)`;
  - `buf`: pointer to the data to be sent;
  - `count`: number of elements to be sent;
  - `datatype`: type of the elements;
  - `dest`: rank of the destination task;
  - `tag`: message tag;
  - `comm`: communicator;
- `MPI_Recv(void *buf, int count, MPI_Datatype datatype, int source, int tag, MPI_Comm comm, MPI_Status *status)`;
  - `buf`: pointer to the data to be received;
  - `count`: number of elements to be received;
  - `datatype`: type of the elements;
  - `source`: rank of the source task; use `MPI_ANY_SOURCE` to receive from any task;
  - `tag`: message tag; use `MPI_ANY_TAG` to receive any message;
  - `comm`: communicator;
  - `status`: status of the received message.

> **Note**: Unless there is a specific reason to use `MPI_ANY_SOURCE` and `MPI_ANY_TAG`, it is better to use specific values to avoid unexpected behavior.

### Predefined MPI Datatypes

- MPI provides a set of predefined datatypes for common C types:
  - `MPI_INT`, `MPI_FLOAT`, `MPI_DOUBLE`, `MPI_CHAR`, etc;
- Custom datatypes can be created using `MPI_Type_create_struct` and `MPI_Type_commit`.

### Return Status

- The `MPI_Status` structure contains information about the received message;
- It is used to obtain the source, tag, and number of elements received.
- `status.MPI_SOURCE`: rank of the source task;
- `status.MPI_TAG`: message tag;
- `status.MPI_ERROR`: error code.
- `MPI_Get_count` can be used to obtain the number of elements received.

---

## Blocking and Non-Blocking Communication

- By default, `MPI_Send` and `MPI_Recv` are blocking operations;
  - The sender blocks until the receiver has received the message, or MPI has saved the data somewhere;
  - The receiver blocks until the receive buffer has been filled with valid data;
- There are non-blocking versions of these operations:
  - `MPI_Isend` and `MPI_Irecv` are non-blocking versions of `MPI_Send` and `MPI_Recv`;
  - They return immediately, allowing the program to perform other tasks;
  - `MPI_Wait` is used to wait for the completion of the communication; returns status;
  - `MPI_Test` is used to test if the communication has completed; returns a flag and status.
  - `MPI_Cancel` is used to cancel a communication request;
  - `MPI_Waitany`, `MPI_Waitall`, and `MPI_Waitsome` are used to wait for multiple communication requests;
  - `MPI_Request` is used to identify the communication request.

> ⚠️ **Caution**: Non-blocking communication
>
> - May not modify the buffer between the non-blocking send and the wait, or between the non-blocking receive and the wait;
> - May not have two non-blocking sends or two non-blocking receives for the same buffer.

- `MPI_Probe` is used to test if a message is available for a given source and tag;
  - It returns a flag and status;
  - It can be used to obtain the size of the message and then allocate the receive buffer.
- `MPI_Iprobe` is a non-blocking version of `MPI_Probe`.

---

## Communicators

- A **communicator** is a group of tasks that can communicate with each other and the private communication channels between them;
- `MPI_COMM_WORLD` is the default communicator;
- There are different collective operations:
  - `MPI_Bcast` is used to broadcast a message from one task to all tasks in the communicator;
  - `MPI_Scatter` is used to distribute the elements of an array to all tasks in the communicator; each process receives a different part of the array;
  - `MPI_Gather` is used to gather the elements of an array from all tasks in the communicator; each process sends a different part of the array;
  - `MPI_Allgather` is used to gather the elements of an array from all tasks in the communicator and broadcast the result;
  - `MPI_Reduce` is used to reduce the elements of an array from all tasks in the communicator; `MPI_Allreduce` is used to reduce and broadcast the result; some predefined operations are available: `MPI_SUM`, `MPI_MAX`, `MPI_MIN`, etc;
  - `MPI_Alltoall` is used to exchange data between all tasks in the communicator;
  - `MPI_Barrier` is used to synchronize all tasks in the communicator - no process can leave the barrier until all processes have entered it.

| Collective Operation | Complexity  |
| -------------------- | ----------- |
| `MPI_Bcast`          | $O(\log p)$ |
| `MPI_Scatter`        | $O(p)$      |
| `MPI_Gather`         | $O(p)$      |
| `MPI_Allgather`      | $O(p)$      |
| `MPI_Reduce`         | $O(\log p)$ |
| `MPI_Allreduce`      | $O(\log p)$ |

---

## Benchmarking Code

- **Benchmarking** is the process of measuring the performance of a program;
- `MPI_Wtime` is used to obtain the current time;
- `MPI_Wtick` is used to obtain the resolution of `MPI_Wtime`;
- To eliminate the effect of the startup time, the benchmarking code should be placed between `MPI_Barrier` calls:

```c
...
MPI_Init(&argc, &argv);
MPI_Barrier(MPI_COMM_WORLD);
elapsed_time = -MPI_Wtime();

...

elapsed_time += MPI_Wtime();
```
