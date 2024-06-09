# Read/Write Protocols

- **Single register**, **shared** by all processes;
- Interface:
  - `read()`: returns the value of the register;
  - `write(v)`: writes the value `v` in the register, and returns `ack` when the write is done;
  - Values are **integers**;
  - Initially, the register contains the value `0`;
  - Every written value is unique.

## Specification #1 - Regular Register

- Initially, we assume a **single writer**;
- **Liveness**: if a correct process invokes an operation, then the operation eventually completes;
- **Safety**: reads must return:
  - the last value written, if there is no concurrent write;
  - either the last value or any value concurrently written;
- When a process crashes in the middle of reading/writing the operation interval does not have an upper limit - the operation may never complete.

---

## Specification #2 - Atomic Register

- Additional new safety property: **ordering** - if a process reads a value `v` and a subsequent read returns a value `w`, then `w` was written after `v`;
- Also known as **linearizability** - a total order of operations that respects the real-time order of operations;
- An alternative definition for atomicity: for any operation, there is a **serialization point** between the invocation and the reply, such that if we move the invocation and the reply to that point, the resulting execution obeys the sequential specification of a read/write register.

_to be continued..._
