# Software Layers and Kernel Interaction Roadmap

A structured path for understanding how software communicates with the Linux kernel — from the user-space program you write down to the hardware the kernel manages on your behalf.

---

## The Big Picture

Every program runs in a sandboxed environment created by the kernel. The kernel owns the hardware — the CPU, memory, disk, network card — and user-space programs access it only through a controlled interface called the **system call interface**. Nothing crosses that boundary without the kernel's involvement.

```
┌─────────────────────────────────────────────────┐
│              User Space (Ring 3)                 │
│                                                  │
│  Application (Python, Rust, Go, C)               │
│       ↓                                          │
│  Standard Library (glibc / musl / libc)          │
│       ↓                                          │
│  System Call Interface  ←── the boundary         │
├─────────────────────────────────────────────────┤
│              Kernel Space (Ring 0)               │
│                                                  │
│  Syscall handlers                                │
│  Process scheduler                               │
│  Virtual memory manager                          │
│  Virtual File System (VFS)                       │
│  Network stack                                   │
│  Device drivers                                  │
│       ↓                                          │
│  Hardware (CPU, RAM, disk, NIC, GPU)             │
└─────────────────────────────────────────────────┘
```

Understanding this model — and every layer in it — is the goal of this roadmap.

---

## Phase 1 — The Kernel/User Space Divide

**Goal:** Understand what the kernel is, why the privilege boundary exists, and what it means for software to "run in user space."

### Key concepts

| Concept | What it means |
|---|---|
| **Kernel** | The core of the OS; runs in Ring 0 with full hardware access |
| **User space** | Where your programs run; Ring 3, hardware access is restricted |
| **Privilege rings** | CPU hardware enforcement of access levels (x86 has Ring 0–3; Linux uses 0 and 3) |
| **Kernel mode vs user mode** | The CPU switches modes on a system call; different instructions are allowed in each |
| **Why the boundary exists** | Isolation and protection — a crashing user program cannot corrupt the kernel or other processes |

### Resources

- **OSTEP Chapter 2 — "Introduction to Operating Systems"** (ostep.org) — Explains the crux: the OS is the "virtualization layer" between programs and hardware.
- **"Privilege Rings Explained"** — Search on YouTube; several short explainers from Low Level Learning and others cover this well.
- **"How Does the Linux Kernel Work?"** by ByteByteGo (YouTube) — A good high-level animated overview to build initial intuition.
- **"Linux Kernel in a Nutshell"** by Greg Kroah-Hartman (free PDF, search title) — Short and accessible introduction to the kernel from a kernel developer.

### Practical exercise

```bash
# Run a simple program and observe the privilege switch via system calls
strace echo "hello"

# The output shows every system call the program makes.
# Each line is a crossing of the user/kernel boundary.
```

---

## Phase 2 — System Calls: The Kernel Interface

**Goal:** Understand what system calls are, how they work at the hardware level, and how to observe them in real programs.

### What a system call is

A system call is a controlled entry point into the kernel. Your program cannot call kernel functions directly — instead, it places a syscall number and arguments in CPU registers, then executes a special instruction (`syscall` on x86-64) that:
1. Switches the CPU from Ring 3 to Ring 0
2. Jumps to the kernel's syscall handler
3. The kernel performs the requested operation
4. Switches back to Ring 3 and returns the result

### The syscall path in detail

```
Your code calls write(1, "hello", 5)
        ↓
glibc wrapper sets up registers:
  rax = 1  (syscall number for write)
  rdi = 1  (file descriptor: stdout)
  rsi = ptr to "hello"
  rdx = 5  (byte count)
        ↓
CPU executes `syscall` instruction
        ↓
CPU switches to Ring 0, jumps to kernel entry point
        ↓
Kernel's sys_write() handler runs
        ↓
Data sent to file descriptor's underlying driver
        ↓
CPU executes `sysret`, returns to Ring 3
        ↓
glibc returns result to your code
```

### Key system calls to understand

| Syscall | What it does |
|---|---|
| `read` / `write` | Read from or write to a file descriptor |
| `open` / `close` | Open a file (get an fd) or close it |
| `fork` | Create a new process (copy of the current one) |
| `execve` | Replace current process image with a new program |
| `exit` | Terminate the process |
| `mmap` / `munmap` | Map memory into the process address space |
| `brk` | Adjust the heap boundary (used by malloc) |
| `wait` / `waitpid` | Wait for a child process to finish |
| `pipe` | Create a unidirectional data channel |
| `socket` / `connect` / `bind` | Network communication |
| `ioctl` | Device-specific control operations |
| `clone` | Create threads (like fork but more configurable) |

### Resources

- **"The Linux Programming Interface" (TLPI)** — Michael Kerrisk. Chapter 3 covers system calls in depth. This is the definitive reference.
- **Linux man pages** — `man 2 <syscall_name>` for any syscall (e.g. `man 2 read`). The Section 2 man pages are the authoritative syscall documentation.
- **Linux syscall table** — Search "Linux x86-64 syscall table". Shows all ~300+ syscalls, their numbers, and argument registers.
- **"System Calls Make the World Go Round"** by LiveOverflow (YouTube) — Excellent practical explanation of what happens at the hardware level.
- **"Syscalls, Kernel vs User Mode"** by Low Level Learning (YouTube) — Clear walkthrough with assembly examples.

### Practical exercises

```bash
# Trace all system calls made by a program
strace ls -la

# Count syscalls by type
strace -c ls -la

# Trace only specific syscalls
strace -e trace=openat,read,write ls

# Trace a running process by PID
strace -p <pid>

# Write the minimal C program that makes a syscall directly (no libc)
# (see assembly section for the raw syscall approach)
```

---

## Phase 3 — The Standard Library: Syscall Abstraction

**Goal:** Understand what the C standard library (glibc, musl) actually is — a collection of wrappers and utilities that sit between your code and raw syscalls.

### What glibc does

Most programs don't call `syscall()` directly. They call C library functions like `printf()`, `malloc()`, `fopen()` — and the C library translates these into the appropriate syscalls.

```
printf("hello\n")
        ↓
glibc: format the string, buffer it
        ↓
glibc calls write(1, buffer, n)
        ↓
write() wrapper sets up registers
        ↓
syscall instruction → kernel
```

### Key abstractions the standard library provides

| Library function | Underlying syscall(s) |
|---|---|
| `fopen()` | `openat()` |
| `fread()` / `fwrite()` | `read()` / `write()` (with buffering) |
| `malloc()` / `free()` | `mmap()` or `brk()` |
| `printf()` | `write()` (after formatting) |
| `pthread_create()` | `clone()` |
| `exit()` | `exit_group()` |
| `getenv()` | Reads from process environment (no syscall) |

### Why buffering matters

`fread()` buffers reads — it calls `read()` once for a large chunk and serves your program from the buffer. This is a major performance optimization: syscalls are expensive because they cross the privilege boundary. Minimizing syscalls is a real concern in systems programming.

### Resources

- **"The GNU C Library" documentation** (gnu.org/software/libc/manual) — The reference for glibc internals.
- **"How Does malloc Work?"** by Siddharth Bhatia (search title on YouTube or blog) — Explains how malloc maps to `mmap`/`brk` syscalls and manages a free list.
- **"libc vs glibc vs musl"** — Search for comparisons; understanding why musl exists (smaller, simpler, statically linkable) clarifies what glibc is doing.
- **"Playing with ptrace"** blog posts — Shows how debuggers use ptrace (a syscall) to intercept other syscalls. Good for understanding the syscall model more deeply.

### Practical exercises

```bash
# See which libc a binary is linked against
ldd /bin/ls

# Build a C program with musl instead of glibc (if installed)
musl-gcc -static -o hello hello.c

# Inspect libc symbols a binary imports
nm -D /bin/ls | grep -i " U "

# Write a C program, run it under strace, and map each C function call
# to the syscall it generates
```

---

## Phase 4 — Process Management

**Goal:** Understand how the kernel creates, manages, and destroys processes — and how fork/exec/wait work together.

### The process model

The kernel represents each running program as a **process** — an isolated execution context with its own:
- Virtual address space
- File descriptor table
- Process ID (PID) and parent PID (PPID)
- Signal handlers
- Credentials (UID, GID)
- Resource limits (ulimits)

All of this state is stored in the kernel's **process descriptor** (the `task_struct` in Linux).

### fork → exec → wait

The Unix process model is built on three syscalls:

```
Parent calls fork()
        ↓
Kernel creates a copy of the parent process (copy-on-write)
Both parent and child continue executing
        ↓
Child calls execve("/bin/ls", args, env)
        ↓
Kernel replaces the child's address space with /bin/ls's binary
        ↓
/bin/ls runs
        ↓
/bin/ls calls exit()
        ↓
Kernel marks child as zombie, sends SIGCHLD to parent
        ↓
Parent calls wait() / waitpid()
        ↓
Kernel cleans up the child's process descriptor
```

### Key concepts

| Concept | What it means |
|---|---|
| **Copy-on-write (COW)** | fork() doesn't copy memory immediately — pages are shared until one process writes, then a copy is made |
| **Zombie process** | A process that has exited but whose parent hasn't called wait() yet; the process descriptor still exists |
| **Orphan process** | A process whose parent exited; adopted by init (PID 1) |
| **Process groups and sessions** | How the kernel organizes processes for signal delivery and terminal management |
| **Environment and argv** | The kernel passes these as part of execve(); they land at the top of the process stack |

### Resources

- **OSTEP — "Virtualization: The Process" chapters** — Covers fork/exec/wait with clear explanations and code examples. Essential reading.
- **"Fork and Exec Explained"** by Low Level Learning (YouTube) — Walks through writing fork/exec code with strace output.
- **TLPI Chapters 24–26** — Deep coverage of process creation, program execution, and process termination.
- **`man 2 fork`, `man 2 execve`, `man 2 wait`** — The authoritative specification for each call.

### Practical exercises

```bash
# See the process tree
pstree -p

# Inspect a process's full state
cat /proc/<pid>/status

# Inspect a process's file descriptors
ls -la /proc/<pid>/fd

# Write a C program that forks, runs ls via execve, and waits for it
# Then run it under strace and trace the fork/exec/wait calls
```

---

## Phase 5 — Memory Management

**Goal:** Understand how the kernel manages virtual memory — how your program gets memory, what page faults are, and how the hardware (MMU) participates.

### Virtual memory overview

Every process has its own virtual address space. The CPU's **Memory Management Unit (MMU)** translates virtual addresses to physical addresses using **page tables** that the kernel maintains. No process can access another's memory — the hardware enforces this.

### The process address space layout

```
High addresses
┌─────────────────────┐
│   Kernel space      │  ← mapped in every process but inaccessible from Ring 3
├─────────────────────┤
│   Stack             │  ← grows down; local variables, return addresses
│        ↓            │
│   (unmapped gap)    │
│        ↑            │
│   Heap              │  ← grows up; malloc/mmap allocations
├─────────────────────┤
│   BSS               │  ← uninitialized global variables (zeroed)
│   Data              │  ← initialized global variables
│   Text (code)       │  ← executable instructions (read-only)
└─────────────────────┘
Low addresses
```

### Key concepts

| Concept | What it means |
|---|---|
| **Pages** | The unit of memory management — typically 4 KB; the kernel and MMU work in pages, not bytes |
| **Page fault** | The MMU cannot translate a virtual address; the kernel's fault handler runs to resolve it (allocate memory, load from disk, or kill the process with SIGSEGV) |
| **Demand paging** | Memory is not physically allocated until it's actually accessed; page faults trigger the real allocation |
| **mmap** | Map a file or anonymous memory into the process's address space — the basis for malloc, shared memory, and loading binaries |
| **Copy-on-write** | Pages shared between processes (after fork) are only copied when written — the MMU marks them read-only and the fault handler copies on first write |
| **Swapping** | The kernel can page memory out to disk when physical RAM is exhausted, bringing it back on demand |
| **Huge pages** | 2 MB or 1 GB pages reduce TLB pressure for large allocations |

### Resources

- **OSTEP — "Virtualization: Memory" chapters** — The best available explanation of virtual memory, paging, TLBs, and page tables. Covers all the concepts with clear diagrams.
- **"What Every Programmer Should Know About Memory"** by Ulrich Drepper (free PDF, search title) — Deep and technical; covers caches, TLBs, NUMA, and memory bandwidth. Read after OSTEP.
- **CS:APP Chapter 9 — "Virtual Memory"** — Thorough treatment of the virtual memory system from the programmer's perspective.
- **"How malloc Works"** and related deep dives — Understand that `malloc` is not a syscall; it manages a heap that it obtained via `mmap`/`brk`.

### Practical exercises

```bash
# Inspect a process's virtual memory map
cat /proc/<pid>/maps
# or
pmap -x <pid>

# Watch page faults in real time for a process
perf stat -e page-faults ./my_program

# Inspect memory stats for a running process
cat /proc/<pid>/smaps | head -50

# Write a C program that:
# 1. Calls mmap() to get anonymous memory
# 2. Writes to it
# 3. Calls munmap() to release it
# Run it under strace to see the kernel interaction
```

---

## Phase 6 — The Virtual File System (VFS) and I/O

**Goal:** Understand how the kernel abstracts all I/O — files, sockets, pipes, devices — behind a unified file descriptor interface.

### Everything is a file descriptor

In Unix, a **file descriptor (fd)** is just an integer that refers to an open kernel object. The same read/write/close syscalls work on:
- Regular files on disk
- Network sockets
- Pipes (IPC)
- Terminals
- Devices (`/dev/null`, `/dev/urandom`, `/dev/sda`)
- Kernel interfaces (`/proc`, `/sys`, inotify, epoll, signalfd)

This uniformity is one of Unix's core design decisions.

### The VFS layer

The **Virtual File System** is a kernel abstraction layer that sits above all filesystem implementations:

```
User space: open("/etc/hosts", O_RDONLY)
        ↓
Kernel: VFS layer
        ↓  (dispatches to the right filesystem driver)
ext4 driver / tmpfs driver / procfs driver / nfs driver / ...
        ↓
Block device driver / network driver
        ↓
Hardware: disk, NIC, etc.
```

This is why `read()` works the same whether the file is on an ext4 drive, a network mount, or a virtual filesystem like `/proc`.

### Key concepts

| Concept | What it means |
|---|---|
| **File descriptor table** | Per-process table mapping fd numbers to kernel file objects |
| **Inode** | The kernel's representation of a file's metadata (permissions, size, data block pointers) |
| **Page cache** | The kernel caches file contents in RAM; reads/writes go through the cache, not directly to disk |
| **Buffered vs direct I/O** | Buffered (default) goes through the page cache; `O_DIRECT` bypasses it for databases that manage their own caching |
| **epoll / select / poll** | Syscalls that let a single thread wait on multiple file descriptors — the basis of async I/O |
| **`/proc` filesystem** | A virtual filesystem exposing kernel state as files; `cat /proc/cpuinfo` does not read a disk |
| **`/sys` filesystem** | Exposes kernel objects (devices, drivers) as a hierarchy of files |

### Resources

- **TLPI Chapters 4–5** — File I/O fundamentals; the file descriptor model.
- **TLPI Chapters 13–14** — Buffering in the I/O layer; the page cache.
- **"The Linux Virtual File System"** by Alessandro Rubini (search LWN archives) — Deep technical explanation of how VFS works internally.
- **"epoll is fundamentally broken"** by Marek Majkowski (Cloudflare blog) — A practical deep-dive that reveals how epoll's edge cases reflect VFS internals. Read after you understand the basics.
- **"How Files Are Stored on Disk"** — Search for ext4 or filesystem internals explainers; Computerphile has a good accessible one.

### Practical exercises

```bash
# See all file descriptors open in a process
ls -la /proc/<pid>/fd

# Watch file opens in real time system-wide (requires root)
inotifywait -m /var/log

# Inspect the page cache size
free -h  # "buff/cache" column is the page cache

# Drop the page cache and measure the difference in read speed
sync && echo 3 > /proc/sys/vm/drop_caches
time dd if=/dev/sda of=/dev/null bs=1M count=100  # cold (from disk)
time dd if=/dev/sda of=/dev/null bs=1M count=100  # warm (from cache)

# Observe filesystem syscalls during a build
strace -e trace=openat,read,write make 2>&1 | head -50
```

---

## Phase 7 — Signals and Inter-Process Communication (IPC)

**Goal:** Understand how the kernel delivers asynchronous notifications to processes and how processes communicate with each other.

### Signals

A **signal** is a kernel mechanism for asynchronous notification. The kernel can deliver a signal to a process at any time, interrupting its execution.

| Signal | Default action | Meaning |
|---|---|---|
| `SIGTERM` | Terminate | Graceful shutdown request |
| `SIGKILL` | Terminate (uncatchable) | Forced kill |
| `SIGSEGV` | Core dump | Invalid memory access |
| `SIGINT` | Terminate | Ctrl+C from terminal |
| `SIGCHLD` | Ignore | Child process changed state |
| `SIGALRM` | Terminate | Timer expired |
| `SIGUSR1/2` | Terminate | User-defined |

When a signal arrives, the kernel suspends the process, runs the signal handler (user-space code), then resumes the process. `SIGKILL` and `SIGSTOP` cannot be caught or ignored — the kernel handles them directly.

### IPC mechanisms

| Mechanism | How it works | Best for |
|---|---|---|
| **Pipes** | Unidirectional byte stream between related processes | Parent-child communication |
| **Named pipes (FIFOs)** | Like pipes but accessible via filesystem path | Unrelated processes |
| **Unix sockets** | Full-duplex byte stream via filesystem path | Local IPC with high throughput |
| **Shared memory** (`mmap` / `shm_open`) | Two processes map the same physical pages | Fast, large data sharing |
| **Message queues** (`mq_open`) | Kernel-managed message passing with priorities | Decoupled producer/consumer |
| **Semaphores** | Kernel-managed synchronization primitives | Coordinating shared memory access |

### Resources

- **TLPI Chapters 20–22** — Signals: fundamentals, signal handlers, and signal delivery.
- **TLPI Chapters 44–54** — All IPC mechanisms with thorough coverage.
- **OSTEP — "Concurrency" section** — Covers the problems that IPC mechanisms solve; good motivation before the mechanics.
- **"Unix Network Programming Vol. 2"** by Stevens — The classic reference for IPC. Older but comprehensive.

### Practical exercises

```bash
# Send a signal manually
kill -SIGTERM <pid>
kill -SIGUSR1 <pid>

# Write a C program with a SIGINT handler that prints "shutting down..."
# before exiting cleanly

# Create a named pipe and use it between two terminals
mkfifo /tmp/mypipe
# Terminal 1: cat /tmp/mypipe
# Terminal 2: echo "hello" > /tmp/mypipe

# Inspect IPC objects on the system
ipcs -a
```

---

## Phase 8 — Scheduling: How the Kernel Shares the CPU

**Goal:** Understand how the kernel decides which process or thread runs on which CPU core at any given moment.

### The scheduler's job

The kernel's scheduler multiplexes one or more CPUs across all runnable processes. Each process gets a time slice; when it expires (or the process blocks on I/O), the scheduler picks the next process to run and performs a **context switch**.

### Context switch in detail

```
Process A is running on CPU core 0
        ↓
Timer interrupt fires (hardware → kernel)
        ↓
Kernel saves Process A's state (registers, stack pointer, program counter)
into A's task_struct
        ↓
Kernel runs scheduler: picks Process B
        ↓
Kernel restores Process B's state from its task_struct
        ↓
CPU resumes executing Process B
```

This happens thousands of times per second.

### Key concepts

| Concept | What it means |
|---|---|
| **CFS (Completely Fair Scheduler)** | Linux's default scheduler; tracks "virtual runtime" to give each process a fair share |
| **Preemption** | The kernel can forcibly interrupt a running process (via timer interrupt) to schedule another |
| **Voluntary vs involuntary context switch** | Voluntary: process blocks (e.g. on I/O); involuntary: preempted by scheduler |
| **Run queue** | The per-CPU list of processes ready to run |
| **CPU affinity** | Pinning a process or thread to specific CPU cores |
| **Priority and nice values** | User-adjustable hints to the scheduler (nice -20 to +19) |
| **Real-time scheduling** | `SCHED_FIFO` / `SCHED_RR` policies for latency-sensitive workloads |
| **NUMA awareness** | The scheduler tries to keep processes on CPUs close to their memory |

### Resources

- **OSTEP — "CPU Virtualization" chapters** — Covers scheduling algorithms from first principles: FIFO, SJF, MLFQ, and fair-share. Read these before Linux specifics.
- **"Inside the Linux CFS Scheduler"** by Josh Triplett (LWN.net, search title) — The best accessible explanation of how CFS actually works.
- **"Linux Kernel Development"** by Robert Love — Chapter 4 covers the scheduler in detail.
- **"perf sched"** tutorial — Using the `perf` tool to observe scheduling behavior in real programs.

### Practical exercises

```bash
# Watch context switches per second for a process
pidstat -w 1

# See voluntary vs involuntary context switches
cat /proc/<pid>/status | grep ctxt

# Pin a process to specific CPU cores
taskset -c 0,1 ./my_program

# Set scheduling policy and priority
chrt -f 50 ./my_program  # SCHED_FIFO priority 50

# Observe what the scheduler is doing system-wide
perf sched record sleep 5
perf sched latency
```

---

## Phase 9 — Device Drivers and Hardware I/O

**Goal:** Understand how the kernel bridges between abstract software I/O (file descriptors, read/write) and real hardware operations.

### The driver model

A **device driver** is kernel code that knows how to talk to a specific piece of hardware. The kernel exposes hardware through the VFS — so a device appears as a file in `/dev` — and routes read/write/ioctl calls to the appropriate driver.

```
Your code: write(fd, data, n)   ← fd points to /dev/ttyUSB0
        ↓
VFS: route to character device driver
        ↓
USB serial driver: package data into USB protocol frames
        ↓
USB host controller driver: DMA transfer to USB controller hardware
        ↓
Physical hardware: USB signal on the wire
```

### Key concepts

| Concept | What it means |
|---|---|
| **Character vs block devices** | Character devices stream bytes (terminals, serial); block devices have addressable sectors (disks) |
| **`ioctl`** | A catch-all syscall for device-specific control operations not covered by read/write |
| **DMA (Direct Memory Access)** | Hardware reads/writes directly to RAM without CPU involvement; driver sets up the transfer |
| **Interrupts** | Hardware signals the CPU when it needs attention (data ready, transfer complete); the driver's interrupt handler runs |
| **Polling vs interrupt-driven I/O** | Polling: CPU constantly checks device; interrupt-driven: device notifies CPU when ready |
| **Kernel modules** | Drivers can be loaded/unloaded at runtime as `.ko` files without rebooting |
| **udev** | Userspace daemon that responds to kernel device events and creates `/dev` entries |

### Resources

- **"Linux Device Drivers" (LDD3)** by Corbet, Rubini, Kroah-Hartman (free online at lwn.net/Kernel/LDD3) — The canonical book for writing Linux drivers. Read Chapters 1–3 for the conceptual model even if you never write a driver.
- **"Writing a Simple Linux Kernel Module"** — Many tutorials exist; search on YouTube and dev.to. Seeing a loadable module work demystifies the kernel/driver boundary.
- **"How DMA Works"** — Search IEEE or LWN for DMA explainers; the concept of hardware-direct memory access is fundamental to understanding fast I/O.
- **"Linux Kernel Networking"** by Rami Rosen — For network driver specifics and the network stack architecture.

### Practical exercises

```bash
# List all loaded kernel modules
lsmod

# See detailed info about a specific module
modinfo usbserial

# Load and unload a module
sudo modprobe usbserial
sudo modprobe -r usbserial

# See kernel messages when you plug in USB hardware
dmesg -w  # then plug in a device

# List all devices and their drivers
ls -la /sys/bus/usb/devices/
cat /sys/bus/usb/devices/1-1/manufacturer
```

---

## Phase 10 — Security Boundaries

**Goal:** Understand the kernel mechanisms that enforce isolation between processes and limit what software can do to the system.

### Key kernel security mechanisms

| Mechanism | What it does |
|---|---|
| **UIDs / GIDs** | Process credentials; the kernel checks these on every resource access |
| **Capabilities** | Fine-grained privileges that replace the root/non-root binary (e.g. `CAP_NET_BIND_SERVICE` to bind port 80 without being root) |
| **`seccomp`** | A syscall filter; allows a process to restrict which syscalls it can make (used by Chrome, containers) |
| **Namespaces** | Isolate kernel resources per group of processes: PID namespace, network namespace, mount namespace, etc. — the basis of containers |
| **cgroups** | Limit and account for resource usage (CPU, memory, I/O) per group of processes |
| **`ptrace`** | Allows one process to inspect and control another; used by debuggers and strace. Restricted by seccomp in sandboxed environments |
| **Mandatory Access Control (MAC)** | SELinux / AppArmor enforce policy beyond discretionary access control |

### Why this matters

Containers (Docker, Kubernetes) are built on namespaces and cgroups. `seccomp` is why Chrome renderer processes can't open files or make network connections directly. Understanding these mechanisms explains both how security works and how it fails.

### Resources

- **"Linux Namespaces"** by Michael Kerrisk (LWN.net articles) — The definitive series on namespace internals. Search "lwn linux namespaces".
- **"Container Security"** by Liz Rice (free PDF from O'Reilly) — Practical book that builds a container from scratch using namespaces and cgroups. Makes the abstraction very concrete.
- **"How Docker Works Under the Hood"** by Ivan Velichko (iximiuz.com) — Detailed walkthrough of the kernel features Docker uses.
- **"Seccomp in Practice"** — Search the phrase; Cloudflare and Google have good blog posts on using seccomp in production.
- **TLPI Chapters 38–39** — Capabilities and setuid programs.

### Practical exercises

```bash
# Run a command in a new network namespace (no network access)
sudo unshare --net ip addr  # shows only loopback

# Limit a process to 100MB of memory with cgroups v2
systemd-run --scope -p MemoryMax=100M ./my_program

# See what capabilities a process has
cat /proc/<pid>/status | grep Cap

# Write a minimal seccomp filter in C that only allows exit and write
# (forces the process to crash if it tries any other syscall)

# Build a container manually from scratch
# Follow "bocker" or similar minimal container implementation tutorials
```

---

## The Full Interaction Chain

Putting all phases together — tracing one user action through every layer:

```
User runs: cat /etc/hosts

Shell forks a child process (fork syscall)
        ↓
Child calls execve("/bin/cat", ...) — kernel loads the ELF binary into memory
        ↓
cat calls fopen("/etc/hosts") → openat() syscall
        ↓
VFS: looks up inode for /etc/hosts, checks permissions (UID/GID)
        ↓
ext4 driver: reads the inode's data blocks; pages loaded into page cache
        ↓
cat calls fread() → read() syscall
        ↓
Kernel: copy data from page cache to cat's user-space buffer
        ↓
cat calls fwrite(stdout) → write() syscall
        ↓
Kernel: routes to terminal driver (tty subsystem)
        ↓
Terminal driver: sends characters to pseudoterminal
        ↓
Terminal emulator receives data, renders to screen
        ↓
cat calls exit() → exit_group() syscall
        ↓
Kernel: marks process as zombie, sends SIGCHLD to shell
        ↓
Shell calls waitpid() → reaps the zombie, prints next prompt
```

Every line in that trace corresponds to a phase in this roadmap.

---

## Summary: Resources to Keep Handy

| Resource | What it covers |
|---|---|
| **OSTEP** (ostep.org) | Processes, memory, scheduling — free and essential |
| **TLPI** by Kerrisk | The definitive Linux system programming reference |
| **CS:APP** by Bryant & O'Hallaron | Machine code, linking, virtual memory, systems programming |
| **LDD3** (lwn.net/Kernel/LDD3) | Linux device driver internals — free online |
| **man 2 <syscall>** | Authoritative specification for every syscall |
| **LWN.net** | The best ongoing coverage of Linux kernel development |
| **Compiler Explorer** (godbolt.org) | See assembly and syscall patterns from C/Rust code |
| `strace` | Observe every kernel boundary crossing your program makes |
| `/proc` filesystem | Live kernel state; inspect any running process |

---

*Work through these phases sequentially. Each one builds vocabulary that the next phase relies on. By Phase 10 you will be able to look at any running program and reason precisely about what it is doing to the kernel, why, and what the kernel is doing in response.*
