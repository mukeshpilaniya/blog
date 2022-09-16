See go tool trace to collect and analyze runtime traces.

GODEBUG
Runtime also emits events and information if GODEBUG environmental variable is set accordingly.

GODEBUG=gctrace=1 prints garbage collector events at each collection, summarizing the amount of memory collected and the length of the pause.
GODEBUG=inittrace=1 prints a summary of execution time and memory allocation information for completed package initialization work.
GODEBUG=schedtrace=X prints scheduling events every X milliseconds.
The GODEBUG environmental variable can be used to disable use of instructihttps://www.ardanlabs.com/blog/2015/02/scheduler-tracing-in-go.htmlon set extensions in the standard library and runtime.

GODEBUG=cpu.all=off disables the use of all optional instruction set extensions.
GODEBUG=cpu.extension=off disables use of instructions from the specified instruction set extension.
extension is the lower case name for the instruction set extension such as sse41 or avx.




https://www.ardanlabs.com/blog/2015/02/scheduler-tracing-in-go.html

https://pkg.go.dev/runtime#hdr-Environment_Variables

https://ydkgo.netlify.app/docs/profiling/godebug/readme/