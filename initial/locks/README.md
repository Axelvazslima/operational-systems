# Locks

    Programmers annotate source codes with locks, putting them around critical sections and thus ensure that any critical section executes as if it were a single atomic instruction.

Critical sections are usually the read and/or update of shared resources (variables). It is important that only one thread executes at once in this piece of code.

Locks are **just variables** - to be used it must be declared. This variable holds the state of the lock at any instant in time: it's either available or acquired. Other informations can be stored in a lock like who holds it or a queue for ordering lock acquisition (hidden from the user of the lock).

![Locks and critical section diagram](image.png)

In general, we view threads as entities created by programmers but scheduled by the OS. Locks yield some of that control back to the programmer.

## Pthreads locks

Mutex, is the name given by the POSIX library to provide **mutual exclusion**.

---

### Coarse-grained Locking

There is only one rule. It is easy to manage but terribly unefficient. Everyone waits for everyone.

    A unique big lock protects a big data structure.

Advantages: Simplicity (easier to implement and to assure correctness); less chance of deadlock.

Cons: Chance of bottleneck (lack of parallelism).

---

### Fine-grained Locking

Multiple threads work at once. Way more efficient, but need caution when managing it.

    We split the data structure into smaller pieces and use a single lock for each one of them. Instead of a global lock, we have a specific lock for that smaller critical section in the big picture.

Advantages: High parallelism and performance boost.

Cons: Complexity and easy for humans to commit errors (race conditions and deadlocks); overhead - big number of locks may lead to a little memory and processing overhead.

## Mutex Review

The mutex starts with the fast path which is a thread that tries to acquire the lock using a cheap atomic instruction like compare-and-swap (cas). In case it succeeds, it acquires the lock with minimal overhead - no OS involvement needed.

The spin phase starts if the fast path fails (lock is held). The lock doesn't immediately goes to sleep, it *spins for a fixed number of interactions* - just in case he lock is quickly released.

The sleep phase is the one that happens in case the thread hasn't acquired the lock yet. It makes a system call to the OS to be placed in a wait queue and go to sleep. It is the "slow path", but it is essential for preventing wasted CPU cycles under high contention.

Waking up, the final phase, happens when the lock is finally released. The releasing thread checks if there are any threads in the wait queue - if it does, it makes a system call to wake one of them up.

## Building a Lock

To build it, we will need a partnership between the OS and the hardware in order to make it efficient (hybrid approach).

## Evaluating Locks

To evaluate locks, we must look at some criterias:

    Does it provide mutual exclusion?

The second criteria is *fairness*.

    Does every thread got a shot at holding the lock? Otherwise, it will lead to starvation.

The third, and final, criteria is *performance*:

    Is it efficient?

## Controlling Interrupts

One of the first solutions was to **disable interrupts for critical sections**. It was used when there were only one single processor in the system.

> It will execute as if it is atomical.

Its main advantage is its *simplicity*.

A lot of problems: any calling thread has **access to privileged operations** (turning interruptions on and off) and it leads to bugs; it **does not work on multiprocessor systems**; turning interruptions off for a long period of time can lead to **interrupts becoming lost** - which can lead to serious issues.

It is only used in limited contexts as a **mutual-exclusion primitive**.

## Test-and-Set

Also known as **atomic exchange**.

```C
int TestAndSet(int *old, int new) {
    int old_v = *old;
    *old = new;
    return old_v;
}
```

It is powerful enough to build a simple **spin lock**.

By atomically test and set the value of the lock, we ensure that only one thread acquires the lock. In order to work as expected, it requires a **preemptive scheduler**.

## Evaluating Spin Locks

In order: **Correctness**, **fairness** and, finally, **performance**.

## Compare-and-Swap

```C
void lock(lock_t *lock){
    while(CompareAndSwap(&lock->flag, 0, 1) == 1);
}
```

If the value at the specified address is equal to expected, update the memory location. Else, do nothing.

## Fetch-and-Add - And Lock and Unlock

Atomically increments a value while returning the old value at a praticular address.

```C
int FetchAndAdd(int *ptr){
    int old = *ptr;
    *ptr = old + 1;
    return old;
}
```

---

### Lauer's Law

Given double the time, the same code be produced in half the lines.

---

**Lock and Unlock**: Instead of a single value, it uses a ticket and turn variable in combination to build a lock. When a thread wishes to acquire a lock, it first does ana tomic fetch-and-add on the ticket value; that value is now considered this thread's turn.

The globally shared lock->turn is then used to deterine which thread's turn it is. `myturn == turn`.

    It ensures progress for all threads based on ticket values.

## How and why to Avoid Spinning?

Hadware alone can not solve this spinning problem. It needs **OS support**.

Sometimes it may endlessly spin - specially if the thread holds the lock gets forcefully interrupted -> Main issue: performance.

### Priority Inversion

It is not just about *performance*, but **correctness**.

    A higher-priority thread waiting for a lower-priority thread can temporarily boost the lower thread's priority, thus enabling it to run and overcoming the inversion - priority inheritance.

The solution? Ensure all threads have the **same priority**.

## Just Yield

    What to do when a context switch occurs in a critical section, and threads starts to spin endlessly, waiting for the interrupted lock-holding thread to be run again?

Yield basically just moves the caller from the *running* state to the *ready* state and thus promotes another thread to *running*.

    The thread uses yield to essentially deschedules itself.

This approach **does not address starvation** - a thread may get caught in an endless yield loop while other threads repeatedly enter and exit the critical section.

## Using Queues: Sleeping Instead of Spinning

    Explicitly exert some control over which thread next gets to acquire the lock after the current holder releases it is a must.

`park()` - puts the calling thread to sleep;

`unpark(threadID)` - wakes a particular thread up.

---

### Wakeup/waiting race

With the worng timing, a thread will be about to park, assuming that it should sleep until the lock is no longer held.A switch at that time to another thread could lead to issues if, for example, that thread then released the lock.

The subsequent park by the first thread would then potentially sleep forever.

`setpark()` - It is solved by the thread indicating it is about to park.

---

