# System Virtualization

* **Virtual machine monitor (VMM)** or **hypervisor** is a layer of software that sits **between the hardware and the virtual machines**; performs the following activities:
  * **Resource management** - **schedule** and **manage allocation of HW resources** to multiple VMs (each one executing their own OS - **guest OS**);
  * Point of **control** for **shared physical resources**;
  * VMM should have **top privileges**, while the guest OS should have **lower privileges**, but at the same time it **believes** it has full control over the system.
* Approach for **resource control** in system VMs:
  * VMM intercepts all **privileged instructions**;
  * VMM intercepts all **accesses to privileged resources**;
    * **traps-and-emulates** - VMM traps the instruction and emulates it.

## Processor Virtualization

* Execute **guest OS expected functionality** in each guest VM;
* Prevent any guest VMs from **controlling the entire system**;
* With **identical guest/host ISAs**, a **significat fraction of intructions** can be **executed directly** on native hardware, and only a **small fraction** needs to be **emulated**;
* There are two types of ISA:
  * **well-behaved**:
    * Efficiently virtualized;
    * Trap occurs naturally when an instruction needs to be emulated;
  * **ill-behaved**:
    * Difficulty in isolating instructions needing emulation (**code discovery**);

### ISA Virtualizability

* Conditions for **ISA Virtualizability**:
  * Processor and **uniformly** addressable memory;
  * Processor operates in two modes:
    * **system mode** - VMM - **only** VMM performs resource control;
    * **user mode** - guest OS;
  * Memory addressing is done **relative** to contents of a relocation register;
* VMM Components:
  * **Dispatcher** - **dispatches** control to the appropriate **handler**;
  * **Allocator** - **allocates** resources to the VMs;
  * **Interpreter routines** - **emulate** the **privileged instructions**;

> In a **well-behaved ISA**, the **trap-and-emulate** technique is used to **trap** the instructions to the **dispatcher**, which calls **allocator** or **interpreter routines**.

* Types of instructions:
  * **Privileged instructions** - traps in user mode, and does not trap in system mode - all guest-OS code is forced to execute in user mode and traps to the VMM;
  * **Non-privileged instructions** - never traps, but my execute with reduced functionality;
  * **Sensitive instructions** - those that interact with the hardware:
    * **Control-sensitive** - attempt to change configuration of system resources;
    * **Behavior-sensitive** - behavior depends on the state of the system.;
  * **Innocuous instructions** - all innocuous instructions are executed directly by the hardware.

> **Theorem for ISA Virtualizability**: A VMM may be contructed if the set of **sensitive instructions** for the ISA is a subset of the set of **privileged instructions**.

* **Critical instructions** - those that are **sensitive** yet **not privileged**;
* Popek and Goldberg assume all non-privileged instructions are executed natively - **problem because of critical instructions**;
* The solution is an **hybrid virtual machine system**:
  * Some non-privileged instructions must be specially emulated - **critical instructions**;
  * Using techniques related to **binary translation**, the VMM intercepts and scans the guest code for critical instructions, and **replaces** them with **explicit traps** to the VMM.

---

## Memory Virtualization

* VMM is responsible for **mapping guest physical addresses** to **host physical addresses**, while the guest OS believes it is in direct control of the physical memory;
* **Real memory** is the **physical memory** of guest OS;
  * **Virtual Address** -> **Real Address** -> **Physical Address**;
  * VMM maintains **real map table**, which maps **real addresses** to **physical addresses**;

_to be continued..._

---

## Paravirtualization and Hardware Support

To improve **performance**:

* **Paravirtualization** - guest OS has **knowledge** of the VMM, and **cooperates** with it;
  * Eliminates the need for complex virtualization: code detection/discovery, code patching and shadow table maintenance;
* **Hardware support** - **hardware extensions** to **improve virtualization**;
  * New CPU modes, instructions, and memory management units;