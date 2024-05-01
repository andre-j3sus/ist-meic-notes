# High-Level Language VMs

## Overview

* Different from **traditional process VMs**;
* **Portability** as a primary goal;
* Design a new **guest-ISA** that is independent of the host-ISA;
  * **Virtual ISA** is a **high-level language**;
  * Free of implementation-specific details;
  * Defined for user programs, and not real hardware;
  * Includes:
    * Instruction set;
    * Meta-data;
    * **Data set architecture**;
* Consolidates features easily supported by most OS;
* Supports features of **high-level languages**.

### Pascal P-Code VM

* **Pascal** was a popular language in the 70s;
* **P-Code** was a **stack-based** VM - similar to **Java**;
* Requires **only one** Pascal compiler for all platforms;
* Its **much simpler** to implement a **P-Code interpreter** than a **Pascal compiler** from scratch.
* P-Code Instruction Set:
  * Op-code and offset with operand relative location;
  * **Stack-based** - push and pop values from/to the stack;
  * **ALU** operations operate on the top of the stack;
  * **Typed instructions** - type information is stored in the instruction.

---

## Java Virtual Machine (JVM)

* The Java virtual-ISA is composed of:
  * **Bytecode** instructions;
  * Set of pre-defined **data types**;
  * **Metadata** - data definitions and inter-relationships (formalized in the **class file** format);
* **No explicit registers** in bytecode leads to **simpler code**;
* Minimal OS dependence through libraries;
* Metadata allows **verification/interoperability**;

### Data Types

* **Primitive types**: `int`, `float`, `double`, `long`, `byte`, `short`, `char`, `boolean`;
* **Reference types**: hold references to objects or null;
* Objects carry data declared by classes;
  * **Array** is a special kind of object with **ISA support**.
* Avoid implementation-specific details:
  * **No fixed representation or sizes for data, only ranges**;
  * **No direct data addressing** - all data is accessed through **references**.

### Data Storage

* For each **running thread**:
  * Implicit **VM registers** - **PC**, **stack pointer**, **frame pointer**;
  * **Stack** - **typed**:
    * On method invocation, a new **frame** is allocated on the stack;
    * **Arguments** and **local variables** are stored in the frame (argument 0 is `this` for instance methods);
    * **Operand stack** - used to pass arguments and return values;
* **Type and Method area**:
  * One method area per JVM;
  * Analogous to `text/code` segment in a process VM;
  * **Type definitions** - class and interface definitions;
  * **Method tables** - method definitions;
* **Constant pool**:
  * Collection of all symbolic data within a class;
  * Instructions often need constants;
  * **Pooling advantages**: allows sharing/saving space; smaller code footprint; fixed pre-determined size for each instruction type;
* **Global data**:
  * **Heap** - objects and arrays;
    * Only one heap per java application (all threads share it);
    * **Garbage collection** is used to manage the heap;
  * **Class file contents** - class definitions, method bodies, static fields, **constant pool** (holds immediate values and references).

### Instruction Set

* **Opcode byte + 0 or more operands**;
* Operands fetched from the **constant pool**, **local variables**, or **stack**;
* Each **primitive type** has its own set of **instructions**;
  * `iadd`(int), `fadd`(float), `dadd`(double), `ladd`(long), ...
* Types of instructions:
  * **Data-movement instructions**:
    * Pushing constants onto the stack - `iconst_0`, `iconst_1`, ...
    * Moving values between the stack and local variables - `aload_0`, `istore_1`, ...
    * Via constant pool - `ldc`, `ldc_w`, `ldc2_w`;
    * Stack manipulation - `dup`, `swap`, `pop`;
  * **Functional instructions**:
    * Arithmetic, logical, and comparison operations;
  * **Control-flow instructions**:
    * Jumps, conditional jumps - `goto`, `if_icmpeq`, `if_acmpne`, ...
    * Switches - `tableswitch` (used for `switch` statements with ranges), `lookupswitch` (used for `switch` statements with arbitrary values);
    * Method invocation - `invokevirtual` (indexes constant pool for method reference, check if arguments match, allocate stack frame of appropriate size, and jump to the method);
    * `invokestatic`, `invokespecial` (constructors, private methods, or methods of superclass), `invokeinterface`;
    * Method return - `ireturn`, `freturn`, `dreturn`, `lreturn`, `areturn`, `return` (void return) - pops the stack frame and returns to the caller;
  * **Others**:
    * Object creation - `new` (creates a new object and pushes a reference to it onto the stack) - proper creation requires a constructor call;
    * Field access - `getfield`, `putfield`, `instanceof`, `checkcast`;
    * Array access, synchronization, exception handling;
* All data movement is done through the **stack**.

<p align="center">
    <img src="imgs/jvm-data-movement.png" alt="JVM Data Movement" width="500"/>
</p>

Example of object creation and method invocation:

```bytecode
new #1 // creates a new object of type #1 (java.lang.Object) and pushes a reference to it onto the stack

dup // duplicates the top value on the stack

invokespecial #4 // invokes the constructor of the object (java.lang.Object.<init>)

areturn // pops the stack frame and returns the reference to the caller
```

### Exceptions and Errors

* `athrow` instruction is used to throw an exception;
* **Exception table** is used to map **PC ranges** to **exception handlers**, specifying:
  * **Address range** of the code that can throw the exception;
  * **Target address** of the exception handler;
  * **Type of exception** to catch;
* Table defined in the class file.

### Java Threads

* Synchronization through **monitors** is supported: `synchronized` method;
* `monitorenter` and `monitorexit` instructions;
* Each object has a **monitor** associated with it;
* An exception table is implicitly created when a `synchronized` block is used.