# Initializers

.Net's instantiation process is basically just the constructor call and object initialization (property injection). There are two problems with such a process:

- You can't work with dependencies injected to properties via the object initializer in the instantiation process, because the constructor is called before the object initializer.
- You are forced to do a synchronous instantiation because .Net constructors are synchronous only.

The initializer feature is intended to solve these issues. It lets you declare which function of which type should be called as the initialization method. There are some restrictions on the initialization methods:

- They can only return either `void`, `ValueTask` or `Task`.
- They must be non-static
- There should be only one declared initializer method per implementation.

DIE guarantees to call the initializer method exactly once.

For the sync initializers, DIE guarantees that the initializer method will be executed to completion before the associated instance is injected into other instances.

The behavior of the async initializer is a bit more complex. If the associated instance is injected via wrapping into a `ValueTask<...>` or a `Task<...>`, then it is injected as soon as the initialization task does the async return (await), and the task wrapper is guaranteed to complete asynchronously exactly when the initializer's task completes. If the associated instance is injected bare (without a task wrapper), then the initializer's task is guaranteed to await and therefore complete before the associated instance is injected further.

The initializers can be declared either for abstractions or for implementations.

## Explicit Implementation Trick

This is only a recommendation, as it is probably a matter of taste.

If you are using an interface for the initializers, then make it an explicit implementation in the implementation types. The advantage of this approach is that in this case you can't reach the initializer method without explicitly casting to the interface type. Since the initializer method should only be called once, and only by the container, you don't need to call it regularly anyway.

```csharp
internal class Dependency : ITypeInitializer
{
    void ITypeInitializer.Initialize()
    {
        // initialization logic
    }
}
```