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

[AllImplementationsAggregation]
[CreateFunction(typeof(IInterface), "Create")]
internal partial class Container {}
```

The `CreateFunctionAttribute` will lead to the generation of following three functions:

- `public IInterface Create()`
- `public Task<IInterface> CreateAsync()`
- `public ValueTask<IInterface> CreateValueAsync()`

Each of which will return an instance of type `Class` either in synchronous or asynchronous fashion.

The `AllImplementationsAggregation` is the way to tell the container to consider all implementations, which are accessible, of the whole code base. This includes the referenced assemblies as well (which also includes the .Net assemblies). For this sample it would be sufficient to just configure the sole implementation explicitly. However, using `AllImplementationsAggregation` is a more versitile approach, as you could now just add more implementations without needing to alter the configuration. Thus, this approach seems to be more fitting for a minimalistic sample. Also it shows the convenience-based approach that DIE takes.

Another aspect which is convenient in this sample is that you don't need to register to the implementation `Class` to be used for the interface `IInterface`. That's because for `IInterface` there is only one known implementation `Class` (and it has only one constructor - the implicit default one). From the perspective of the container there is only one possible way to resolve `IInterface` and therefore, the container can decide on it's own without the need of you telling it explicitly.

However, if there would be further implementations for the interface known to the container, then it wouldn't compile. In this case you would need to extend the configuration by telling the container which of the implementations should be used for the interface. That way the unambiguity is maintained.

## Use cases of configuration

Unfortunately, even the finest of code bases cannot be modeled in such a way that the container is able to automatically resolve everything as soon as they reach a certain amount content or complexity. There maybe configurations needed in order to use advanced features like decorators & composites or in order to resolve ambiguities like multiple implementations of interfaces or multiple constructors of implementations. Therefore, DIE has configuration features, so you should be able to teach your intentions to the container.

## Aggregating, Choosing, and Filtering

Most of DIE's configuration feature fall into one of the categories: Aggregation, Choice, Filter.

### Aggregation

Aggregation is about gathering knowledge. The container will make decision based on the aggregated knowledge only. If a container is configured so that it only knows of one implementation of an interface which actually has multiple implementations, then the container would still act as if this interface has only one implemetation.

### Choice

Choices are a kind of configuration that let's you resolve ambiguities. For example, if an implementation has multiple constructors. Choices can also be a more comfortable alternative to filtering. Another example, if an interface has multiple implementations you can aggregate all implementations and choose one of them for the interface. That way it is possible to inject dependencies of the interface type and collection of the interface type (`IReadOnlyList<IInterface>`) in the same container(/scope).

### Filter

For every aggregation and choice configuration there is a filter configuration which does the exact opposite (filters aggregated elements or removes choices).

It's important to note that filters are only applied at "the beginning" of the configured scope. Thus, you can only filter aggregations and choices set in the parent scope from configuration's perspective.

## More Detailed Articles

[Implementations](configuration/implementations.md)

[Disposal](configuration/disposal.md)

[Decorator/Composite](configuration/decorator-composite.md)

## Scopes

In context of the configuration three levels of scopes do exist:

1. Assembly
2. Container
3. (Transient)Scope

In the currently configured level the configurations of the previous level get applied first, then the filter configuration and then the aggregations and choices.

If you don't need to write a DI-container implementation yourself and therefore don't need to have a lot of containers in the test project, then the benefit of having an assembly level configuration might not be obvious. Usually, one single container would be sufficient for one code base and I would actually also recommend to only have one container. If not for sharing configurations between multiple containers, why would you need an assembly level configuration then? A simple examplary use case would be: you would like to aggregate all implementations at container level but with a few exceptions which should be left out. An easy way to accomplish that would be to aggregate all implementations at assembly level and filter(/opt-out) the exceptions at container level.

Another important aspect to note is that configurations of TransientScopes and Scopes alike will inherit the configuration of the container that they're in. That means configuration-wise TransientScopes and Scopes are peers.