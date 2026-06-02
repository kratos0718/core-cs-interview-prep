# Operating Systems — 80 Questions (basic → advanced)

⭐ = very frequently asked. Read these first if short on time.

---

## A. Basics & Processes

**1. ⭐ What is an Operating System?**
System software that sits between the user/applications and the hardware. It manages resources (CPU, memory, disk, devices) and provides services so programs don't talk to hardware directly. Examples: Windows, Linux, macOS, Android. *Why it exists:* without it, every program would have to manage hardware itself — chaos.

**2. What are the main functions of an OS?**
Process management, memory management, file system management, device (I/O) management, security/protection, and providing a user interface. Think of it as the "manager" that allocates limited resources fairly among many programs.

**3. ⭐ What is a process?**
A program in execution. A program is passive (just a file on disk); a process is active — it has its own memory (code, data, stack, heap), a program counter, registers, and state. Running the same program twice creates two separate processes.

**4. ⭐ Process vs Program?**
A **program** is static code stored on disk. A **process** is that program *loaded into memory and running*, with its own resources. One program → many processes (open Chrome twice = two processes).

**5. ⭐ Process vs Thread?**
A **process** has its own separate memory space. A **thread** is a lightweight unit of execution *inside* a process; threads of the same process **share** that process's memory (code, data, heap) but each has its own stack and registers. *Why threads:* cheaper to create and they can communicate easily via shared memory; *risk:* that sharing causes race conditions (Q on synchronization).

**6. What are the states of a process?**
New (being created) → Ready (waiting for CPU) → Running (executing) → Waiting/Blocked (waiting for I/O) → Terminated. A process cycles between Ready, Running, and Waiting many times during its life.

**7. What is a Process Control Block (PCB)?**
A data structure the OS keeps for each process holding all its info: process ID, state, program counter, CPU registers, memory limits, open files, scheduling info. When the OS switches processes, it saves/restores this. It's the process's "identity card + save file."

**8. ⭐ What is context switching?**
Saving the state (PCB) of the currently running process and loading the state of another, so the CPU can switch between processes. It gives the illusion of multitasking on a single CPU. *Cost:* it's pure overhead — no useful work happens during the switch, so too-frequent switching hurts performance.

**9. What is a context switch's overhead and why?**
The CPU spends time saving registers, updating the PCB, flushing/reloading caches and possibly the TLB (memory translation cache), and loading the new process's state — all of which is wasted on no actual computation.

**10. What is a system call?**
A controlled way for a program to request a service from the OS kernel (e.g. read a file, create a process, allocate memory). It switches the CPU from user mode to kernel mode. Examples: `read()`, `write()`, `fork()`, `open()`. Programs can't touch hardware directly — they must ask via system calls.

**11. ⭐ User mode vs Kernel mode?**
Two CPU privilege levels. **Kernel mode**: full access to hardware and all instructions (the OS runs here). **User mode**: restricted; apps run here and must use system calls to request privileged operations. This protects the system — a buggy app can't directly crash the hardware or other processes.

**12. What is the kernel?**
The core part of the OS that always runs, with full hardware access. It manages processes, memory, devices, and system calls. Everything else (UI, apps) sits on top of it.

**13. Monolithic vs Microkernel?**
**Monolithic**: the whole OS (drivers, file system, scheduling) runs in kernel space as one big program — fast (no message-passing overhead) but a bug anywhere can crash everything (Linux). **Microkernel**: only the bare minimum runs in kernel space; the rest runs as user-space services that communicate via messages — more stable/modular but slower (more context switches).

**14. What is `fork()`?**
A system call that creates a new process by duplicating the calling (parent) process. It returns twice: 0 in the child, and the child's PID in the parent. The child is a near-copy of the parent. Often followed by `exec()` to replace the child's program with a new one.

**15. What is a zombie process?**
A process that has finished executing but still has an entry in the process table because its parent hasn't read its exit status (via `wait()`). It's "dead but not cleaned up." Harmless individually, but many zombies leak process-table slots.

**16. What is an orphan process?**
A process whose parent terminated before it did. The OS re-parents orphans to the `init` (PID 1) process, which adopts and cleans them up. (Contrast: a zombie's parent is alive but neglectful; an orphan's parent is gone.)

**17. What is a daemon?**
A background process that runs continuously without user interaction, usually providing a service (e.g. a web server `httpd`, print spooler, `cron`). Typically started at boot.

---

## B. Scheduling

**18. ⭐ What is CPU scheduling?**
Deciding which ready process gets the CPU next, when there are more processes than CPUs. The goal is to maximize CPU use and throughput while minimizing wait/response time. Done by the **scheduler**.

**19. Preemptive vs Non-preemptive scheduling?**
**Preemptive**: the OS can forcibly take the CPU from a running process (e.g. when a higher-priority process arrives or a time slice ends) — responsive, used by modern OSes. **Non-preemptive**: a process keeps the CPU until it finishes or blocks voluntarily — simpler but a long process can starve others.

**20. Explain FCFS scheduling.**
First-Come-First-Served: processes run in arrival order, non-preemptive. Simple but suffers the **convoy effect** — one long process makes everyone behind it wait (like one slow cart blocking a checkout line).

**21. Explain SJF (Shortest Job First).**
Run the process with the smallest burst time next. Gives the *minimum average waiting time* (provably optimal) — but requires knowing burst times in advance (you usually don't) and can **starve** long processes.

**22. Explain Round Robin.**
Each process gets a fixed **time quantum**; if it doesn't finish, it goes to the back of the ready queue. Preemptive, fair, good for time-sharing. *Trade-off:* small quantum = more responsive but more context-switch overhead; large quantum → behaves like FCFS.

**23. Explain Priority Scheduling.**
Each process has a priority; the highest-priority ready process runs. Problem: **starvation** of low-priority processes. Fix: **aging** — gradually raise the priority of waiting processes so they eventually run.

**24. ⭐ What is starvation? What is aging?**
**Starvation**: a process waits indefinitely because others keep getting preferred (e.g. low priority forever skipped). **Aging**: the solution — increase a process's priority the longer it waits, guaranteeing it eventually runs.

**25. Turnaround time vs Waiting time vs Response time?**
**Turnaround** = completion time − arrival time (total time in system). **Waiting** = turnaround − burst time (time spent waiting in ready queue). **Response** = time from arrival to *first* getting the CPU (matters for interactivity).

**26. What is the convoy effect?**
In FCFS, short processes get stuck waiting behind one long process, tanking average waiting time. Round Robin / SJF mitigate it.

---

## C. Synchronization & Deadlock (very high-frequency)

**27. ⭐ What is a race condition?**
When two or more threads/processes access shared data concurrently and the final result depends on the *timing/order* of their execution. Example: two threads both do `count = count + 1` — if interleaved badly, one update is lost. The root of most concurrency bugs. *(This is exactly the bug class behind my open-source async fixes.)*

**28. ⭐ What is the critical section?**
The part of code that accesses shared resources and must NOT be executed by more than one thread at a time. The goal of synchronization is to ensure **mutual exclusion** over the critical section.

**29. What three requirements must a critical-section solution satisfy?**
**Mutual exclusion** (only one in the critical section at a time), **progress** (if no one's inside, someone waiting must be allowed in — no needless blocking), and **bounded waiting** (a thread can't wait forever — no starvation).

**30. ⭐ What is a mutex?**
"Mutual exclusion" lock. A thread **locks** it before entering the critical section and **unlocks** after. Only one thread can hold it at a time; others block until it's released. Ownership matters — only the locker should unlock.

**31. ⭐ What is a semaphore?**
A synchronization variable (a counter) with two atomic operations: `wait()`/P (decrement; block if it'd go negative) and `signal()`/V (increment; wake a waiter). **Binary semaphore** (0/1) ≈ a lock. **Counting semaphore** allows up to N threads (e.g. N identical resources).

**32. Mutex vs Semaphore?**
A **mutex** is a locking mechanism with ownership — the locker unlocks it; used for mutual exclusion. A **semaphore** is a signaling mechanism (a counter) with no ownership — any thread can signal; used to control access to N resources or to coordinate ordering between threads.

**33. ⭐ What is a deadlock?**
A situation where a set of processes are all blocked, each waiting for a resource held by another in the set — so none can proceed. Classic: P1 holds A and wants B; P2 holds B and wants A.

**34. ⭐ What are the four (Coffman) conditions for deadlock?**
All four must hold simultaneously: **Mutual exclusion** (resources non-shareable), **Hold and wait** (a process holds resources while waiting for more), **No preemption** (resources can't be forcibly taken), **Circular wait** (a cycle of processes each waiting on the next). Break any one → no deadlock.

**35. How do you handle deadlocks?**
Four strategies: **Prevention** (ensure one Coffman condition can't hold, e.g. impose resource ordering to kill circular wait), **Avoidance** (Banker's algorithm — only grant requests that keep the system in a safe state), **Detection & recovery** (let them happen, detect cycles, then kill/preempt), **Ignore** (the "ostrich algorithm" — what most OSes actually do, since deadlocks are rare).

**36. What is the Banker's Algorithm?**
A deadlock-*avoidance* algorithm: before granting a resource request, it checks whether doing so leaves the system in a **safe state** (one where all processes can still finish in some order). If not safe, the request waits. Named because it's like a banker only lending money if all clients can still be satisfied.

**37. What is a safe state?**
A state in which there exists at least one ordering of processes such that each can get its needed resources, run, and release them, letting the next finish. A safe state guarantees no deadlock; an unsafe state *may* lead to one.

**38. What is livelock?**
Like deadlock, processes can't proceed — but unlike deadlock, they're *not blocked*; they keep changing state in response to each other and make no progress (two people stepping side-to-side in a hallway forever). 

**39. What is busy waiting / spinlock?**
A thread repeatedly checks a condition in a loop instead of sleeping (a **spinlock**). Wastes CPU but avoids the overhead of context-switching to sleep — good only when the wait is expected to be very short.

**40. Producer-Consumer problem?**
A classic sync problem: producers add items to a shared bounded buffer, consumers remove them. You must prevent producing into a full buffer or consuming from an empty one, and prevent simultaneous access. Solved with a mutex (for the buffer) + two counting semaphores (empty slots, full slots).

**41. Reader-Writer problem?**
Multiple readers can access shared data simultaneously (reading is safe), but a writer needs exclusive access. The challenge is allowing concurrent reads while ensuring writes are exclusive — and avoiding starving writers (or readers).

**42. Dining Philosophers problem?**
5 philosophers, 5 forks, each needs 2 adjacent forks to eat. If all grab their left fork at once, all wait forever for the right — deadlock. Illustrates deadlock + resource contention. Fixes: limit to 4 at the table, or make one philosopher pick up forks in the opposite order (breaks circular wait).

---

## D. Memory Management

**43. ⭐ What is virtual memory?**
An abstraction that gives each process the illusion of a large, private, contiguous memory space, even though physical RAM is limited and shared. Parts of a process live on disk (swap) and are loaded into RAM on demand. Lets programs bigger than RAM run, and isolates processes from each other.

**44. ⭐ What is paging?**
A memory-management scheme that divides virtual memory into fixed-size **pages** and physical memory into same-size **frames**. The OS maps pages to frames via a **page table**. Eliminates external fragmentation (any free frame fits any page) and enables virtual memory.

**45. What is a page fault?**
Occurs when a process accesses a page that's not currently in RAM (it's on disk or not loaded). The OS pauses the process, fetches the page from disk into a frame, updates the page table, and resumes. Too many page faults = **thrashing**.

**46. ⭐ What is thrashing?**
When the system spends more time swapping pages in/out of disk than doing actual work, because processes don't have enough frames. Performance collapses. Caused by over-committing memory (too many processes). Fixed by reducing the degree of multiprogramming or adding RAM.

**47. What is the page table?**
A per-process data structure mapping virtual page numbers to physical frame numbers. Consulted on every memory access to translate addresses. Can be large, so multi-level page tables and the TLB exist to manage it.

**48. ⭐ What is the TLB (Translation Lookaside Buffer)?**
A small, fast hardware cache that stores recent virtual→physical page translations, so the CPU doesn't hit the (slow, RAM-resident) page table on every access. A **TLB hit** = fast; a **TLB miss** = consult the page table (slower). Exploits locality of reference.

**49. Paging vs Segmentation?**
**Paging**: fixed-size blocks (pages), invisible to the programmer, eliminates external fragmentation but has internal fragmentation. **Segmentation**: variable-size, logical units (code segment, stack segment) matching the program's structure — meaningful to the programmer but causes external fragmentation. Modern systems often combine both.

**50. Internal vs External fragmentation?**
**Internal**: wasted space *inside* an allocated block (you allocated a fixed page but the process only used part of it). **External**: enough total free memory exists but it's split into small non-contiguous holes, so a large request can't be satisfied. Paging removes external fragmentation; segmentation suffers from it.

**51. ⭐ Page replacement algorithms — name and explain.**
When RAM is full and a new page is needed, one must be evicted. **FIFO**: evict the oldest-loaded page (simple, but can suffer Belady's anomaly). **LRU (Least Recently Used)**: evict the page unused for the longest time (good, exploits locality, but costlier to track). **Optimal**: evict the page that won't be used for the longest future time (best possible, but needs future knowledge — only a theoretical benchmark).

**52. What is Belady's anomaly?**
A counterintuitive case where *increasing* the number of frames *increases* the number of page faults — occurs with FIFO replacement. LRU and Optimal don't suffer it.

**53. What is demand paging?**
Loading pages into memory only when they're actually accessed (on demand), rather than loading the whole program upfront. Saves memory and startup time; the first access to each page causes a page fault.

**54. What is swapping?**
Moving an entire process (or pages) between RAM and disk (the **swap space**) to free up memory. Lets more processes run than fit in RAM, at the cost of slow disk I/O.

**55. Logical vs Physical address?**
**Logical (virtual) address**: generated by the CPU/program; what the program "sees." **Physical address**: the actual location in RAM. The **MMU (Memory Management Unit)** translates logical→physical at runtime using the page table.

---

## E. File Systems & I/O

**56. What is a file system?**
The OS component that organizes and stores files on disk: how data is laid out, directories, metadata (name, size, permissions, timestamps), and how to read/write/locate files. Examples: NTFS, ext4, FAT32, APFS.

**57. What is an inode?**
In Unix file systems, a data structure storing a file's **metadata** (permissions, owner, size, timestamps, and pointers to the data blocks) — but NOT the filename. The directory maps names → inode numbers. That's why you can have multiple names (hard links) for one file.

**58. Hard link vs Soft (symbolic) link?**
A **hard link** is another directory entry pointing to the *same inode* — the file exists as long as any hard link remains; can't cross file systems or link directories. A **soft link** is a separate file containing a *path* to the target — can cross file systems and link directories, but breaks if the target is deleted (dangling).

**59. What is a file descriptor?**
A small non-negative integer the OS gives a process to refer to an open file (or socket/pipe). 0/1/2 are stdin/stdout/stderr by convention. There's a per-process limit (RLIMIT_NOFILE); leaking them (opening without closing) eventually exhausts it. *(This is exactly my agno file-handle-leak fix.)*

**60. Sequential vs Direct (random) access?**
**Sequential**: read/write data in order, start to end (like a tape). **Direct/random**: jump to any position instantly (like an array/disk). 

---

## F. Advanced / Misc

**61. Multiprogramming vs Multitasking vs Multiprocessing?**
**Multiprogramming**: keep multiple programs in memory, run one while another waits for I/O (maximizes CPU use). **Multitasking**: time-sharing — rapidly switch between tasks for interactivity (a multiprogramming refinement). **Multiprocessing**: multiple physical CPUs/cores running processes truly in parallel.

**62. Concurrency vs Parallelism?**
**Concurrency**: dealing with many tasks at once by interleaving them on (possibly) one CPU — structure. **Parallelism**: literally executing multiple tasks at the same instant on multiple cores — execution. You can have concurrency without parallelism (single core, time-slicing). *(This distinction is central to async programming and my OSS bug fixes.)*

**63. ⭐ What is a thread? Why use multithreading?**
A thread is the smallest unit of CPU execution within a process. Multithreading lets one program do multiple things "at once" (responsiveness, parallel use of cores, efficient I/O overlap) while sharing memory cheaply. Downside: shared memory → synchronization complexity and race conditions.

**64. User-level vs Kernel-level threads?**
**User-level**: managed by a library in user space, fast to create/switch, but the kernel sees only one process — if one thread blocks on I/O, all block. **Kernel-level**: managed by the OS, can run truly in parallel and one blocking doesn't stop others, but heavier to create/switch.

**65. What is a process address space layout?**
A process's memory is divided into: **Text/Code** (the instructions), **Data** (global/static variables), **Heap** (dynamically allocated memory, grows up), and **Stack** (function calls/local variables, grows down). Heap and stack grow toward each other.

**66. ⭐ Stack vs Heap memory?**
**Stack**: stores local variables and function-call frames; fast (just move a pointer); automatically managed (freed when a function returns); limited size (stack overflow if too deep). **Heap**: for dynamic allocation (`malloc`/`new`); flexible size; manually managed (you must free, or rely on GC); slower; fragmentation possible.

**67. What causes a stack overflow?**
The call stack exceeds its limit — usually from **infinite/too-deep recursion** or allocating huge local arrays. Each call adds a stack frame; without a base case, recursion never returns and the stack grows until it overflows.

**68. What is an interrupt?**
A signal to the CPU that an event needs attention (e.g. keyboard press, I/O completion, timer). The CPU pauses its current work, saves state, runs an **interrupt handler (ISR)**, then resumes. Interrupts let the CPU respond to events without constantly polling.

**69. Interrupt vs Polling?**
**Polling**: the CPU repeatedly checks a device's status — wastes cycles. **Interrupt**: the device notifies the CPU only when ready — efficient. Interrupts are preferred for infrequent events; polling can win for very frequent ones.

**70. What is DMA (Direct Memory Access)?**
A mechanism letting devices transfer data directly to/from RAM **without** involving the CPU for each byte. The CPU sets it up, then the DMA controller does the transfer and interrupts the CPU when done — freeing the CPU for other work during large I/O.

**71. What is spooling?**
Simultaneous Peripheral Operations On-Line: buffering data for a slow device (like a printer) on disk so the program isn't blocked waiting. Print jobs queue up and print in the background.

**72. What is a bootloader / the boot process?**
On power-on, firmware (BIOS/UEFI) runs, finds the boot device, and loads the **bootloader** (e.g. GRUB), which loads the OS kernel into memory and starts it. The kernel then initializes hardware and starts the first process (`init`/`systemd`).

**73. Cooperative vs Preemptive multitasking?**
**Cooperative**: a process keeps the CPU until it voluntarily yields — one misbehaving program can freeze the system (old Windows/Mac). **Preemptive**: the OS forcibly time-slices — robust, used by all modern OSes.

**74. What is a real-time operating system (RTOS)?**
An OS that guarantees responses within strict time deadlines. **Hard RTOS**: missing a deadline is catastrophic (pacemaker, airbag). **Soft RTOS**: deadlines are important but occasional misses are tolerable (video streaming). Determinism matters more than throughput.

**75. What is the dispatcher?**
The OS module that actually gives CPU control to the process the scheduler selected: it performs the context switch, switches to user mode, and jumps to the right instruction. **Dispatch latency** is the time this takes.

**76. What is CPU-bound vs I/O-bound?**
**CPU-bound**: a process spends most time computing (needs the CPU). **I/O-bound**: spends most time waiting for I/O (disk/network). Good scheduling mixes them so the CPU stays busy while I/O-bound processes wait.

**77. What is a working set?**
The set of pages a process is actively using in a recent time window. Keeping a process's working set in RAM prevents thrashing; the **working-set model** uses this to decide how many frames each process needs.

**78. What is copy-on-write (COW)?**
An optimization where, after `fork()`, parent and child *share* the same physical pages (marked read-only) instead of copying. A page is only actually copied when one of them *writes* to it. Saves memory and time when the child immediately `exec()`s anyway.

**79. ⭐ What happens when you run a program? (end-to-end)**
The OS loads the executable from disk into memory, creates a process (PCB), sets up its address space (code, data, heap, stack), loads needed libraries, the loader sets the program counter to `main`/entry, the scheduler eventually gives it the CPU, and it runs — making system calls for I/O/memory as needed, until it terminates and the OS reclaims its resources.

**80. ⭐ Difference between `mutex`, `semaphore`, and `monitor`?**
**Mutex**: a lock with ownership for mutual exclusion. **Semaphore**: a signaling counter (no ownership) for N resources / ordering. **Monitor**: a higher-level construct (in languages like Java's `synchronized`) that bundles shared data + the lock + condition variables together, so mutual exclusion is automatic — safer and less error-prone than raw semaphores.

---

## ⭐ If you memorize/understand only 12 for OS:
Process vs thread (5) · Context switch (8) · User vs kernel mode (11) · Race condition (27) · Critical section (28) · Mutex vs semaphore (32) · Deadlock + 4 conditions (33–34) · Virtual memory (43) · Paging (44) · Thrashing (46) · LRU/page replacement (51) · Stack vs heap (66).
