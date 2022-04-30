# Configuration

The following characteristics have a high value for DIE:

1. Unambiguity
2. Convenience

This means DIE tries to be as comfortable as possible while always maintaining unambiguity.

## Minimal sample

Let's show that by going through the minimal sample:

```csharp
internal interface IInterface {}

internal class Class : IInterface {}

[CreateFunction(typeof(IInterface), "Create")]
internal partial class Container {}
```

The `CreateFunctionAttribute` will lead to the generation of following three functions: `public IInterface Create()`, `public Task<IInterface> CreateAsync()` & `public ValueTask<IInterface> CreateValueAsync()`. Each of which will return an instance of type `Class` either in synchronous or asynchronous fashion.

If you are familiar with other DI containers you'll probably miss some sort of registration of the type `Class`. So shouldn't DIE require such a registration as well? No, that is where convenience comes into play. By default, the container gathers all type information of the assembly of which it is contained in. The container is instructed to create a function which returns an instance of type `IInterface`. Because it is an interface it needs an implementation which implements it, so it seeks all implementations which implement this particular interface. There is only one implementation namely `Class` and it also has a single constructor (the implicit parameterless one). Hence, there is only one possible way to create an `IInterface` instance, so the container automatically knows what to do. If there would be further implementations known to the container, then it wouldn't compile. That way the unambiguity is maintained.

## Use cases of configuration

Unfortunately, even the best of code bases cannot be modeled in such a way that the container is able to automatically resolve everything. There maybe configurations needed in order to use advanced features like decorators & composites or in order to resolve ambiguities like multiple implementations of interfaces or multiple constructors of implementations. Therefore, DIE has a lot of configuration features, so you should be able to teach your intentions.