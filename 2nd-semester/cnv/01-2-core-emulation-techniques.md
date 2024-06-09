# Core Emulation Techniques

- **Emulation** is the process of mimicking the outwardly observable behavior to match an existing target system;
  - **Implementing the interface/behavior of a system on a system with a different interface/behavior**;
  - **Simulation** is different from emulation because it models the internal state of the system, and not the full behavior;
- **Instruction set emulation** is a key aspect of many virtual machine implementations;

  - **Host** - host hardware and target ISA - **implements the emulation**;
  - **Guest** - source ISA;

- **Interpretation** is the simplest form of emulation, where the source code is transformed into an **intermediate form** and executed by the host system;
  - **Decode-and-dispatch interpreter**;
  - **Threaded interpretation**;
    - **Indirect threaded interpretation**
    - **Basic pre-decoding**
    - **Direct threaded interpretation**;
- **Translation** is a technique to convert the binary code of a program from one ISA to another, instead of having intermediate code - **remove interpretation overhead**;
  - **Simple binary translation**;
  - **Incremental pre-decoding and translation**;
  - **Control transfer optimization**.

## Basic Interpretation

- **Interpretation** is the simplest form of emulation;
- **Decode-and-dispatch interpreter**:
  - For each instruction:
    - **Fetch** - read the instruction from memory;
    - **Decode** - determine the operation to be performed and the operands;
    - **Dispatch** - dispatch it to an **interpreter routine** based on the **type of instruction**;
  - ✅ Low memory requirements - zero **start-up time**;
  - ✅ Easy to implement;
  - ❌ Slow **steady-state performance**;
  - ❌ Inefficient control flow - **branching** and **dispatching** for each instruction - **slower execution**.

> ### Terms
>
> - **Branching** - the process of selecting a path of execution from multiple possibilities;
> - **Dispatching** - the process of sending an instruction to the appropriate interpreter routine.
> - **Start-up**: cost of emulating instructions for the **first time**;
> - **Steady-state**: cost of emulating instructions after the first time - **emulation long-term rate**.

---

## Threaded Interpretation

- **Threaded interpretation** is a variation of basic interpretation;

### Indirect Threaded Interpretation

- Idea is to **avoid inefficient control flow**;
- **Reduce number of branches** by **duplicating decode-dispatch** code, at every instruction - **append dispatch code** to the end of each instruction;
- ✅ Low memory requirements (but higher than decode-and-dispatch);
- ✅ Zero start-up time;
- ❌ **Steady-state performance is still** slow, but better than basic interpretation;
  - Still requires analysis of each instruction, and employs indirect branch and access to centralized table.

### Basic Pre-decoding

- Why repetitive instruction analysis?
- **Parses** and instruction and **saves** it in an intermediate form;
- **Dispatch table** - **indexed by opcode**;
- ✅ Speeds up execution;
- ❌ Higher memory requirements.

### Direct Threaded Interpretation

- **Direct threaded interpretation** is a further optimization of basic pre-decoding;
- **Replace instruction code** with actual **address of interpreter routine** - dispatch table is not needed;
- ✅ Greater speed-up;
- ❌ Portability issues - **instruction set changes** require **recompilation**.

---

## Binary Translation

- **Binary translation** is a technique to **convert** the **binary code** of a program from one **ISA** to another, instead of having intermediate code - **remove interpretation overhead**;

<p align="center">
  <img src="./imgs/predecoding_vs_binary_translation.png" alt="Pre-decoding vs Binary Translation" width="500px">
  <br>
  <em>Pre-decoding vs Binary Translation</em>
</p>

- **Simple binary translation**:
  - Map each **source ISA instruction** to its own **customized target ISA code** - **execute** the **translated code** directly on the host;
  - One-by-one instruction translation;
  - Always loads and saves registers from context block;
- **(Register) State-mapping**: avoid constant access to register context in memory;
  - Remember which target registers are mapped to which host registers;
- **Incremental pre-decoding and translation**:
  - Load source code in memory;
  - Initially **interpret** (decode-and-dispatch, indirect threaded, etc.);
  - **Dynamically** perform **translation**:
    - **Translate** basic blocks and **cache** them;
    - **Lookup SPC->TPC** mapping (source PC to target PC);
  - When **branch found**, basic block completed;
  - Execute **next basic block** (if absent, translate it and update SPC->TPC mapping);
  - Eventually, **all code is translated**.
- **Control transfer optimization**:
  - **Translation chaining** - link translated blocks, avoiding indirect transfers among blocks via Emulation Manager;

|                              | **Decode-and-Dispatch** | **Indirect Threaded Interpretation** | **Basic Pre-decoding** | **Direct Threaded Interpretation** | **Binary Translation** |
| ---------------------------- | ----------------------- | ------------------------------------ | ---------------------- | ---------------------------------- | ---------------------- |
| **Memory Requirements**      | ✅Low                   | ✅Low                                | ❌High                 | ❌High                             | ❌High                 |
| **Start-up Performance**     | ✅Fast                  | ✅Fast                               | ❌Slow                 | ❌Slow                             | ❌Very Slow            |
| **Steady-state Performance** | ❌Slow                  | ❌Slow                               | ✅Medium               | ✅Medium                           | ✅Fast                 |
| **Code Portability**         | ✅High                  | ✅High                               | ✅High                 | ✅Medium                           | ❌Low                  |

> - **Static basic block**: a sequence of instructions with a single entry point and a single exit point;
>   - Begin and end with control transfer instructions;
> - **Dynamic basic block**: determined by actual flow of control at runtime;
