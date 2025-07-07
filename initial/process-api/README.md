# Process API

## The fork() System Call

A **strange** routine used to create procecesses.

Every processes has its own *pid* - Process Identifier. It is used to name the process. 

The fork() is a way to create a new process, but the process crated is an *almost exact copy of the calling process*.

> Parent --> Child

The child doesn't start running at main(), but it comes into life like it had called fork() itself.

The output is **not deterministic**. When a process is created there is two important processes (the parent and the child) - but, if we only have one CPU, then either the child or the parent runs at that point. The CPU **scheduler** determines which process runs at a given moment.

Nondeterminism leads to issues, patricularly in *multi-threaded programs (concurrency)*.

## The wait() System Call

Its is important for a parent to wait for a child process to finish what it has been doing. So wait() and waitpid(), more complete, do that.

It delays the parent ptocess execution until the child finishes executing.

This system call makes the fork() system call have a deterministic output.

## The exec() System Call

Importnt when you want to run a program that is *different from the calling program*.

There are six variations, such as execv(), execvp(), execvpe(), execl(), execlp() and execle() - for different purposes.

It also a strange function.

Given the name of a executable and some arguments, it loads code and static data from that executabe and overwrites its current code segment and current static data with it; the heap and stack and other parts of the memory space of the program re re-initialized. Then, the OS simply runs that program, passing in any arguments as the argv of that process. But, it doesn't create a new process: it transforms thr currently running program into a different running program.

## Why?

The separation of fork() and exec() is essential in building a UNIX shell. It lets the shell run code *after the call to fork* but *before the call to exec()*

The shell is nothing but a user program: it shows you a prompt and waits for your command.

---
### The pipe() System Call

The output of the process is connected to an inkernel pipe (e.g. queue). The output of a process is used as input of the next one.

## Process Control And Users

There are others interfaces for interacting with processes in UNIX systems, such as *kill()*.

The *kill()* system call is used to send signals to processes like die, stop, pause and other imperative useful imperatives.

The *signal()* system call is used to "catch" various signals - when a particular signal is delivered to a process it will suspend its normal execution and run a particular piece of code in response to the signal.

**user** -> The user logs in to gain access to system resources. It is used for security reasons, so not everyone can send signals. Users generally can only control their own processes; it is the job of the OS to parcel out resources to each user to meet overall system goals.

## Useful Tools

*ps*, *man*, *top*, *kill*...





