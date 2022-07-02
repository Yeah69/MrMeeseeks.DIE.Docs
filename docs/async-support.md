# Key Points

- Use async initializers if you need to call asynchronous methods during instantiation
- Wrap asynchronous dependencies into a `ValueTask<T>` or a `Task<T>` if you don't want the container to await the async initialization before injection. Otherwise, …
- The async dependencies will get awaited and therefore the sync `CreateFunction` will be discarded from code generation
- DIE will never use blocking calls in order to make asynchronous instantiations synchronous
- DIE will never switch the synchronization contex in the generated code
  - Therefore, no use of `Task.Run(…)`
  - Therefore, no use of `await MethodAsync().ConfigureAwait(false)`
- DIE will prefer to generate code with use of `ValueTask`s over `Task`s

# Async Support

DIE supports asynchronous programming with three aspects:

1. Async initializer
1. Async `CreateFunction`
1. (Value)Task-wrapped dependencies

## Async Initializer

The initialization is called by DIE always right after the instance is created and before it is used anywhere else. If the initialization returns `ValueTask` or `Task`, then the initialization is asynchronous. From the perspective of DIE this also makes the instantiation asynchronous.

In the synchronous initialization that would imply that the instance can only be injected as dependency into another instance as soon as the initialization method is run to the end. With asynchronous initialization, however, it depend upon whether the asynchronously initialized dependency is injected directly or wrapped into a `ValueTask` or a `Task`. We'll have a look into both ways next. One after the other.

## Async `CreateFunction`

With a code base where all used implementations are purely synchronous instantiated, the use of a `CreateFunction`-attribute for a type `T` will lead to the generation of three functions: one returning an instance of `T` synchronously and two which would return the instance wrapped into a `ValueTask<T>` and a `Task<T>`, respectively. In the synchronous code base the latter two are just calling the synchronous function and wrapping the result. Therefore, all three functions are in terms of creating the instance functionally equivalent. But what would change if we would have an asynchronous instantiation in the code base?

If the code base has any async dependencies which isn't wrapped upon injection, then DIE will generate `await` instructions before the dependency gets injected. Consequently, the instantion of root instance can only be asynchronous, also because of following two design decisions:

- DIE will never use breaking calls like `.Result` or `.Wait()`
  - Otherwise deadlocks due to the generated code may be caused
- DIE will never switch the synchronization contexts by using `Task.Run(…)` or `.ConfigureAwait(false)`
  - That means if you need your instance to be generated on a certain thread - like the main thread for UI applications - you just have to call the `CreateFunction` from this thread

Both of this design decision also imply that there cannot be a synchronous `CreateFunction` if any non-wrapped async instantiation is needed. Therefore, if you still need a synchronous `CreateFunction` then you would need to wrap the dependencies. That we'll look into next.
 

## `(Value)Task`-wrapped Dependencies

Let's say you have an async initialized implementation called `AsyncDependency` and it is a dependency of `Instance`. Instead of injecting the instance of `AsyncDependency` into the instance of `Instance` the usual way by directly injecting into the constructor:

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

With DIE you have a second alternative option by injecting it wrapped into a `ValueTask<AsyncDependency>` or a `Task<AsyncDependency>`:

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

That way DIE wouldn't need to use an `away`-statement and therefore keep the synchronous `CreateFunction` still enabled. Of course this only applies if the `Instance`-instance doesn't need the `AsyncDependency`-instance in the constructor or the initialization, because that would mean unwrapping the `AsyncDependency`-instance and givin the `Instance`-instance itself an asynchronous initialization. Which means that wrapping dependencies works best for use cases where the dependencies isn't needed at instantiation (construction & initialization), but later on.

Another use case is whenever you want to start the construct the depending instances before the async initialization of the dependencies is done. Otherwise, without wrapping the dependency's initialization will be awaited before the construction of the instance which depends on it starts.

If the use cases aren't relevant to you and in case of asynchronous initializations you won't need a synchronous `CreateFunction`, you can just inject the dependencies directly like usual. Wrapping is just an alternative that is supported by DIE.

## Remarks On Generated Code

- Whenever suitable DIE will prefer a `ValueTask<T>` as the return type for async function
  - Exceptions are where an explicit `Task<T>` is expected
- When necessary DIE can and will from `ValueTask<T>` to `Task<T>` and vice versa
  - please note that when DIE is force to transform into `Task<T>` that it will mean more allocations on the heap which might have been prevented