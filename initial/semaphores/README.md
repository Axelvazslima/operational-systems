# Semaphores

Semaphores is like a protected "counter" that manages permissions.

## Fundamental Operations

It works with two atomic operations.

### Wait

`wait()` decrements the semaphore's value.

It is what a thread does to ask to access the resource.

If the semaphore is > 0, is goes in and the decrement occurs. Else, git is blocked and have to wait until the semaphore is bigger than 0.

```C
wait(Semaphore s){
    while(s.value <= 0){
        //stays blocked, waiting;
    }
    s.value--;
}
```

### Post

`post()` increments the semaphore value.

It is what happens when the thread stops using the resource - unblocking it to others.

```C
post(Semaphore s){
    s.value++;
    // if there is any thread waiting, it wakes them up
}
```

## Counting Semaphore

    It is perfect for managing a pool of identic resources.

It can assume a non-negative integer value.

## Binary Semaphore

It only assumes 0 or 1.

    It works very smilar to a Mutex. Only a thread can access the resource at once.

It is used for protecting a critical section.

## Semaphore VS Mutex

### Mutex (Lock)

It is about **property**.

    The same thread that locks the locks unblocks them.

### Semaphore

It is about **signaling**.

    A thread can decrement the semaphore (wait()) and another thread can increment the it (post()).

It is more flexible for complex synchronization problems, like the **producer-consumer** issue, which the producer signals (post) that he had produced an item for the consumer (wait)

