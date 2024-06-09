# Broadcast Primitives

We will consider three forms of reliability for a broadcast primitive:

- **Best-effort broadcast**: the sender sends the message, and the system does its best to deliver it, but it may fail;
- **Reliable broadcast in the crash fault model**: the system guarantees that if the sender is correct, then all correct processes will deliver the message;
- **Reliable broadcast in the arbitrary fault model**: the system guarantees that if the sender is correct, then all correct processes will deliver the message.

---

## Best-Effort Broadcast

The best-effort broadcast is the simplest form of broadcast. The sender sends the message, and the system **does its best to deliver it**, but it may fail. The system does not guarantee that the message will be delivered, but it does not guarantee that it will not be delivered either.

Properties:

- **Validity**: if `pi` and `pj` are correct, then every message broadcast by `pi` is eventually delivered by `pj`;
- **No duplication**: no message is delivered more than once;
- **No creation**: no message is delivered unless it was sent.

All these properties are the same as the ones for the **perfect link**.

---

## Reliable Broadcast in the Crash Fault Model

The reliable broadcast in the crash fault model guarantees that if the sender is correct, then all correct processes will deliver the message.

The properties are the same as the ones for the best-effort broadcast, with the addition of:

- **Agreement**: if a correct process delivers a message `m`, then all correct processes deliver `m`.

---

## Reliable Broadcast in the Arbitrary (Byzantine) Fault Model

- A **byzantine process** may:
  - broadcast messages different from the ones sent by applications;
  - forge messages so that they look as originated by other processes;
  - send different messages to different processes;
- **Authenticated perfect links** are a useful tool, disallowing byzantine processes from forging messages;
- **Digital signatures** are a useful tool, allowing processes to sign messages, and other processes to verify the signature.

We consider two variants of reliable broadcast in the arbitrary fault model:

- **Byzantine Consistent Broadcast**;
  - If the sender is correct, then all correct processes deliver the same message;
  - If the sender is byzantine, then all correct processes deliver the same message, **if they deliver any**;
- **Byzantine Reliable Broadcast**;
  - If a correct process delivers `m`, then all correct processes deliver `m` - **independently of the sender**.

### Byzantine Consistent Broadcast

Specification:

- **Validity**: if a correct process `p` broadcasts a message `m`, then every correct process eventually delivers `m`;
- **No duplication**: no message is delivered more than once by any correct process;
- **Integrity**: if a correct process `p` delivers a message `m` with sender `s`, and `s` is correct, then `m` was broadcast by `s`;
- **Consistency**: if a correct process `p` delivers a message `m`, then every correct process delivers `m`.

There are two algorithms:

- **Authenticated Echo Broadcast**: uses authenticated perfect links, but it requires exchanges a quadratic number of messages;
  - 1st round: sender disseminates msg to all processes;
  - 2nd round: processes echo the message to all processes;
  - Deliver only when received more than a **quorum of echoes**;
- **Signed Echo Broadcast**: uses **digital signatures** - costly, but more powerful than MACs, because it allows for **non-repudiation** (the sender cannot deny having sent the message);
  - Witnesses authenticate a request not by sending an echo, but by **signing the request**;
  - Sender collects a byzantine quorum of signed requests, and sends the message to all processes.

So **Byzantine quorums** are used:

- In the crash failure model, **majority** is enough (`N/2 + 1`) - intersection of two majorities is not empty;
- In Byzantine quorums, at least one **correct** process is guaranteed to be in the intersection of any two quorums;
  - Quorum of size - `Q`
  - Faulty processes - `f`
  - `N = 3f + 1` - number of processes
  - `Q = 2f + 1` - size of the quorum - generically, `Q = (N + f) / 2`

### Byzantine Reliable Broadcast

- **Stronger** than Byzantine Consistent Broadcast;
- Extends the authenticated echo broadcast algorithm, to a **double echo** algorithm;
  - 1st round: sender disseminates msg to all processes;
  - 2nd round: processes echo the message to all processes;
  - 3rd round: processes echo ready to all processes;
  - Deliver only when received more than a **quorum of echoes**.
- The first 4 properties are the same as the ones for the Byzantine Consistent Broadcast, with the addition of:
  - **Totality**: if some message is delivered by any correct process, every correct process eventually delivers a message.
    - Totality + Consistency are equivalent to Agreement of the Reliable Broadcast in the crash fault model.
