# Thread API

## Thread Creation

The first things you have to be able to do to write a multi-threaded program is to **create new threads**, and thus some kind of *thread creation interface* must exist.

Usually done with four arguments:

    1- thread: Pointer to a structure of type pthread_t. We will use this interface to intreact with the thread. We need to pass it to pthread_create() in order to initialize it;
    
    2- atr: Specify any attributes this thread might have. Like stack size, information about the scheduling priority of the thread;

    3- start_routine: Which function shoud this thread start running in? We call this a function pointer;

    4- arg: The argument to be passed to the function where the thread begins execution.

Why always receiving an returning void? void in C, is like an "any" type. It allows us to receive any type and return any type.

## Thread Completion

You must do something special if you want to wait for a thread to complete: call the routine `pthread_join()`.

    This routine takes two arguments: The first is of type pthread_t and is used to specify which thread to wait for; the second one is a pointer to the return value you expect to get back.

### Procedure Call

Create a task and immediatelly call to `pthread_join()`.

## Locks

Probably the *most useful set of functions* provided by the POSIX threads library are those for **providing mutual exclusion to a critial section via `locks`**.

    Used for regions of code that needs to be protceted to ensure correct operation - critical sections.

### How does it work

``` C
pthread_mutex_t lock;
pthread_mutex_lock(&lock);
x = x + 1 // critical section example
pthread_mutex_unlock(&lock);
```

If no other thread holds the lock when `pthread_mutex_lock()` is called, the thread will hold the lock and enter the critical section;

If another thread holds the lock, the thread trying to grab it will not return from the call until it has acquired the lock;

    Only the thread with the lock should call unlock.

### Proper Initialization

All locks must be properly initialized in order to *guarantee that they have the correct values to begin with* and thus work as desired when lock and unlock are called.

One way to initialize locks is to use `pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZE`.

The dynamic way to do this is to make a call to `pthread_mutex_init()`.

```C
int rc = pthread_mutex_init(&lock, NULL) //(address of the lock itself, optional set of attributes)

assert(rc == 0); // always check success!
```

When you are done with the lock, call `pthread_mutex_destroy()`.

Minimally, *use wrappers* - which assert that the routine succeeded; more sophisticated (non-toy) programs, which can't simply exit when something goes wrong, **should check for failure and do something appropriate** when a call does not succeed.

### Two Other Routines of Interest

```C
// Returns failure if the lock is already held
int pthread_mutex_trylock(pthread_mutex_t *mutex);

//Returns after a timeout or after acquiring the lock - whichever happens first;
// timeout set to 0 and it works like a trylock;
int pthread_mutex_timedlock(pthread_mutex_t *mutex, struct timespec *abs_timeout);
```

These two calls are used in **lock acquisition**.

## Condition Variables

Useful when some kind of signaling must take place between threads - **if one thread is waiting on another to do something before it can continue**.

Used in addition to a lock associated with the condition.

`pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex)` puts the calling thread to sleep and thus waits for another thread to signal it.

`pthread_cond_signal(pthread_cond_t *cond)` signals that something in the program has changed.

Always make sure to have the lock held, in other to avoid race condition.

```C
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;

Pthread_mutex_lock(&lock);
while (ready == 0)
    Pthread_cond_wait(&cond, &lock);
Pthread_mutex_unlock(&lock);
```

**See *waking up as a hint* that something changed, not an absolute fact**.

**Bad practice**:

```C
while (ready == 0); //spin

ready = 1;
```

Do not use flags to synchronyze between threads: it is very easy to have errors and it performs poorly.

## Guidelines

    Keep it simple;

    Minimize thread interactions;

    Initialize locks and condition variables;

    Check your return codes;

    Be careful with how you pass arguments to, and return values from, threads;

    Each thread has its own stack;

    Always use condition variables to signal between threads.