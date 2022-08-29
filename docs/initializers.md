# Initializers

The instantiation process of .Net is basically just the constructor call and the object initialization (property injection). There are two issues with such a procedure:

- You can't work in the instantiation process with dependencies injected to properties via the object initializer, because the constructor is called before object initializer.
- You are forced to have a sync instantiation, because .Net constructors are sync-only.

The initializer feature is intended to solve these issues. The feature let's you declare which function of which type should be called as the initialization method. There are some constraints to the initialization methods:

- They can only return either `void`, `ValueTask`, or `Task`.
- They have to be parameterless and non-static
- There should be only one declared initialization method per implementation

DIE guarantees to call the initializer method exactly once.

With the sync initializers DIE guarantees that the initializer method is run until completion before the asociated instance is further injected into other instances.

The async initializer's behavior is a bit more complex. If the asociate instance is injected via wrapping into a `ValueTask<…>` or a `Task<…>`, then it gets injected injected as soon as the initialization task does the async return (await) and the task wrapper is guaranteed to complete asynchronously exactly when the initializer's task completes. If the asociated instance is injected bare (without task wrapper) then the initializer's task is guaranteed to be awaited and therefore run to completion before the asociated instance is further injected.

The initalizers can be declared either for abstractions or for implementations. 

## Explicit Implementation Trick

This is just a recommendation, because it probably is a matter of taste.

If you use an interface a for the initializers, then make it an explicit implemetation in the implementation types. The benefit of this approach is, that in this case you can't reach the initializer method without explicitly casting to the interace type. Because the initializer method should be called only once and only by the container, you don't need to access the initializer method regularly anyway.

```csharp
internal class Dependency : ITypeInitializer
{
    void ITypeInitializer.Initialize()
    {
        // initialization logic
    }
}
```