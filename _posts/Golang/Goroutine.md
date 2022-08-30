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
   1. 1:1 Scheduling (Thread per goroutine)
      - would work but too expensive.
      - memory at least ~32k (memory for user stack and kernel stacks)
      - performance issues (calling syscall)
      - no infinite stacks
   2. N:1 Scheduling (Multilex all goroutine on a single kernel thread)
      - no concurrency (if one goroutine is performing blocking all than the thread will block which means all the other goroutine don't get run )
      - no parallelism (can only use a single CPU core, even if more cpur core are available)
      - example form go in action book
   3. Thread Pool
      - Create thread when needed which means create a thread if there are goroutine to run but all the other threads are busy.
      - Once the thread complete it's execution rather than distroying reuse it.
      - this can  only faster goroutine creation because we can reuse threads
      - but still more memory consumption, performance issue and no infinite stacks. 
   4. M:N Threading Shared Run Queue Schedular
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
      - Let's suppose that one goroutine is made a syscall which is scheduled on one kernel thread, when the kernel thread is complete is execution it will wake up another kernel thread(thread reuse) that will pick up another goroutine and start executing it.This is a ideal scenario but in real case we don't know how much time syscall will take so we can't relay on the kernel thread to wake up another thread, we need some code level logic which will decide when to wake up another thread in case of syscall.

      - When the system call is made to the kernel then it has to decideding points, one is entry point and another one is exit point.
      - Conclusion-
        - Number of kernel thread can be more than number of Core.
        - lightweight goroutines
        - handling of IO and syscalls
        - parallel schedular( executions of goroutines)
        - not scalable (All the kernel level thread try to acess golabl run queue with mutex enable. So due to mutex it's not easy to scale)
    5. Distributed Run Queue Scheduler
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
    6. M:P:N Threading  
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

---
---

### Ankur medium block
### Dmitry 
### Kavya youtube channel
### Go in action Book
https://medium.com/a-journey-with-go/go-goroutine-os-thread-and-cpu-management-2f5a5eaf518a
https://www.morsmachine.dk/go-scheduler
https://www.sobyte.net/post/2022-04/go-scheduling/
http://lroolle.com/notes/scheduling-in-go/
https://medium.com/@ankur_anand/illustrated-tales-of-go-runtime-scheduler-74809ef6d19b
https://dave.cheney.net/2013/06/02/why-is-a-goroutines-stack-infinite
```go
func main(){
   // create go routine
   for _, i := range images{
      go process(i)        // goroutine created
   }
   // wait
   <-ch                    // blocking channel operation
}

func process(image) {
   complexalgo(image)
   f, err := os.Openfile() //blocking system call, network IO
} 
```
Include all blocking all

the decision of go schedular and it's scheduing decisions of what to run when have a huge impact on performance. 

> why have a schedular?


> When to schedule goroutines ?

If there is any operation that should or would affect goroutine execution like goroutine starting and blocking etc...

Blocking system call will block goroutine and underline kernel thread as well

### Goals
   - use a smaller number of kernel threads- kernel threads are expensive to create.
   - support high concurrency- go program should be able to create 1 million go routine per process.
   - leverage parallelism- on P logicall processor, go program should be able to run p goroutine in parallel.

> How to multilex goroutine onto kernel threads ?
>> when to create threads ?

>> How to distribute goroutines across threads ? 
 


 Global and local run queue is allocated into the heap memory.

 ### Limitations of Go schedular
   - FIFO runqueues -> no notion of goroutine priorities (unlike linux kernel)
   - No strong preemption -> no strong fairness or latency guarantees. 
   - Is not aware of the system topology -> no real locality. There is an old NUMA-aware scheduler proposal. Also a suggestion to use LIFO queue so its more likely to have data in that CPU cores cache.



### Logocal Processor -P


Processor are the resource that are require to run go code.