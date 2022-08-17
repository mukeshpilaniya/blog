### Concurrency
  - Support for concurrency has been built directly into Go’s language and runtime.
  - Concurrency in Go is the ability for functions to run independent of each other.
  - When a function is created as a goroutine, it’s treated as an independent unit of
work that gets scheduled and then executed on an available **logical processor**.
  - The scheduler sits on top of the operating system, **binding operating system’s threads to logical processors**
which, in turn, execute goroutines.
  -  The scheduler controls everything related to
which goroutines are running on which logical processors at any given time.

### Process vs Thread vs Goroutine
  - Each process contains at least one thread, and the initial thread for each process is
called the main thread. When the main thread terminates, the application terminates,
because this path of the execution is the origin for the application
  - The operating system schedules threads to run against processors regardless of the process they belong
to. 
  - The primary difference between the process and thread is that threads within the same process run in a shared memory space, while processes run in separate memory spaces.
  - The operating system schedules threads to run against **physical processors**.
  - Go runtime schedules goroutines to run against **logical processors**. Each logical processor is individually bound to a single operating system thread. 
  -  These logical processors are used to execute all the goroutines that are created. Even with a
single logical processor, hundreds of thousands of goroutines can be scheduled to run
concurrently with amazing efficiency and performance.
  - There’s no restriction built into the scheduler for the number of logical processors
that can be created. But the runtime limits each go program to a maximum of 10,000 threads by default. https://pkg.go.dev/runtime/debug#SetMaxThreads
  - https://medium.com/@ankur_anand/illustrated-tales-of-go-runtime-scheduler-74809ef6d19b
### Concurrency vs Parallelism
  - Parallelism can only be achieved when multiple
pieces of code are executing simultaneously against different physical processors.
  - Parallelism is about doing a lot of things at once.
  - Concurrency is about managing a lot of
things at once. 
  - Concurrency is a charaterstic of writing code and parallelism is a property of hardware.
  - Through writing concurrent code you can able to achieve true parallelism.
  - If you want to run goroutines in parallel, you must use more than one logical processor.
  - When there are multiple logical processors, the scheduler will evenly distribute
goroutines between the logical processors. **This will result in goroutines running on
different threads**. But to have true parallelism, you still need to run your program on
a machine with multiple physical processors. 
  - More about concurrency and parallelism visit https://divan.dev/posts/go_concurrency_visualize/#:~:text=One%20of%20the%20strongest%20sides,various%20concurrency%20patterns%20look%20like%3F
  - 