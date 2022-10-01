# Async Support

DIE supports asynchronous programming in three ways:

1. Async initializer
1. Async `CreateFunction`
1. (Value)Task-wrapped dependencies
1. Async disposal

## Async Initializer

The initialization is always called by DIE right after the instance is created and before it is used anywhere else. If the initialization returns a `ValueTask` or a `Task` then the initialization is asynchronous. From DIE's point of view this also makes the instantiation asynchronous.

With synchronous initialization, this would mean that the instance could not be injected as a dependency into another instance until the initialization method was executed to completion. With asynchronous initialization, however, it depends on whether the asynchronously initialized dependency is injected directly or wrapped into a `ValueTask` or a `Task`. We'll look at both ways next. One at a time.

## Async `CreateFunction`

In a codebase where all the implementations used are purely synchronous instantiations, using a `CreateFunction` attribute on a type `T` will result in the creation of three functions: one that returns an instance of `T` synchronously, and two that would return the instance wrapped in a `ValueTask<T>` and a `Task<T>` respectively. In the synchronous codebase, the latter two are just calling the synchronous function and wrapping the result. Therefore, all three functions are functionally equivalent in terms of creating the instance. But what would change if we had asynchronous instantiation in the codebase?

If the code base has any async dependencies that aren't wrapped at injection, then DIE will generate `await` statements before the dependency is injected. Consequently, the instantiation of the root instance can only be asynchronous, also because of the following two design decisions:

- DIE will never use breaking calls like `.Result` or `.Wait()`.
    - Otherwise deadlocks could be caused by the generated code.
- DIE will never switch synchronization contexts using `Task.Run(…)` or `.ConfigureAwait(false)`.
    - This means that if you need your instance to be created on a specific thread - like the main thread for UI applications - you just need to call the CreateFunction from that thread.

Both of these design choices also imply that there can be no synchronous `CreateFunction` if any non-wrapped async instantiation is required. So if you still need a synchronous CreateFunction, you would need to wrap the dependencies. We'll look at this next.
 

## `(Value)Task`-wrapped Dependencies

Let's say you have an async initialized implementation called `AsyncDependency`, and it is a dependency of `Instance`. Instead of injecting the instance of `AsyncDependency` into the instance of `Instance` the usual way, by injecting it directly into the constructor:

```csharp

internal class Instance
{
    internal Instance(
        AsyncDependency asyncDependency)
    {
        // …
    }
}

```

With DIE, you have a second alternative option by injecting it wrapped in a `ValueTask<AsyncDependency>` or a `Task<AsyncDependency>`:

```csharp

internal class Instance
{
    internal Instance(
        ValueTask<AsyncDependency> asyncDependency)
    {
        // …
    }
}

```

This way, DIE wouldn't need to use an `away` statement and thus still keep the synchronous `CreateFunction` enabled. Of course, this is only true if the `Instance` instance doesn't need the `AsyncDependency` instance in its constructor or initialization, because that would mean unwrapping the `AsyncDependency` instance and giving the `Instance` instance asynchronous initialization itself. This means that wrapping dependencies works best for use cases where the dependencies aren't needed at instantiation, but later.

Another use case is whenever you want to start constructing the dependent instances before the async initialization of the dependencies is done. Otherwise, without wrapping, the initialization of the dependency is waited for before the construction of the dependent instance starts.

If the use cases aren't relevant to you, and you don't need a synchronous `CreateFunction' in the case of asynchronous initializations, you can just inject the dependencies directly as usual. Wrapping is just an alternative supported by DIE.

## Async Disposal

DIE will always let the container implement `IAsyncDisposable`. So if you want to make sure the container is always properly disposed of, use the `IAsyncDisposable` interface for disposal. If the container needs to track a dependency that implements `IAsyncDisposable`, then the container's `IDisposable` will be discarded, because at that point it's no longer a valid option (without blocking calls).

## Remarks On Generated Code

- Whenever appropriate, DIE will prefer a `ValueTask<T>` as the return type for an async function.
    - Exceptions are where an explicit `Task<T>` is expected.
- If necessary, DIE can and will switch from `ValueTask<T>` to `Task<T>` and vice versa.
    - Note that forcing DIE to convert to `Task<T>` will cause more allocations on the heap which may have been avoided.

## Summary

- Use async initializers if you need to call asynchronous methods during instantiation.
- Wrap asynchronous dependencies in a `ValueTask<T>' or a `Task<T>' if you don't want the container to await async initialization before injection. Otherwise…
- The async dependencies will be awaited and therefore the sync `CreateFunction` will be discarded during code generation.
- DIE will never use blocking calls to make asynchronous instances synchronous
- DIE will never change the synchronization context in the generated code.
    - Therefore, no use of `Task.Run(...)`.
    - Therefore, no use of `await MethodAsync().ConfigureAwait(false)`.
- DIE will prefer to generate code using `ValueTask` over `Task`.