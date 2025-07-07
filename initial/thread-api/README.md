# Thread API

## Thread Creation

The first things you have to be able to do to write a multi-threaded program is to **create new threads**, and thus some kind of *thread creation interface* must exist.

Usually done with four arguments:
    1- thread: Pointer to a structure of type pthread_t. We will use this interface to intreact with the thread. We need to pass it to pthread_create() in order to initialize it;
    
    2- atr: Specify any attributes this thread might have. Like stack size, information about the scheduling priority of the thread;

    3- start_routine: Which function shoud this thread start running in? We call this a function pointer;

    4- arg: The argumentr to be passed to the function where the thread begins execution.

Why always receiving an returning void? void in C, is like an "any" type. It allows us to receive any type and return any type.

## Thread Completion


