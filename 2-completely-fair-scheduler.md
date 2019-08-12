# Completely fair scheduler

Linux's completely fair scheduler (CFS) provides processing[1] time to tasks in a fair or balanced way. It models an "ideal, precise multi-tasking CPU" on real hardware.

This "ideal, precise multi-tasking CPU" can be summed up as a CPU that has 100% physical power and which can run each task at precise equal speed, in parallel, each at 1/nr_running speed (e.g. if there are 2 tasks running, then it runs each at 50% physical power).

To determine the balance of the allocated time to any given tasks, the concept of _virtual runtime_ is introduced. The virtual runtime of a task specifies when its next time-slice would start execution on the ideal multi-tasking CPU. In practice, the virtual runtime of a task is its actual runtime normalized to the total number of running tasks.

The smaller a task's virtual runtime (the smaller amount of time a task has been permitted access to the processor), the higher its need for the processor and given more priority.

From the scheduler's [design document](https://www.kernel.org/doc/Documentation/scheduler/sched-design-CFS.txt):
> In CFS the virtual runtime is expressed and tracked via the per-task p->se.vruntime (nanosec-unit) value.  This way, it's possible to accurately timestamp and measure the "expected CPU time" a task should have gotten.
>
> CFS's task picking logic is based on this p->se.vruntime value and it is thus very simple: it always tries to run the task with the smallest p->se.vruntime value (i.e., the task which executed least so far).  CFS always tries to split up CPU time between runnable tasks as close to "ideal multitasking hardware" as possible.
>
> Most of the rest of CFS's design just falls out of this really simple concept, with a few add-on embellishments like nice levels, multiprocessing and various algorithm variants to recognize sleepers.

The concept of sleeper fairness is implemented to ensure that tasks that are not currently runnable (for example, waiting for I/O) receive a comparable share of the processor when they eventually need it.

## Red-black tree

The scheduler uses a time-ordered red-black tree to build a "timeline" of future task execution, and thus has no "array switch" method of distributing time-slices like the previously used runqueues.

![rbtree](assets/rbtree-figure1.gif)

With tasks (represented by sched_entity objects) stored in the time-ordered red-black tree, tasks with the gravest need for the processor (lowest virtual runtime) are stored toward the left side of the tree, and tasks with the least need of the processor (highest virtual runtimes) are stored toward the right side of the tree. The scheduler then, to be fair, picks the left-most node of the red-black tree to schedule next to maintain fairness. The task accounts for its time with the CPU by adding its execution time to the virtual runtime and is then inserted back into the tree if runnable. In this way, tasks on the left side of the tree are given time to execute, and the contents of the tree migrate from the right to the left to maintain fairness. Therefore, each runnable task chases the other to maintain a balance of execution across the set of runnable tasks.

## References

- [Wikipedia](https://en.wikipedia.org/wiki/Completely_Fair_Scheduler)
- [IBM's CFS tutorial](https://developer.ibm.com/tutorials/l-completely-fair-scheduler/)

----
_[1] Linux incorporates process and thread scheduling by treating them as one in the same. A process can be viewed as a single thread, but a process can contain multiple threads that share some number of resources (code and/or data)._
