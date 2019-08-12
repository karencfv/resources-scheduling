# Go's runtime scheduler

## A bit of history

Before the current runtime work-stealing scheduler was implemented, Go's [goroutine scheduler](https://github.com/golang/go/blob/release-branch.go1/src/pkg/runtime/proc.c) was simpler. "Ready-to-run" goroutines were matched with "waiting-for-work" machines. When there were ready goroutines and no waiting machines, a new machine was started running in a new OS thread. All ready goroutines could run simultaneously, up to a limit.

Unfortunately this meant it had a limited scalability of concurrent programs; particularly high throughput servers and parallel computational programs.

## Current implementation

The biggest change was to introduce processors into the runtime and implement a [work-stealing scheduler](work-stealing-sched.md) on top of them.

So now we have three main actors for Go's scheduler:

- **M**: Machine or worker thread
- **G**: Goroutine
- **P**: Processor, a resource that is required to execute Go code.

    _M must have an associated P to execute Go code, however it can be blocked or in a syscall w/o an associated P._

![actors](assets/go-sched-figure1.png)

<!--TODO: finish writing up how the work stealing scheduler is implemented in Go's runtime scheduler -->

## References

- [Design document](https://docs.google.com/document/d/1TTj4T2JO42uD5ID9e89oa0sLKhJYD0Y_kqxDv3I3XMw/edit)
- [Previous scheduler](https://github.com/golang/go/blob/release-branch.go1/src/pkg/runtime/proc.c)
- [Goroutine scheduler](https://github.com/golang/go/blob/release-branch.go1.12/src/runtime/proc.go)
- [JBD's blog](https://rakyll.org/scheduler/)
- [Morsing's blog](https://morsmachine.dk/go-scheduler)
