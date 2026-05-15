# Computer Architecture, Hardware, and Software Fundamentals

A structured learning repository for understanding how computers work — from transistors and CPU microarchitecture through operating systems and compilers — and how software is built to run on real hardware.

## What This Covers

Most programming education stops at "how to use a language." This repository goes deeper: how the language you write gets turned into instructions, how the CPU executes those instructions, how the operating system manages the hardware, and why hardware design decisions (cache sizes, pipeline depth, ISA choice) affect every program you run.

The goal is to build a mental model that spans the full stack:

```
High-level code (Python, Rust, C)
        ↓  Compiler / Interpreter
Assembly / Machine code
        ↓  CPU Microarchitecture
Pipelines, caches, branch predictors
        ↓  Hardware
Transistors, memory cells, buses, interconnects
```

The OS sits alongside all of this — managing hardware on behalf of programs by virtualizing the CPU, virtualizing memory, and abstracting hardware devices through drivers.

---

## Roadmaps

### [Hardware Learning Roadmap](./roadmap/hardware_learning_roadmap.md)
A phase-based learning path from first principles to advanced topics. Covers:
- **Phase 1–4:** CPU foundations, microarchitecture, performance concepts, GPU fundamentals, and chip comparisons (Intel vs AMD vs Apple Silicon)
- **Phase 5:** Assembly language and machine code — how high-level code maps to hardware instructions
- **Phase 6:** Operating systems — privilege rings, system calls, virtual memory, scheduling, interrupts, and device drivers
- **Phase 7:** Compilers and the toolchain — how source code becomes an executable, optimization passes, linking, and ABI
- **Phase 8–9:** Applied understanding and emerging topics (chiplets, NPUs, RISC-V, heterogeneous computing)

Includes curated resources (books, YouTube channels, web tools), practical exercises, and the key question each phase should let you answer.

### [Computer Hardware Learning Roadmap](./roadmap/computer_hardware_learning_roadmap.md)
A complementary layer-by-layer guide organized around concepts rather than phases. Useful as a reference alongside the phase-based roadmap. Covers the same ground with:
- 7 conceptual layers from computer fundamentals through compilers
- Comparison tables for Intel vs AMD vs Apple Silicon
- A 6-week suggested learning plan
- Practical shell exercises for each concept
- Curated books, channels, and tools organized by level

---

## Core Concepts

### Hardware
- The instruction cycle (fetch → decode → execute)
- CPU microarchitecture: pipelines, caches, branch prediction, out-of-order execution
- ISA: x86 vs ARM, RISC vs CISC
- Performance metrics: IPC, TDP, process nodes, performance-per-watt
- GPU architecture: parallel execution, VRAM, compute vs graphics workloads
- Modern chip design: SoCs, chiplets, NPUs, unified memory

### Software-Hardware Interface
- Assembly language: registers, stack frames, calling conventions
- How compilers translate source code to machine instructions
- Compiler optimizations and what `-O2` / `-O3` actually change
- Operating system internals: privilege rings, system calls, virtual memory
- Process scheduling and context switching
- Device drivers and hardware interrupts
- Static vs dynamic linking; ABI; the loader

---

## Where to Start

**If you are new to this topic:** Start with Phase 1 of the [Hardware Learning Roadmap](./roadmap/hardware_learning_roadmap.md). Watch Crash Course Computer Science episodes 1–10 and read Charles Petzold's *Code*.

**If you understand hardware basics but not the software side:** Start at Phase 5 (Assembly) of the hardware roadmap. Use [Compiler Explorer](https://godbolt.org) alongside whatever you're reading — paste code, see real assembly output, and change optimization flags to understand what the compiler does.

**If you want a quick reference:** Use the [Computer Hardware Learning Roadmap](./roadmap/computer_hardware_learning_roadmap.md) as a concept index with the 6-week plan as a guide.

---

## Key Tools

| Tool | What it's for |
|---|---|
| [Compiler Explorer](https://godbolt.org) | See what assembly the compiler generates from your source code |
| [nand2tetris](https://nand2tetris.org) | Build a working computer from NAND gates; the best hands-on foundation |
| [OSTEP](https://ostep.org) | Free OS textbook — the best treatment of how operating systems manage hardware |
| [Intel ARK](https://ark.intel.com) | Official CPU specification database |
| `strace` (Linux) | Trace the system calls your program makes |
| `objdump -d` | Disassemble any compiled binary |
| `gcc -S` | Output assembly from a C file |

---

## Recommended Books

| Level | Book |
|---|---|
| Beginner | *Code: The Hidden Language of Computer Hardware and Software* — Petzold |
| Intermediate | *Computer Organization and Design* — Patterson & Hennessy |
| Intermediate | *Operating Systems: Three Easy Pieces* — Arpaci-Dusseau (free online) |
| Advanced | *Computer Systems: A Programmer's Perspective* — Bryant & O'Hallaron |
| Advanced | *The Linux Programming Interface* — Kerrisk |
