# Computer Hardware and Architecture Learning Roadmap

## Goal
Build a complete mental model of how computers work — from transistors and CPUs up through operating systems and compilers — so you can reason confidently about how software is built to run on real hardware.

---

# Part 1: Learning Layers

## Layer 1: Computer Fundamentals

**Goal:** Understand how a computer executes code at the most basic level.

- What is a CPU? What are cores and threads?
- Binary, logic gates, and the ALU
- RAM vs storage — why the distinction matters
- The instruction cycle: fetch → decode → execute

**Key question to be able to answer:** If I write `x = 1 + 2` in any language, what does the CPU actually do?

---

## Layer 2: CPU Architecture

**Goal:** Understand why CPUs behave differently and what the microarchitecture decisions are.

- ISA (Instruction Set Architecture): x86 vs ARM, RISC vs CISC
- Clock speed vs IPC (Instructions Per Cycle) — why GHz isn't everything
- Multicore vs multithreading (physical vs logical cores)
- Cache hierarchy: L1, L2, L3 — why proximity to the core matters
- Pipelining and branch prediction — how CPUs stay busy

**Key question to be able to answer:** Why does a newer chip at 3.5 GHz beat an older chip at 4.0 GHz?

---

## Layer 3: Performance Concepts

**Goal:** Understand efficiency metrics and how to compare hardware meaningfully.

- TDP (Thermal Design Power) — heat as a proxy for power consumption
- Performance per watt — why this metric matters more than peak performance
- Bottlenecks — CPU-bound vs memory-bound vs I/O-bound workloads
- Parallelism — when more cores help and when they don't
- Process nodes (nm) — why transistor size affects power and density

**Key question to be able to answer:** Why does Apple Silicon deliver better battery life than Intel at similar performance levels?

---

## Layer 4: GPUs and Modern Chips

**Goal:** Understand how GPUs differ architecturally from CPUs and what modern SoC designs look like.

- CPU vs GPU: sequential vs massively parallel execution
- Shader cores, VRAM, and compute workloads
- Integrated vs dedicated GPUs — trade-offs
- SoC (System on Chip) — why putting everything on one die changes the performance equation
- AI accelerators (NPUs) — dedicated silicon for matrix operations
- Chiplets — why AMD and Intel moved from monolithic dies to modular designs

**Key question to be able to answer:** Why is a GPU good at training neural networks but overkill for running a web server?

---

## Layer 5: Assembly Language and Machine Code

**Goal:** Understand how high-level code maps to actual CPU instructions. This is where software meets hardware.

- What is an instruction? How does the CPU decode and execute it?
- Registers — the CPU's small, fast on-chip storage
- Stack frames — how function calls are managed in hardware
- Calling conventions — how functions pass arguments and return values
- How C, Rust, or Go code compiles down to machine instructions
- Reading disassembly — what your compiled program actually looks like

**Why this layer matters:** Until you've read assembly, your mental model of software running on hardware is incomplete. Assembly reveals exactly what the CPU executes — register moves, memory loads, branches — and makes the layers above (compilers, OS) immediately more legible.

**Key question to be able to answer:** What does a `for` loop look like in assembly? What does a function call look like?

**Tools:**
- **Compiler Explorer** (godbolt.org) — paste C/Rust/C++ code, see real assembly output. Change optimization flags (`-O0` vs `-O2`) and watch what changes.
- `gcc -S -O0 file.c` — output assembly from a C file

---

## Layer 6: Operating Systems — The Hardware Manager

**Goal:** Understand how the OS manages hardware resources on behalf of programs and how your code interacts with the kernel.

- **Privilege rings** — hardware-enforced protection levels; kernel runs in Ring 0, your code in Ring 3
- **System calls** — how user programs request OS services (open a file, allocate memory, create a process)
- **Virtual memory and paging** — the OS and MMU (hardware) give each process its own address space
- **Process scheduling** — how the OS decides which process runs on the CPU and when
- **Context switching** — how the CPU saves and restores program state when switching processes
- **Interrupts** — hardware signals (keyboard, timer, disk) that pause the CPU and hand control to the kernel
- **Device drivers** — kernel-level code that translates OS abstractions into hardware-specific commands

**Why this layer matters:** Every program you run makes system calls. `print()`, `malloc()`, file I/O — all of it goes through the OS which talks to hardware. Understanding this chain explains program behavior, performance anomalies, and security boundaries.

**Key question to be able to answer:** What actually happens when your Python script calls `open("file.txt")`? Trace it from Python down to hardware.

---

## Layer 7: Compilers and the Toolchain

**Goal:** Understand how source code becomes an executable binary and how the compiler's choices affect what the CPU runs.

**The compilation pipeline:**
```
Source code (.c / .rs / .py)
        ↓  Preprocessing
Expanded source
        ↓  Compilation
Assembly code
        ↓  Assembling
Object file (.o)
        ↓  Linking
Executable binary
        ↓  OS Loader
Loaded into virtual memory → CPU executes
```

- **Compiler optimizations** — inlining, loop unrolling, dead code elimination, vectorization (SIMD)
- **ABI (Application Binary Interface)** — calling conventions and data layout that make compiled code interoperate
- **Static vs dynamic linking** — trade-offs between self-contained binaries and shared libraries
- **LLVM IR / GCC GIMPLE** — intermediate representations between source code and assembly
- **Undefined behavior** — why it matters at the hardware level (the compiler assumes it never happens and optimizes accordingly)

**Key question to be able to answer:** Why does compiling with `-O3` make the same C code run 3x faster? What did the compiler actually change?

---

# Part 2: Resources

## YouTube Channels

### Foundational
- **Crash Course Computer Science** — Episodes 1–10; binary, logic, ALU, CPU basics
- **Sebastian Lague** — Visual, intuition-focused computer science
- **Branch Education** — CPU internals and GPU architecture; animated and excellent
- **Techquickie** — Quick explanations of cores, threads, TDP

### Intermediate
- **Low Level Learning** — Assembly, systems programming, and hardware-software bridge
- **Ben Eater** — Build a CPU and computer from scratch on a breadboard
- **Computerphile** — Architecture concepts with academic depth

### Advanced
- **Gamers Nexus** — Rigorous CPU/GPU hardware reviews and benchmark methodology
- **Hardware Unboxed** — GPU architecture and benchmark comparisons
- **Asianometry** — Semiconductor industry, chip design, and manufacturing deep dives
- **Digital Foundry** — GPU and console hardware analysis

---

## Books

### Beginner
- **"Code: The Hidden Language of Computer Hardware and Software"** — Charles Petzold. Builds computers from first principles. The best starting book for this subject.

### Intermediate
- **"Computer Organization and Design"** — Patterson & Hennessy. The standard university textbook on CPU architecture and the hardware-software interface.
- **"Operating Systems: Three Easy Pieces" (OSTEP)** — Arpaci-Dusseau. Free at ostep.org. The best OS textbook available. Covers virtualization, memory, concurrency, and persistence.

### Advanced
- **"Computer Systems: A Programmer's Perspective" (CS:APP)** — Bryant & O'Hallaron. Used at Carnegie Mellon. Covers machine code, memory hierarchy, linking, OS interaction, and more from the programmer's view.
- **"Modern Processor Design"** — John Paul Shen. Deep microarchitecture.
- **"The Linux Programming Interface"** — Michael Kerrisk. The definitive reference for Linux system calls and OS interaction.

---

## Websites and Articles

### Beginner
- **HowStuffWorks** — "How CPUs Work" — accessible introduction
- **GeeksforGeeks** — cache, pipelining, architecture basics

### Intermediate / Advanced
- **Chips and Cheese** (chipsandcheese.com) — microarchitecture deep dives on real chips
- **AnandTech archives** — gold-standard CPU reviews and architecture analysis (archived; site closed 2023)
- **OSDev Wiki** (wiki.osdev.org) — OS internals reference

### Tools
- **Compiler Explorer** (godbolt.org) — see what the compiler generates from your source code
- **Intel ARK** (ark.intel.com) — official CPU specification database
- **CPU Monkey / PassMark** — CPU comparison tools

---

# Part 3: Key Concepts to Master

| Concept | Layer |
|---|---|
| Fetch-decode-execute cycle | 1 |
| Core vs thread | 2 |
| Clock speed vs IPC | 2, 3 |
| Cache hierarchy (L1/L2/L3) | 2 |
| TDP and performance per watt | 3 |
| x86 vs ARM, RISC vs CISC | 2 |
| CPU vs GPU (serial vs parallel) | 4 |
| Registers and stack frames | 5 |
| Reading assembly output | 5 |
| System calls and privilege rings | 6 |
| Virtual memory and paging | 6 |
| Compilation pipeline | 7 |
| Compiler optimization flags | 7 |
| Static vs dynamic linking | 7 |

---

# Part 4: Practical Exercises

## Inspect your CPU
```bash
lscpu
cat /proc/cpuinfo | head -30
```

## Monitor performance
```bash
htop
# or
top
```

## See what assembly the compiler generates
```bash
gcc -O0 -S -o output.s input.c   # unoptimized
gcc -O2 -S -o output_opt.s input.c   # optimized
diff output.s output_opt.s
```

## Trace system calls
```bash
strace ls -la
```

## Inspect your process's virtual memory layout
```bash
cat /proc/self/maps
```

## Disassemble a binary
```bash
objdump -d /bin/ls | head -80
```

## Run a CPU benchmark
```bash
sysbench cpu run
```

---

# Part 5: Intel vs AMD vs Apple Silicon

## Architecture Comparison

| Feature | Intel | AMD | Apple Silicon |
|--------|------|-----|---------------|
| ISA | x86 (CISC) | x86 (CISC) | ARM (RISC) |
| Design Focus | High single-core performance | Multi-core performance | Performance per watt |
| Manufacturing | Intel fabs | TSMC | TSMC |
| Memory | Discrete DRAM | Discrete DRAM | Unified memory (on-package) |
| Typical Use | General purpose, gaming | Multithreaded workloads, servers | Mobile, laptops |

## Intel (Core i5, i7, i9)
- Strong single-core performance; high clock speeds
- Hybrid architecture (P-cores + E-cores in recent generations)
- Higher TDP; more heat output

## AMD (Ryzen)
- High core counts; strong multi-threading
- Chiplet design (separate compute and I/O dies)
- Good price-to-performance; strong server presence (EPYC)

## Apple Silicon (M-series)
- ARM-based (RISC); unified memory on the SoC package
- Extremely high performance per watt
- Tight hardware-software co-optimization (macOS + Apple toolchain)
- No discrete GPU option; not upgradeable

## Choosing Based on Workload

| Workload | Best Choice | Why |
|---|---|---|
| Gaming / single-threaded | Intel or AMD | High clock speeds, mature x86 ecosystem |
| Rendering / VMs / servers | AMD | High core count, competitive price |
| Battery-critical laptop | Apple Silicon | Industry-leading performance per watt |
| ML / AI training | GPU (NVIDIA) + AMD/Intel CPU | CUDA ecosystem dominance |
| Embedded / mobile | ARM (RISC-V increasingly) | Power efficiency |

---

# Part 6: Suggested Learning Plan

## Week 1 — Foundation
- Watch Crash Course Computer Science episodes 1–10
- Read *Code* by Petzold (chapters 1–10)
- Goal: understand what a CPU is and how it executes instructions

## Week 2 — CPU Architecture
- Study cache, IPC, cores, pipelining
- Compare CPU specs using Intel ARK and CPU Monkey
- Watch Branch Education's CPU internals videos
- Goal: explain why two CPUs at the same GHz perform differently

## Week 3 — Performance and Modern Chips
- Study TDP, Apple Silicon vs Intel/AMD
- Learn GPU fundamentals (Branch Education GPU video)
- Read one archived AnandTech CPU review end-to-end
- Goal: read a spec sheet and extract meaning

## Week 4 — Assembly and Machine Code
- Start nand2tetris (Part I at minimum)
- Use Compiler Explorer daily: write C, read the assembly output
- Learn what registers, stack frames, and calling conventions are
- Goal: recognize common patterns in disassembled code

## Week 5 — Operating Systems
- Begin OSTEP (start with virtualization chapters)
- Run `strace` on common programs; read the output
- Inspect `/proc/self/maps` in a running process
- Goal: explain what happens between `main()` and the CPU

## Week 6 — Compilers and the Full Stack
- Read CS:APP Chapter 3 (machine-level representation)
- Compare `-O0` vs `-O2` compiler output for the same program
- Run `ldd` on a binary; understand static vs dynamic linking
- Goal: trace the path from source code to CPU instruction

---

# Final Notes

Mastering these layers makes you a fundamentally different kind of engineer or analyst. You stop treating the computer as a black box and start reasoning about:

- **Why code is fast or slow** (not just profiling output, but understanding what the hardware is doing)
- **How security vulnerabilities work at the hardware level** (buffer overflows, speculative execution attacks like Spectre/Meltdown)
- **How to debug low-level behavior** (system calls, memory layout, compiler artifacts)
- **How to write systems software** (OS kernels, device drivers, compilers, virtual machines)

The path from transistors to running Python is long, but every layer is learnable. Build the mental model one layer at a time.
