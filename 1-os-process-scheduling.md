# OS process scheduling

## What are we scheduling for

- Maximising throughput (total amount of work completed per time unit)
- Minimising wait time (time from work becoming ready until the first point it begins execution)
- Minimising latency/response time (time from work becoming ready until it is finished in the case of batch activity, or until the system responds and hands the first output to user in case of interactive activity)
- Maximising fairness (equal CPU time to each process, or more generally appropriate times according to the priority and workload of each process)

A OS scheduler will often will implement a suitable compromise of different goals as these tend to conflict. In certain cases like embedded systems, schedulers must ensure that processes meet deadlines.

## Different types of OS schedulers

Operating systems may feature up to three scheduler types: **Long term scheduler** also known as admission or high level scheduler, **medium term scheduler** and **short term scheduler**.

### Process scheduler

There are two types of process schedulers:

- Preemptive, which has the ability to pause a running process, move it back to the queue and start a new process.
- Cooperative, which never initiates a context switch from one process to another.

### Long term scheduling

Decides which processes or jobs are admitted to the ready queue in main memory. This scheduler decides which processes are to run on a system, the degree of concurrency at any given time and the split between I/O or CPU intensive processes. It either authorises or delays a new process execution.

### Medium term scheduling

This scheduler swaps out or in processes, this means it will temporarily remove processes from the main memory and place them in a secondary memory and swap them back in when there is more memory available or the process is unblocked. This will happen for several reasons, for example:

- A process which has not been active for some time.
- A low priority process.
- A faulty process.
- A process which takes up a large amount of memory.

When mapping virtual address space to a secondary storage other than a swap file is supported, this scheduler may act as a long term scheduler. It will treat binaries as "swapped out processes" upon their execution, when a segment of the binary ir required it can be swapped in on demand (lazy loaded).

### Short term scheduling

Also known as the CPU scheduler decides which of the ready, in memory processes to be executed (allocated a CPU) after a clock interrupt, I/O interrupt, operating system call or any other form of signal. This scheduler makes decisions more frequently than the other types of schedulers.

### Dispatcher

Module that gives control of the CPU to the process selected by the short term scheduler. Is given control through an interrupt or syscall. Functions of the dispatcher are:

- Context switches. The state (or context) of the process or thread that was previously running is saved, and the initial or previously saved state of the new process is loaded.
- Switching to user mode.
- Jumping to the proper location in the user program to restart it as indicated by the new state.

Unnecessary context switches should be avoided as the processor is idle during these. Dispatch latency is the time it takes for the dispatcher to stop one process and start another.

## Scheduling algorithms (disciplines)

These are used in routers to handle packet traffic, operating systems to share CPU time between threads and processes, disk drives for I/O scheduling etc.

### FIFO (first in first out)

Simplest scheduling algorithm. Queues processes in the order they arrive. Commonly used for task queues.

- Scheduling overhead is minimal.
- Low throughput.
- No starvation as each process gets a chance to be executed after a definite time.
- Turnaround, waiting and response time can be high.

### Priority scheduling

Earliest deadline first (least time to go) is used in real time operating systems to place processes in a priority queue. When a new scheduling event occurs, the queue is searched for the process closes to the deadline, which will be scheduled next.

### Shortest remaining time first

Processes are arranged with the least estimated processing time remaining to be next in queue. It's necessary to have previous knowledge or an estimate of the remaining time required for the process to complete. Waiting time and response time increase as the process' computational requirement's increase.

This algorithm is normally used to maximise throughput, but can create a lot of overhead. For example: when a shorter process arrives during another process' execution, the current process is interrupted, dividing that process into two separate computing blocks. Apart from having the additional overhead of placing each item in a specific spot in the queue.

Waiting and response time increase as the process' computational requirements increase. Longer processes are affected by the fact that turnaround time is based on waiting + processing time.

## Fixed priority preemptive scheduling

OS assigns a fixed priority rank to every process and are arranged per this rank. When a higher priority process comes along, a lower priority process is interrupted.

Starvation of lower-priority processes is possible with large numbers of high-priority processes queuing for CPU time.

### Round-robin scheduling

Scheduler assigns a fixed time unit per process, and cycles through them. Once the time slice is finished, the process is scheduled again unless the process is finished. This algorithm creates extensive overhead, although it has good average response time and balanced throughput.

Starvation can never occur, since no priority is given.

### Multilevel queue scheduling

Used for situations in which processes are easily divided into different groups. For example, a common division is made between foreground (interactive) processes and background (batch) processes.

### Work conserving scheduling

Always tries to keep scheduled resources busy, if there are submitted jobs ready to be scheduled. The opposite would leave the scheduled resources idle despite the presence of jobs ready to be scheduled.

## Problems with scheduling optimisation

### Job shop scheduling

There are n jobs and m identical stations. Each job should be executed on a single machine. This is usually regarded as an online problem.

### Open-shop scheduling

There are n jobs and m different stations. Each job should spend some time at each station, in a free order.

### Flow shop scheduling

There are n jobs and m different stations. Each job should spend some time at each station, in a pre-determined order.

## Operating systems and their process scheduler implementations

Most operating systems use extended or combinations of the scheduling algorithms above.

A **multilevel feedback queue**  is a combination of fixed priority preemptive scheduling, round-robin, FIFO. Threads can dynamically increase or decrease in priority. Every priority level is represented by it's own queue, with round-robin scheduling among the high priority threads and FIFO for the low priority threads.

The kernel always uses whatever resources it needs to ensure proper functioning of the system. In symmetric multiprocessing (SMP) systems, processor (cache) affinity is considered to increase overall system performance, even if it may cause a process itself to run more slowly. This generally improves performance by reducing cache thrashing.

### macOS

It uses a multilevel feedback queue, with four priority bands for threads which are are scheduled preemptively:

- normal
- system high priority
- kernel mode only
- real-time

Useful mac realated links:

- [Threaded programming guide](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/Introduction/Introduction.html#//apple_ref/doc/uid/10000057i)
- [Thread management](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/CreatingThreads/CreatingThreads.html#//apple_ref/doc/uid/10000057i-CH15-SW2)
- [Dispatch](https://developer.apple.com/documentation/dispatch)
- [Concurrency programming guide](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008091-CH1-SW1)

### Windows

Like macOS, Windows uses a multilevel feedback queue.

32 priority levels are defined, 0 through to 31, with priorities 0 through 15 being "normal" priorities and priorities 16 through 31 being soft real-time priorities, requiring privileges to assign. 0 is reserved for the Operating System. Users can select 5 of these priorities to assign to a running application from the Task Manager application, or through thread management APIs.

### Linux

Implements the **[Completely Fair Scheduler](completely-fair-scheduler.md)**, which uses the classic scheduling algorithm fair queuing, originally invented for packet networks.

The fair queuing CFS scheduler has a scheduling complexity of O(log N), where N is the number of tasks in the runqueue. Choosing a task can be done in constant time, but reinserting a task after it has run requires O(log N) operations, because the run queue is implemented as a red-black tree.

## References

- [CFS documentation](https://www.kernel.org/doc/Documentation/scheduler/sched-design-CFS.txt)
- [Wikipedia](https://en.wikipedia.org/wiki/Scheduling_(computing))
- [Inside the Linux 2.6 Completely Fair Scheduler](https://developer.ibm.com/tutorials/l-completely-fair-scheduler/)
