# Computer Architecture and Hardware Learning Roadmap

A structured path from zero to deeply understanding CPUs, GPUs, operating systems, compilers, and how software is built to run on real hardware.

---

## Phase 1 — Foundation

**Goal:** Build a mental model of what a CPU actually is — the fetch-decode-execute cycle, clock speed, cores basics, binary and logic gates.

### Resources

- **Crash Course Computer Science** (YouTube) — Episodes 1–10. Covers binary, logic gates, the ALU, and how a CPU fetches and executes instructions. Free, well-paced, and the perfect starting point.
- **"How a CPU Works"** by In One Lesson (YouTube) — A single 20-minute video that gives you the mental model you need before going deeper.
- **Sebastian Lague's "How Do Computers Work?" series** (YouTube) — More visual and intuition-focused. Great complement to Crash Course.
- **nand2tetris Part I** (nand2tetris.org) — Build a working computer from NAND gates up to a CPU. Hands-on and irreplaceable for building genuine intuition.

---

## Phase 2 — Architecture Deep Dive

**Goal:** Understand ISA (Instruction Set Architecture), microarchitecture, caches, pipelines, and branch prediction — the building blocks that explain *why* chips are designed the way they are.

### Resources

- **"Modern Microprocessors — A 90 Minute Guide"** by Jason Robert Carey Patterson — A free web article (search the title). One of the best overviews of CPU microarchitecture ever written. Read this early in Phase 2.
- **Computer Organization and Design** by Patterson & Hennessy (textbook) — Focus on:
  - Chapter 1 — Computer Abstractions and Technology
  - Chapter 4 — The Processor
  - Chapter 5 — Memory Hierarchy
- **Chips and Cheese** (chipsandcheese.com) — Deep but accessible technical teardowns of real CPU and GPU architectures. Bookmark this early.

---

## Phase 3 — Performance Concepts

**Goal:** Understand TDP, IPC, process nodes (nm), performance-per-watt, efficiency cores, and how to read a spec sheet meaningfully.

### Key concepts

| Concept | What it means |
|---|---|
| **TDP** (Thermal Design Power) | The maximum heat a chip generates under load — relates to power draw and cooling needs |
| **IPC** (Instructions Per Clock) | How much work a core does per clock cycle — more important than raw GHz |
| **Process node (nm)** | The size of transistors on a chip — smaller = more transistors, better efficiency |
| **Efficiency cores vs performance cores** | Hybrid designs (like Intel's P/E cores) balance throughput and power draw |

### Resources

- **"TDP Explained"** by Hardware Unboxed (YouTube) — Focused, practical breakdown of exactly that concept.
- **"Why More Cores Isn't Always Better"** (YouTube) — Search the phrase; Linus Tech Tips and Digital Foundry have solid versions.
- **AnandTech archives** (anandtech.com) — The site shut down in 2023 but archives remain. Their CPU reviews are the gold standard for learning to read benchmarks.

---

## Phase 4a — CPU Comparison: Intel vs AMD vs Apple Silicon

**Goal:** Apply your architecture knowledge to real products. Understand why a Core i7 outperforms a Core i5, what AMD's Zen architecture changed, and why Apple Silicon performs the way it does.

### Resources

- **"Why Apple Silicon is So Fast"** by Branch Education (YouTube) — The single best video on unified memory architecture and M-series chip design.
- **"AMD vs Intel — Architecture Differences Explained"** by Gamers Nexus (YouTube) — Rigorous and doesn't oversimplify.
- **AnandTech's M1 Architecture Analysis** (archived) — Search "Anandtech M1 architecture analysis". Long read but thorough.
- **"x86 vs ARM — What's the Difference?"** by Low Level Learning (YouTube) — Great for the deeper architectural questions behind why different chip families exist.

### Things you should be able to explain after this phase

- Why a Core i7 beats a Core i5 (hint: it's not just clock speed)
- What "Zen 4" or "Raptor Lake" means when AMD/Intel name a generation
- Why Apple Silicon has unified memory and what that actually changes
- What an efficiency core is and why it exists

---

## Phase 4b — GPU Fundamentals

**Goal:** Understand how GPUs differ from CPUs architecturally, what makes them suited for parallel workloads, and how to compare GPU generations.

### Resources

- **"How GPUs Work"** by Branch Education (YouTube) — Animated, visual, and excellent. Watch this before anything else on GPUs.
- **"NVIDIA vs AMD GPU Architecture"** by Hardware Unboxed (YouTube) — Explains how they handle shaders, ray tracing, and memory differently.
- **"A100 vs H100 — What Changed?"** type videos — Helps you understand generational GPU leaps, especially relevant for AI/ML workloads.

### Key GPU concepts to understand

- **CUDA cores vs shader processors** — NVIDIA vs AMD terminology for the same basic unit
- **VRAM** — Why it matters and when you hit its limits
- **Parallel vs serial execution** — The core architectural difference between a GPU and CPU
- **Compute workloads vs graphics workloads** — Why a GPU good at gaming isn't necessarily ideal for AI training

---

## Phase 5 — Assembly Language and Machine Code

**Goal:** Read and write basic assembly. Understand how high-level constructs (loops, function calls, conditionals) map to real hardware instructions. This is the layer where software meets silicon.

### Why this phase matters

Until you read assembly, you're reasoning about software on hardware in the abstract. Assembly forces you to see exactly what the CPU is executing: register manipulation, stack frames, memory addressing, calling conventions. It permanently changes how you read and write code.

### Key concepts

| Concept | What it means |
|---|---|
| **Registers** | The CPU's tiny, fast on-chip storage — where actual computation happens |
| **Stack and heap** | Memory regions managed by the OS and program; the stack is directly manipulated by assembly instructions |
| **Calling conventions** | The agreed-upon rules for how functions pass arguments and return values (cdecl, System V ABI, etc.) |
| **Instruction encoding** | How a human-readable instruction (e.g. `mov rax, 1`) becomes bytes the CPU reads |
| **Disassembly** | Reading the assembly output of compiled code — the reverse of writing it |

### Resources

- **nand2tetris Part II** (nand2tetris.org) — Write an assembler and compiler for the machine you built in Part I. The best way to understand how assembly relates to machine code.
- **"x86 Assembly Language"** by Open Security Training (opensecuritytraining.info or YouTube) — Thorough, focused on real x86-64. Look for the Xeno Kovah lectures.
- **Compiler Explorer** (godbolt.org) — Paste C/C++/Rust code and see the assembly output in real time. Essential for building the mental model of compiler output. Use it constantly throughout this phase.
- **"Assembly Language for x86 Processors"** by Kip Irvine — A textbook commonly used in university courses. Good for structured learning.
- **"Low-Level Programming"** by Igor Zhirkov — Bridges C, assembly, and operating systems in one book.

### Practical exercises

```bash
# Inspect assembly output of a C file (GCC)
gcc -O0 -S -o output.s input.c

# Inspect assembly with optimization enabled (compare the output)
gcc -O2 -S -o output_opt.s input.c

# Disassemble a compiled binary
objdump -d ./my_binary | less

# On Linux: inspect what system calls a program makes
strace ./my_binary
```

---

## Phase 6 — Operating Systems and the Software-Hardware Bridge

**Goal:** Understand how the OS manages hardware on behalf of programs — privilege rings, system calls, virtual memory, process scheduling, and device drivers. This is where "running software" actually happens at the hardware level.

### Key concepts

| Concept | What it means |
|---|---|
| **Privilege rings (Ring 0 / Ring 3)** | Hardware-enforced protection levels — kernel code runs in Ring 0, user programs in Ring 3 |
| **System calls** | The mechanism by which user-space programs request services from the kernel (read, write, fork, mmap) |
| **Virtual memory and paging** | The OS gives each process its own address space; the MMU (hardware) translates virtual to physical addresses |
| **Context switching** | How the CPU saves and restores program state when switching between processes |
| **Interrupts** | Hardware signals that pause the CPU and transfer control to the OS (e.g., keyboard input, timer, disk ready) |
| **Device drivers** | Kernel-level code that translates OS abstractions into hardware-specific commands |

### Why this phase matters

Every program you run — no matter the language — ultimately makes system calls. When you call `open()`, `malloc()`, or `print()`, you're invoking the OS which talks to hardware. Understanding this chain removes the magic from software behavior and makes debugging, performance tuning, and security reasoning far more precise.

### Resources

- **"Operating Systems: Three Easy Pieces" (OSTEP)** by Arpaci-Dusseau (ostep.org) — Free online. The best OS textbook in existence. Read the sections on virtualization (CPU and memory) first, then concurrency, then persistence. Focused, practical, and excellent.
- **"Computer Systems: A Programmer's Perspective" (CS:APP)** by Bryant & O'Hallaron — Chapter 8 (Exceptional Control Flow) and Chapter 9 (Virtual Memory) are especially relevant for this phase. This book is how Carnegie Mellon teaches systems programming.
- **"Linux Kernel Development"** by Robert Love — A readable introduction to how the Linux kernel actually works internally. Good after OSTEP.
- **"How Linux Works"** by Brian Ward — More accessible than Love's book. Good for understanding what happens from boot to running a user program.
- **OSDev Wiki** (wiki.osdev.org) — The reference for anyone building or studying OS internals. Extremely detailed.
- **"Writing a Simple OS from Scratch"** by Nick Blundell (free PDF, search title) — A practical walkthrough of building a bootloader and basic kernel in x86 assembly and C.

### Practical exercises

```bash
# Trace system calls of any program
strace ls -la

# Inspect the virtual memory map of a running process
cat /proc/$(pgrep firefox)/maps

# View hardware interrupts in real time
watch -n 1 cat /proc/interrupts

# Inspect kernel boot messages (hardware detection)
dmesg | head -100

# See page faults and context switches for a process
pidstat -w -d 1
```

---

## Phase 7 — Compilers and Code Generation

**Goal:** Understand how source code becomes machine instructions — the full compilation pipeline, how compilers optimize, and how language choice and code structure affect what the CPU actually executes.

### Why this phase matters

The compiler is the bridge between the software you write and the hardware it runs on. Understanding what the compiler does (and doesn't do) explains why certain code patterns are faster, how compiler flags change behavior, why different languages have different performance profiles, and what "undefined behavior" in C means at the hardware level.

### The compilation pipeline

```
Source code (.c / .rs / .cpp)
        ↓
Preprocessing (macros, includes)
        ↓
Compilation (source → assembly)
        ↓
Assembling (assembly → object file .o)
        ↓
Linking (object files + libraries → executable)
        ↓
Loading (OS maps executable into memory, hands off to CPU)
```

### Key concepts

| Concept | What it means |
|---|---|
| **IR (Intermediate Representation)** | An internal compiler format between source and assembly — LLVM IR, GCC GIMPLE |
| **Optimization passes** | Transformations the compiler applies: inlining, loop unrolling, dead code elimination, vectorization |
| **ABI (Application Binary Interface)** | The calling convention and data layout rules that allow compiled code from different sources to interoperate |
| **Linking (static vs dynamic)** | Static links library code into the binary; dynamic loading happens at runtime via shared libraries (.so / .dll) |
| **Vectorization (SIMD)** | Compiler uses SIMD instructions (SSE, AVX, NEON) to process multiple data elements in one instruction |

### Resources

- **Compiler Explorer** (godbolt.org) — Continue using this from Phase 5. Now observe how `-O0` vs `-O2` vs `-O3` changes the output. Compare Clang vs GCC vs MSVC output for the same source.
- **"CS:APP" Chapters 3 and 7** — Chapter 3 (Machine-Level Representation of Programs) is one of the best treatments of how C maps to x86-64. Chapter 7 covers linking in depth.
- **"Crafting Interpreters"** by Robert Nystrom (craftinginterpreters.com) — Free online. Building an interpreter and compiler teaches you what a compiler must solve. Excellent way to internalize the problem space.
- **"Engineering a Compiler"** by Cooper & Torczon — The university-level textbook on compilers. More formal than Crafting Interpreters but more comprehensive on optimization.
- **Matt Godbolt's CppCon talks** (YouTube) — "What Has My Compiler Done for Me Lately?" is a classic. Directly shows how to use Compiler Explorer to reason about compiler output.
- **"The LLVM Tutorial"** (llvm.org/docs/tutorial) — If you want to go deep on compiler infrastructure, this walkthrough builds a compiler using LLVM's IR and optimization passes.

### Practical exercises

```bash
# Compare unoptimized vs optimized assembly
gcc -O0 -S -o unopt.s example.c
gcc -O3 -S -o opt.s example.c
diff unopt.s opt.s

# View LLVM IR output (Clang)
clang -S -emit-llvm -o output.ll example.c

# Inspect shared library dependencies
ldd /usr/bin/ls

# Inspect what symbols a compiled object exports
nm -C my_object.o

# See final binary sections
readelf -S my_binary
```

---

## Phase 8 — Applied Understanding

**Goal:** Read real spec sheets, benchmark reports, chip announcements, and systems code and extract meaning from them independently.

### Practice exercises

- Go to Intel's ARK database (ark.intel.com) or AMD's product pages. Pick a Core i5 and a Core i7 from the same generation. Explain every row of the spec table in your own words.
- Take a C program you've written, compile with `-O0` and `-O3`, and use Compiler Explorer to walk through the differences instruction by instruction.
- Read the `proc(5)` man page on Linux and write a small program that reads its own `/proc/self/maps` and prints the memory regions.

### Resources

- **Gamers Nexus** (YouTube + gamersnexus.net) — Rigorous hardware reviews; good for learning how professionals evaluate chips.
- **Chips and Cheese** (chipsandcheese.com) — Stays current with deep architecture analysis of new releases.
- **Digital Foundry** (YouTube) — GPU and console hardware analysis.
- **"The Linux Programming Interface"** by Michael Kerrisk — The definitive reference for Linux system programming. Use it as a reference when exploring how your code interfaces with the kernel.

---

## Phase 9 — Emerging Topics

**Goal:** Understand where chip design and system software are heading — chiplets, 3D stacking, NPUs, RISC-V, and heterogeneous computing.

### Topics to explore

- **Chiplets** — Why AMD and Intel are moving from monolithic dies to modular chiplet designs
- **3D stacking** (e.g. AMD's V-Cache) — Stacking cache directly on top of the compute die
- **NPUs** (Neural Processing Units) — Dedicated AI accelerators built into modern chips (Apple's Neural Engine, Intel's NPU in Meteor Lake)
- **RISC-V** — An open-source ISA gaining traction; understanding it clarifies why x86 dominance isn't guaranteed
- **Heterogeneous computing** — Combining CPUs, GPUs, and NPUs on the same chip or package
- **WebAssembly (Wasm)** — A portable binary instruction format that raises questions about ISA abstraction

### Resources

- **IEEE Spectrum** (spectrum.ieee.org) — Industry-level coverage of new chip announcements and semiconductor trends.
- **Hot Chips conference** (hotchips.org) — Actual presentations from AMD, Intel, Apple, and NVIDIA engineers. Dense, but incredible primary source material once you have the vocabulary.
- **"The Chip Letter"** and similar Substack newsletters — Accessible industry news on semiconductors.
- **"RISC-V Explained"** — Search on YouTube; Low Level Learning and others have solid explainers.

---

## The Full Stack: How It All Connects

Understanding computers end-to-end means understanding each layer and how they talk to the layers above and below:

```
High-level code (Python, JavaScript, Rust, C)
        ↓  [Compiler / Interpreter]
Assembly / Machine code (ISA-specific instructions)
        ↓  [CPU Microarchitecture]
Microops, pipelines, caches, branch predictors
        ↓  [Hardware]
Transistors, logic gates, memory cells, buses
```

The OS sits alongside all of this, managing hardware on behalf of programs — virtualizing the CPU (scheduling), virtualizing memory (paging), and abstracting hardware devices (drivers).

Each phase in this roadmap builds vocabulary and mental models for one layer. By Phase 8, you can reason across all of them simultaneously — which is the actual goal.

---

## Summary: Channels and Sites to Follow

| Resource | Type | Best for |
|---|---|---|
| Gamers Nexus | YouTube + website | Rigorous CPU/GPU reviews |
| Branch Education | YouTube | Visual architecture explainers |
| Low Level Learning | YouTube | Assembly, systems programming, architecture |
| Hardware Unboxed | YouTube | Benchmarks, GPU analysis |
| Chips and Cheese | Blog | Architecture teardowns |
| Digital Foundry | YouTube | GPU + console hardware |
| AnandTech (archived) | Website | Gold-standard historical reviews |
| IEEE Spectrum | Website | Industry news |
| Hot Chips | Conference archive | Primary source engineering talks |
| Compiler Explorer (godbolt.org) | Web tool | Compiler output analysis |
| OSDev Wiki | Wiki | OS internals reference |
| OSTEP (ostep.org) | Free textbook | Operating systems fundamentals |

---

*Start at Phase 1 even if you feel you know some basics — the foundation matters more than you think. The goal isn't to memorise specs but to build a mental model that makes every new chip announcement, compiler flag, or OS behavior immediately interpretable.*
