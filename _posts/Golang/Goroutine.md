### Goroutine
1. Go Specifics
2. Schedular
3. Scalability
4. Fairness

  - Goroutine is user level thread
  - Logically implementation same as
    - Os thread
    - Coroutine
    - Green thread

1. Go Specifics
   1. Go Design decisions
   2. Go requirements and constraints
   - goroutine are lightweight (10^6/process)
   - parallel and scalable
   - minimal API  
   - infinite st ack
   - handling of IO, syscall, C calls


2. Implementaion approachs of goroutine
   1. Thread per goroutine
      - would work but too expensive.
      - memory at least ~32k (memory for user stack and kernel stacks)
      - performance issues (calling syscall)
      - no infinite stacks
   2. Thread Pool
      - only faster goroutine creation because we can reuse threads
      - but still memory consumption, performance and no infinite stacks issue
   3. M:N Threading Shared Run Queue Schedular
      - M represents number of OS Thread
      - N represents number of goroutine
      - creation of goroutine is cheap and we can fully control complete lifecycle of goroutine beacuse it's creation in user space.
      - creation of OS thread is expensive and we have less control over it but through multiple thread we can achieve  parallelism.
      - In this model multiple goroutine is multiplex into kernel threads.
      - Goroutine has 3 state running, runnable and blocked state.
      - Goroutine tracking
         - Running
         - Runnable
         - Blocked
            - Blocked on the Channel 
            - Mutexes
            - Network IO
            - Timers
            - System Call

      - if a goroutine is blocked on the channel then the channel is having wait Queue and all blocked goroutine is listed on the wait queue. then it will be placed in global run queue of schedular and OS Thread will pick this in FIFO order.
      - the same mechanism is used for Mutexes, Timers and Network IO.
      -if a goroutine is blocked on the system call then the situation is differnt because we don't know what is happing in the kernel space.Channels are created in the user space so we have full control over it but in the case of system call we don't have.
      - This might cause a deadloack situation, let's suppose that all Running goroutine is required the kernel semaphore to execute some task. but the semaphore is already accquired by a runnable goroutine which is in a run queue of global schedular(due to peremption)but we can't schedule that goroutine because we don't have enough os thread to execute a goroutine.This seems to be a common problem for any schedular with the fixed number of threads.
      - Let's suppose that one goroutine is made a syscall which is scheduled on one kernel thread, when the kernel thread is complete is execution it will wake up another kernel thread(thread reuser) that will pick up another goroutine and start executing it.This is a ideal scenario but in real case we don't know how much time syscall will take so we relay on the kernel thread to wake up another thread, we need some logic code level logic which will decide when to wake up another thread in case of syscall.

      - When the system call is made to the kernel then it has to decideding points, one is entry point and another one is exit point.
      - Conclusion-
        - Number of kernel thread can be more than number of Core.
        - lightweight goroutines
        - handling of IO and syscalls
        - parallel schedular( executions of goroutines)
        - not scalable (All the kernel level thread try to acess golabl run queue with mutex enable. So due to mutex it's not easy to scale)
    4. Distributed Run Queue Scheduler
       > Scalable
       >>- Per thread state (local run queue)
       >>- Still have global run queue  
       
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
          > If number of thread is more than number of cores than what is the problem?  

          In distributed run queue schedular we know that each thread is having their own local run queue which contains information about which goroutine going to be execute next. So if the number of threads are greater than number of cores than during the **work stealing** process each thread has to scan all the thread local run queue so if threads are more than thid process is time consuming and the solution is not efficent so we need to limit thread scanning to a constant which is solve using M:P:N threading model.
    5. M:P:N Threading  
       - P represented Processor that are resource required to run the go code.
       - Generally number of processor is same as number of **logical Processor**.
       - Processor are created before starting of the main go routine. 
       - During the work stealing only fixed number number of queue has to be checked because number of processors are limited.  
       - Conclusion
          - lightweight goroutines
          - handling of IO and System calls
          - Parallel (multiple kernel thread)
          - Scalable (local run queue)
          - efficient (work stealing)
          
https://www.youtube.com/watch?v=YHRO5WQGh0k
https://github.com/sourcegraph/gophercon-2018-liveblog/issues/7
https://kavyajoshi.me/
4. Fairness 






### medium Block
### Kavya youtube channel
### Go in action Book

  