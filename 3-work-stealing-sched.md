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

- **Join edges:** <!-- dotted arrows -->

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
