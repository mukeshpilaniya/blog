
### Process
---
  - Process will have various attributes like
    - Process ID
    - Process State
    - Process Priority
    - Program Counter
    - General purpose register
    - List of open files
    - List of open devices
    - Protection information
    - List of child process
    - Pending alarms
    - Signals and signal handlers
    - Accounting information
  

### Thread
---
  - Thread is a light weight process and an thread will share resources of the process like code, data, global variables, files and memory address space among all the thread within the process but **stack and register** cannot be shared, every thread have it's own stakcs and registers.
  - Advantages of threads
    - Enhanced throughput of system
    - Imporve responsiveness
    - Faster context switching due to less attributes
    - Effetive utilisation of multiprocessor system
    - Resource sharing (Code, Data, Address Space, Files, Global Variables)
  - User level thread also known as green thread, coroutine in C, goroutinr in Go and fiber in Ruby.
  


| Process      | Thread (Kernel Thread) | Goroutine / (User Thread)     |
| :---        |    :----:   |          ---: |
| Program under execution is known as a process. it should reside in the main memory and occupies cpu to execute instructions and should be in active state. | Kernel level thread is a light weight process and implemented by the Operating system| User level thread also light weighted process but implemented by the user/programmer/programming language |
| Context switching time between processes is more and creating a process will take more time. | Context switching time between kernel level thread will take less time than context switching between process also creation of kernel level thread also take less time than creation of a process. | User level thread is having less context switching time and creation of user level thread will take less time than kernel level thread. |
| OS schedular is responsible for scheduling process | The kernel thread scheduler is in charge of scheduling kernel threads. | User/Programming Schedular (Golang schedular) is responsible for shceduling user thread/goroutines. |


> So it's more efficient to create multiple user thread(goroutine ) inside one process as compare to the process creation which is time consuming and resource intensive.

### Go Specific 
---
  - In Go user level thread is known as **Goroutine**.
  - Go has took some decision when creating goroutines
    - Easy to create
    - Lightweight
    - Parallel execution
    - Scalable
    - Handling of blocking calls
      - Sending and Receing on Channel
      - Network IO calls
      - Blocking System calls/ syscalls
      - Timers
      - Mutexes
    - Efficient (work stealing)

> Why Go have a schedular ?  

Go uses user level thread known as **goroutine** , which are lighter and cheaper than kernel level thread. for example creation of initial goroutine will take 2KB of stack size and kernel level thread will take 8KB of stack size. Also goroutine has faster creation, destruction and faster context switches than kernel thread So go schedular needs to exits to schedule goroutine.OS can't schedule user level thread, OS only know about kernel level thread. Go schedular multiplexes goroutines to kernel level threads, which will run on the differnet CPU core.

> When go schedular schedule goroutines ?

If there is any operation that should or would affect goroutine execution like goroutine starting and blocking etc...
  
> How go schedular will multiplexes goroutines into kernel threads.

1. Thread per Core