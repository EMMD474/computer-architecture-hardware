# Computer Hardware Learning Roadmap

A structured path from zero to confidently understanding CPUs, GPUs, architectures, TDP, IPC, and chip comparisons (Intel vs AMD vs Apple Silicon).

---

## Phase 1 — Foundation

**Goal:** Build a mental model of what a CPU actually is — fetch-decode-execute cycle, clock speed, cores basics, binary and logic.

### Resources

- **Crash Course Computer Science** (YouTube) — Episodes 1–10. Covers binary, logic gates, the ALU, and how a CPU fetches and executes instructions. Free, well-paced, and the perfect starting point.
- **"How a CPU Works"** by In One Lesson (YouTube) — A single 20-minute video that gives you the mental model you need before going deeper.
- **Sebastian Lague's "How Do Computers Work?" series** (YouTube) — More visual and intuition-focused. Great complement to Crash Course.

---

## Phase 2 — Architecture Deep Dive

**Goal:** Understand ISA (Instruction Set Architecture), microarchitecture, caches, pipelines, and branch prediction — the building blocks that explain *why* chips are designed the way they are.

### Resources

- **"Modern Microprocessors — A 90 Minute Guide"** by Jason Robert Carey Patterson — A free web article (search the title). One of the best overviews of CPU microarchitecture ever written. Read this early in Phase 2.
- **Computer Organization and Design** by Patterson & Hennessy (textbook) — You don't need to read it cover to cover. Focus on:
  - Chapter 1 — Computer Abstractions and Technology
  - Chapter 4 — The Processor
  - Chapter 5 — Memory Hierarchy
- **Chips and Cheese** (chipsandcheese.com) — A blog with deep but accessible technical teardowns of real CPU and GPU architectures. Bookmark this early.

---

## Phase 3 — Performance Concepts

**Goal:** Understand TDP, IPC, process nodes (nm), performance-per-watt, efficiency cores, and how to read a spec sheet meaningfully.

### Key concepts to learn in this phase

| Concept | What it means |
|---|---|
| **TDP** (Thermal Design Power) | The maximum heat a chip generates under load — relates to power draw and cooling needs |
| **IPC** (Instructions Per Clock) | How much work a core does per clock cycle — more important than raw GHz |
| **Process node (nm)** | The size of transistors on a chip — smaller = more transistors, better efficiency |
| **Efficiency cores vs performance cores** | Hybrid designs (like Intel's P/E cores) balance throughput and power draw |

### Resources

- **"TDP Explained"** by Hardware Unboxed (YouTube) — Focused, practical breakdown of exactly that concept.
- **"Why More Cores Isn't Always Better"** (YouTube) — Multiple good explainers; search the phrase. Channels like Linus Tech Tips and Digital Foundry have solid versions.
- **AnandTech archives** (anandtech.com) — The site shut down in 2023 but archives remain. Their CPU reviews are the gold standard for learning to read benchmarks. Read an old Intel or AMD review and follow how each test is explained.

---

## Phase 4a — CPU Comparison: Intel vs AMD vs Apple Silicon

**Goal:** Apply your architecture knowledge to real products. Understand why a Core i7 outperforms a Core i5, what AMD's Zen architecture changed, and why Apple Silicon performs the way it does.

### Resources

- **"Why Apple Silicon is So Fast"** by Branch Education (YouTube) — The single best video on unified memory architecture and the M-series chip design.
- **"AMD vs Intel — Architecture Differences Explained"** by Gamers Nexus (YouTube) — Rigorous and doesn't oversimplify. Their channel overall is excellent for this.
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
- **"A100 vs H100 — What Changed?"** type videos — Helps you understand generational GPU leaps, especially relevant if you're interested in AI/ML workloads.

### Key GPU concepts to understand

- **CUDA cores vs shader processors** — NVIDIA vs AMD terminology for the same basic unit
- **VRAM** — Why it matters and when you hit its limits
- **Parallel vs serial execution** — The core architectural difference between a GPU and CPU
- **Compute workloads vs graphics workloads** — Why a GPU good at gaming isn't necessarily ideal for AI training

---

## Phase 5 — Applied Understanding

**Goal:** Read real spec sheets, benchmark reports, and chip announcements and extract meaning from them independently.

### Practice exercise

Go to Intel's ARK database (ark.intel.com) or AMD's product pages. Pick a Core i5 and a Core i7 from the same generation. Try to explain *every row of the spec table* in your own words using what you've learned. When you can do that fluently, you've genuinely arrived.

### Resources

- **Gamers Nexus** (YouTube + gamersnexus.net) — Rigorous hardware reviews; good for learning how professionals evaluate chips.
- **Chips and Cheese** (chipsandcheese.com) — Stays current with deep architecture analysis of new releases.
- **Digital Foundry** (YouTube) — GPU and console hardware analysis, excellent if you care about graphics performance.

---

## Phase 6 — Emerging Topics

**Goal:** Understand where chip design is heading — chiplets, 3D stacking, NPUs, RISC-V, and heterogeneous computing.

### Topics to explore

- **Chiplets** — Why AMD and Intel are moving from monolithic dies to modular chiplet designs
- **3D stacking** (e.g. AMD's V-Cache) — Stacking cache directly on top of the compute die
- **NPUs** (Neural Processing Units) — Dedicated AI accelerators built into modern chips (Apple's Neural Engine, Intel's NPU in Meteor Lake)
- **RISC-V** — An open-source ISA gaining traction; understanding it clarifies why x86 dominance isn't guaranteed
- **Heterogeneous computing** — Combining CPUs, GPUs, and NPUs on the same chip or package

### Resources

- **IEEE Spectrum** (spectrum.ieee.org) — Industry-level coverage of new chip announcements and semiconductor trends.
- **Hot Chips conference** (hotchips.org) — Actual presentations from AMD, Intel, Apple, and NVIDIA engineers. Dense, but incredible primary source material once you have the vocabulary.
- **"The Chip Letter"** and similar Substack newsletters — Accessible industry news on semiconductors.
- **"RISC-V Explained"** — Search on YouTube; several good explainers exist from Low Level Learning and others.

---

## Summary: Channels and Sites to Follow Consistently

| Resource | Type | Best for |
|---|---|---|
| Gamers Nexus | YouTube + website | Rigorous CPU/GPU reviews |
| Branch Education | YouTube | Visual architecture explainers |
| Low Level Learning | YouTube | Deep architecture and systems topics |
| Hardware Unboxed | YouTube | Benchmarks, GPU analysis |
| Chips and Cheese | Blog | Architecture teardowns |
| Digital Foundry | YouTube | GPU + console hardware |
| AnandTech (archived) | Website | Gold-standard historical reviews |
| IEEE Spectrum | Website | Industry news |
| Hot Chips | Conference archive | Primary source engineering talks |

---

*Start at Phase 1 even if you feel you know some basics — the foundation matters more than you think. The goal isn't to memorise specs but to build a mental model that makes every new chip announcement immediately interpretable.*
