### Goroutine
1. Go Specifics
2. Schedular
3. Scalability
4. Fairness
5. Stacks
6. Future

  - Goroutine is Logically a Thread of execution
  - Logically same as
    - Os thread
    - Coroutine
    - Green thread

1. Go Specifics
   1. Go Design decisions
   2. Go requirements and constraints
   - goroutine are lightweight (10^6/process)
   - parallel and scalable
   - minimal API  
   - infinite stack
   - handling of IO, syscall, C calls


2. Implementaion approachs of goroutine
   1. Thread per goroutine
      - would work but too expensive.
      - memory at least ~32k (memory for user stack and kernel stacks)
      - performance issues (calling syscall)
      - no infinite stacks
   2. Thread Pool
      - only faster goroutine creation
      - but still memory consumption, performance and no infinite stacks issue
   3. M:N Threading Shared Run Queue
      - M represents number of OS Thread
      - N represents number of goroutine
      - creation of goroutine is cheap and we can fully control complete lifecycle of goroutine beacuse it's creation in user space.
      - creation of OS thread is expensive and we have less control over it but through multiple thread we can achieve parallelism.
      - Goroutine has 3 state running, runnable and blocked state.
      - if a goroutine is blocked on the channel then the channel is having wait Queue and all blocked goroutine is listed on the wait queue. then it will be placed in global run queue of schedular and OS Thread will pick this in FIFO order.
      - the same mechanism is used for Mutexes, Timers and Network IO.
      -if a goroutine is blocked on the system call then the situation is differnt because we don't know what is happing in the kernel space.Channels are created in the user space so we have full control but in the case of system call we don't have.
      - This might cause a deadloack situation, let's suppose that all the goroutine is required the kernel semaphore to execute some task. but the semaphore is already accquired by a goroutine which is in a run queue of global schedular(due to peremption)but we can't schedule that goroutine because we don't have enough thread to execute a goroutine.
      - When the system call is made to the kernel then it has to decideding points, one is entry point and another one is exit point.
      - Conclusion-
        - Number of thread might be more than number of Core.
        - lightweight goroutines
        - handling of IO and syscalls
        - parallel executions of goroutines4
        - There is problem with golbal mutex due to it's not going to be scale.
        - not scalable
    4. Distributed Run Queue Scheduler
       -  
       - Poll Order
         - what is the next goroutine to run
           1. Local Run Queue
           2. Global Run Queue
           3. Network Poller
           4. Work Stealing
        - Conclusion
          - lightweight goroutines
          - handling of IO and SystemCalls
          - Parallel
          - Scalable
          - Thread in syscalls (#threads > #cores)
    5. M:P:N Threading
       - P represented Processor that are resource required to run the go code.
       - Generally number of processor is same as number of cores.
       - Processor are created before starting of the main go routine.
       - During the work stealing only fixed number number of queue has to be checked because number of processors are limited.  
       - Conclusion
          - lightweight goroutines
          - handling of IO and SystemCalls
          - Parallel
          - Scalable
          - efficient
https://www.youtube.com/watch?v=YHRO5WQGh0k
https://github.com/sourcegraph/gophercon-2018-liveblog/issues/7
https://kavyajoshi.me/
4. Fairness 






### medium Block
### Kavya youtube channel
### Go in action Book

  