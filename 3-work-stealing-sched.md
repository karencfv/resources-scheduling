# Work stealing scheduling algorithm

## Work stealing vs work sharing

A scheduling algorithm for the efficient execution of a growing multithreaded computation on a MIMD (multiple instruction, multiple data) styled computer, must ensure the following:

- Enough threads are active concurrently to keep the processors busy.
- Ensure that the number of these concurrently active threads remains at a certain limit so that memory requirements are not excessive.
- Related threads should be maintained within the same processor if possible to mimimise communication between.

As opposed to work sharing, in which the scheduler attempts to migrate some of the processor's newly created threads to underutilised processors with the goal of distributing work; in work stealing, the underutilised processors take the initiative and try to "steal" threads from other processors.

With this approach, thread migration is much more infrequent as it will only occur if a processor is not busy. Whereas a work sharing scheduler is always migrating threads. This creates a significant amount of overhead.

The result is a randomised work-stealing scheduling algorithm for fully strict (well structured) multithreaded computations.

## "Fully strict" multithreaded computations

In order to understand the concept of "fully strict" multithreaded computation, we'll first look into multithreaded computations. These are composed by a set of threads, each of which is a grouping of unit-time instructions which are ordered in a sequential manner.

- **Dependency edges:** Decide which unit-time instructions should be executed first by connecting and ordering them.
- **Continue edges:** In Figure 1 each shaded block is a thread with circles representing unit-time instructions, and the horizontal edges which are the continue edges represent the sequential ordering.
- **Activation frame:** Chunk of memory allocated for a thread to be able to execute. The unit-time instructions of said thread can use up this memory space to store the values on which they compute.
- **Thread spawning:** When a thread is executing and it creates or "spawns" another thread. Spawned threads are children of the thread that spawned them, and each thread can spawn as many children as it desires, but can only be done one instruction at a time. As Figure1 shows, i3 spawns T3(i4) and i6 spawns T4(i7). It is important to note that no processor can execute an instruction in a child thread until the spawning instruction has been executed (e.g. i4 cannot execute until i3 has completed).
  
  Unlike a subroutine call, a spawning thread can operate concurrently with the spawned thread.

![Figure 1](./assets/work-steal-figure1.png)
**Figure 1:** A multithreaded computation.

- **Spawn tree:** As shown on Figure1, when a **root** thread spawns children, and these in turn spawn other threads a spawn tree is created; as shown by the downward pointing arrows (**spawn edges**) that connect threads to their spawned children.

- **Leaf threads:** These are spawned threads that have no children. In Figure1 these are represented by T3, T4, T5 and T6.

- **Living thread:** What a thread is called once it has been spawned and its frame allocated. When the last instruction on the thread has been executed, it deallocates its frame and the thread **dies**.

- **Join edges:** Represented on Figure1 by dotted arrows, join edges enforce ordering of instructions that consume data values produced by a separate instruction. A thread will stall if the instruction that produces the necessary data values hasn't been executed yet. Once said instruction executes, the join dependency is resolved and the consuming thread resumes it's execution (thread becomes ready).

    Multithreaded computations do **not** model how join dependencies get solved nor by which unresolved join dependencies get detected.

    Each instruction has at most a constant number of join edges occurring on it, this is consistent with the unit-time model of instructions.

    To prevent stalling after spawning. no join edges enter a parent thread immediately after it has spawned a child thread. A parent thread should be able to execute at least one more instruction.

- **DAG:** A directed acyclic graph is the order in which the instructions must be executed or execution schedule. This graph of instructions is defined by the constraints given by the spawn, continue and join edges of the computation. No processor can execute an instruction until after all the previous instructions in the graph have been executed. Because of this, the graph must be acyclic.

It is assumed that a parent thread is alive until all of its children die, in other words, a thread will not deallocate its activation frame until all its children's frames have been deallocated. It is also assumed that the frames hold all the values in the computation. It is also assumed that no global storage is available for the computations outside of the frames. In conclusion, we are assuming that the total size of all the frames used by all living threads is the total space used by the computation at any given moment. This total space used in executing th computation is the maximum value of space over the course of the computation.

A multithreaded computation is basically a **dag** of instructions connected by **dependency edges**. These instructions are connected by **continue edges** into threads, and these threads form a **spawn tree** with **spawn edges**. When a thread is spawned, an activation frame is allocated and this frame remains allocated as long as the thread remains alive. A living thread may be either ready or stalled due to an unresolved dependency.

A multithreaded program can have many multithreaded computations. Here we will analyse only multithreaded computations.

- **Strict multithreaded computation:** When all join edges from a thread go to an ancestor of the thread in an activation tree. This is to say that a thread cannot be invoked before all its arguments are available, although the arguments can be collected in parallel.

- **Fully strict multithreaded computation:** When all join edges from a thread go to the thread's parent. This is a "well-structured" computation, in which all join edges from a subtree of the spawn tree, emanate from the root of the subtree. Figure 1 is an example of a fully strict multithreaded computation

<!-- TODO: finish summary on this section -->

## Busy leaves property

<!-- TODO: finish summary on this section -->

## Randomised work-stealing algorithm

<!-- TODO: finish summary on this section -->

## Atomic access and recycling game

<!-- TODO: finish summary on this section -->

## Analysis of work-stealing algorithm and conclusion

<!-- TODO: finish summary on this section -->

## References

[Scheduling Multithreaded Computations by Work Stealing](http://supertech.csail.mit.edu/papers/steal.pdf)

[A Review on Multiple Instruction Multiple Data (MIMD) Architecture](https://www.researchgate.net/publication/303822973_A_Review_on_Multiple_Instruction_Multiple_Data_MIMD_Architecture)
