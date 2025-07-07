# Concurrency and Multithreading - Introduction

There are certqain types of programs we can call **multithreaded**. Each thread is an independent agent running around in this program.

The *OS must support multithreading* with primitives like **locks** and **condittion variables**.

The OS itself was the first concurrent program.

## Threads

Thread is a single point of execution within a program - the same program may have multiple threads - **multithreading**.

A multithreaded program has *more than one point of execution*.

Each thread is pretty much like a separate process - but they *share the same address space and thus can access the same data*.

The state of s single thread is very similar to a process. It has a PC (program counter) that tracks where the program is fetching instructions from.

When swithcing threads within the same CPU, a **context switch** may take place. It happens pretty similarly to a process context switch. The running state of the first thread is saved and the register state of the other thread is restored to run.

**TCBs**: Thread Control Blocks is where we save thread states. Similar to *PCBs* (Process Conttrol Blocks).

Major difference between TCBs and PCBs is that in the context switch we perform between threads, the **address space remains the same** - in the PCBs it changes.

Another major difference between threads and processes concerns *the stack*. There will be **one stack per thread**. Any stack-allocated variables, parameters, return values, etc, will be placed in what is called ***thread-local** storage* - the stack of the relevant thread.

A thread starts, code-wise, excatly where you start it - with the same variable values and all.

---

### Why using Threads?

**Parallelism**: Potential of speeding things up by using different threads to each perform a portion of the work.

**Avoid blocking program progress due to slow I/O**.: Using threads is a natural way to avoid getting stuck. While one thread in your program waits, the CPU scheduler can switch to other threads.

**Multiple processes VS multiple threads**: Threads share an address space, making it easy to share data. Multiple processes is more used for logically separated tasks where little sharing of data structires in memory is needed.

### Issues

Multithreading may lead to **unexpected behaviour**: a thread that was supposed to run first, doesn't; a thread finishes before the others, resulting in wrong results; the programs terminates before one thread completes; etc.

It is also **unpredictable**, it sometimes happens, sometimes doesn t. You may need to run a lot of times for the issue to happen only once - or maybe never happens, only in production.

It runs independently of the caller and is determined by the OS scheduler what runs next - so it is hard to know what will run at any given moment in time.

Many times, the expected result is not achieved.

---

    Dissasembler

    It shows you what assembly instructions make up the program. Helping you understand what is happening underneath the high-level coding.

---

Shared data to, for example, update a variable leads to almost every time the results being different (*nondeterministic*) due to **uncontrolled schedulling**.

### Race Condition

Also called **data race**.

    The results depend on the timing of the code's execution. Bad timing leads to wrong results.

This is the reason for a nondeterministic beahviour - indeterminate result.

### Critical Section

If multiple threads executing the code can result in a race condition, the code is called a *critical section*.

    It is a piece of code that accesses a shared variable/resource and must not be concurrently executed by more than one thread.

What is wanted for this code is a **mutual exclusion** - this property guarantees that if one thread is executed within the critical section, the other will be prevented from doing so.

### Atomicity

**Atomic** is simply expressed with the phrase '*all or nothing*' - or all of the actions occur, or nothing does.

**Transaction**: The grouping of many actions into a single atomic action - databases uses this a lot.

More powerful intructions that, in a single step, did exactly what is needed done and thus removed the possibility of an untimely interrupt.

    An action can not be interrupted mid-instruction - or it doesn't run at all, or it already finished entirely.

**Synchronization primitives**: Few useful instructions. Build multi-threaded code that access *critical sections* in a synchronized and controlled manner, and thus reliably produces the correct result despite the challenging nature of concurrent execution.

### Waiting for Another

Very common issue in the concurrent world. A thread msut wait for another to complete some action before it continues (when in a critical section). This interruption arises, for example, when a process performs a disk I/O and is put to sleep; when the I/O completes, the process needs to be roused from its slumber so it can continue.

    Sleeping/waking interactions is very common in multi-threaded programs and must be carefully handled.

### Recap: Important issues, solutions and terms

**Critical Section**

    Piece of code that accesses a shared resource, usually a variable or data structure. The mutual exclusion works on it to only allow one thread at once to execute the code in this critical section.

**Race Condition (Data race)**

    Problem that arises if multiple threads of execution enter the critical section at roughly the same time and they all try to update the shared data structure. It leads to nondeterministic results.

**Indeterminate Program**

    It consists of one ot more race conditions. The output of the program varies from run to run - depending on which thread ran when.The outcome is nondeterministic.

**Mutual Exclusion**

    The way we avoid the problems below. It guarantees that only a single thread ever enters a critical section, thus avoiding races and resulting in deterministic program outputs.