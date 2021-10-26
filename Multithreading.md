# Hull Flattening & Clipper Multi-Threading (Part A)

In part A each hull is generated in a single thread. This includes the import of the object file, the
flattening of the geometry, and the generation of the external hull using Clipper. However, multiple hulls
can be generated at once by using multiple threads.

## Libraries

This multi-threading implementation uses the `<thread>` and `<mutex>` libraries from the standard template library.
No other libraries are required, so this should be well supported on all machines.

## Thread Pool

To optimise the usage of the CPU we do not create a new thread for every input file, instead we create one thread for every
available hardware thread on the host machine, as returned by the `std::thread::hardware_concurrency()` function.
The input files are placed into a `queue`, which is provided to each thread in the pool. Once each thread has finished
operating on a file they attempt to fetch a new file from the start of the queue. Hence, the input files will be balanced
between all of the threads, and there is no chance of a single large file blocking others from being processed.

The act of popping a new value off the stack is safely within a critical section, as is the creation of a new Hull at the end of the
global list of Hulls. The individual hull generate functions are passed a reference to this Hull object, and they save the results of
Clipper to that object directly, so there are no race conditions.
