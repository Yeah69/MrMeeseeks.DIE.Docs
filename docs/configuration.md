# Configuration

The following features have a high value for DIE:

1. Unambiguousness
2. Convenience

This means that DIE tries to be as convenient as possible while still maintaining unambiguousness.

## Minimal Example

Let's show this by going through the minimal example:

```csharp
internal interface IInterface {}

internal class Class : IInterface {}

[AllImplementationsAggregation]
[CreateFunction(typeof(IInterface), "Create")]
internal partial class Container {}
```

The `CreateFunctionAttribute` will result in the creation of the following three functions:

- `public IInterface Create()`
- `public Task<IInterface> CreateAsync()`
- `public ValueTask<IInterface> CreateValueAsync()`

Each of these will return an instance of type `Class` either synchronously or asynchronously.

The `AllImplementationsAggregation` is the way to tell the container to consider all accessible implementations of the entire code base. This includes the referenced assemblies (which includes the .Net assemblies). For this example, it would be sufficient to just explicitly configure the single implementation. However, using `AllImplementationsAggregation` is a more versatile approach, as you can now simply add more implementations without having to change the configuration. Thus, this approach seems more appropriate for a minimalist example. It also shows the convenience-based approach that DIE takes.

Another aspect that is convenient in this example is that you don't need to register the implementation `Class` to be used for the interface `IInterface`. - public ValueTask<IInterface> CreateValueAsync()`.

Each of these will return an instance of type `Class` either synchronously or asynchronously.

The `AllImplementationsAggregation` is the way to tell the container to consider all accessible implementations of the entire code base. This includes the referenced assemblies (which includes the .Net assemblies). For this example, it would be sufficient to just explicitly configure the single implementation. However, using `AllImplementationsAggregation` is a more versatile approach, as you can now simply add more implementations without having to change the configuration. Thus, this approach seems more appropriate for a minimalist example. It also shows the convenience-based approach that DIE takes.

Another aspect that is convenient in this example is that you don't need to register the implementation `Class' to be used for the interface `IInterface'. That's because there is only one known implementation of `Class` for `IInterface` (and it has only one constructor - the implicit default one). From the container's point of view, there is only one possible way to resolve `Interface`, and therefore the container can decide on it's own without you telling it explicitly.

However, if there were other implementations for the interface that the container knew about, it wouldn't compile. In this case, you would need to extend the configuration by telling the container which of the implementations for the interface to use. This way, unambiguousness is maintained.

## Use cases of configuration

Unfortunately, even the best codebases cannot be modeled in such a way that the container can automatically resolve everything once they reach a certain level of content or complexity. There may be configuration required to take advantage of advanced features like decorators & composites, or to resolve ambiguities like multiple implementations of interfaces or multiple constructors of implementations. That is why DIE has configuration capabilities, so you should be able to teach the container your intentions.

## Property-focused Configurations

Typically, DI containers take an implementation-oriented approach to configurations. That is, they start with the implementation and then add properties (e.g., scoping, transitivity, and so on). DIE chooses a different path.

DIE reverses the traditional configuration principle by focusing on properties. The configuration attributes represent properties and collect the implementations that should apply them. For example, the `ContainerInstanceImplementationAggregation` attribute collects implementations that should be instantiated only once for the entire container and shared everywhere in use.

## Abstraction Configurations

You can also register abstractions (e.g. interfaces). In this case, however, the properties are not applied to the abstractions themselves, but to all registered implementations that implement or inherit from the abstraction. This way you can use marker interfaces and don't have to register each implementation to a property individually. For example, the `ContainerInstanceImplementationAggregation` attribute has an alternative `ContainerInstanceAbstractionAggregation` attribute.

Marker interfaces can only be used by your own code. Therefore, even if you decide to use marker interfaces, using "implementation" attributes to assign properties to external implementations (e.g., .Net types) may still be the only feasible way.

### Recommended Marker Interfaces And Their Configurations

The marker interfaces:

```csharp
public interface IContainerInstance { }
public interface ITransientScopeInstance { }
public interface IScopeInstance { }
public interface ITransientScopeRoot { }
public interface IScopeRoot { }
public interface ITransient { }
public interface ISyncTransient { }
public interface IAsyncTransient { }
public interface IDecorator<T> { }
public interface IComposite<T> { }
public interface ITypeInitializer
{
    void Initialize();
}
public interface ITaskTypeInitializer
{
    Task InitializeAsync();
}
public interface IValueTaskTypeInitializer
{
    ValueTask InitializeAsync();
}
``` 

The configurations of the marker interfaces:

```csharp
[assembly:ContainerInstanceAbstractionAggregation(typeof(IContainerInstance))]
[assembly:TransientScopeInstanceAbstractionAggregation(typeof(ITransientScopeInstance))]
[assembly:ScopeInstanceAbstractionAggregation(typeof(IScopeInstance))]
[assembly:TransientScopeRootAbstractionAggregation(typeof(ITransientScopeRoot))]
[assembly:ScopeRootAbstractionAggregation(typeof(IScopeRoot))]
[assembly:TransientAbstractionAggregation(typeof(ITransient))]
[assembly:SyncTransientAbstractionAggregation(typeof(ISyncTransient))]
[assembly:AsyncTransientAbstractionAggregation(typeof(IAsyncTransient))]
[assembly:DecoratorAbstractionAggregation(typeof(IDecorator<>))]
[assembly:CompositeAbstractionAggregation(typeof(IComposite<>))]
[assembly:TypeInitializer(typeof(ITypeInitializer), nameof(ITypeInitializer.Initialize))]
[assembly:TypeInitializer(typeof(ITaskTypeInitializer), nameof(ITaskTypeInitializer.InitializeAsync))]
[assembly:TypeInitializer(typeof(IValueTaskTypeInitializer), nameof(IValueTaskTypeInitializer.InitializeAsync))]
```

The location of the marker interfaces can be in a separate assembly and therefore doesn't need to know anything about DIE. Only the location of the configurations needs to reference the namespace of the marker interface and DIE.

By using marker interfaces, container configuration becomes much simpler, and focuses more on specialized settings. However, with DIE, it's up to you whether you want to use a "marker interface" configuration style.

## Aggregating, Choosing, and Filtering

Most of DIE's configuration features fall into one of the following categories: Aggregation, Selection, Filter.

### Aggregation

Aggregation is about collecting knowledge. The container will make decisions based only on the aggregated knowledge. If a container is configured to know of only one implementation of an interface that actually has multiple implementations, the container would still behave as if that interface had only one implementation.

### Choice

Choices are a kind of configuration that let's you resolve ambiguities. For example, when an implementation has multiple constructors. Choices can also be a more convenient alternative to filtering. Another example: if an interface has multiple implementations, you can aggregate all the implementations and choose one for the interface. This way it is possible to inject dependencies of the interface type and the interface type collection (`IReadOnlyList<IInterface>`) into the same container (or scope).

### Filter

For each aggregation and choice configuration, there is a filter configuration that does the exact opposite (filters aggregated items or removes choices).

It's important to note that filters are applied only at the "the beginning" of the configured scope. That is, you can only filter aggregations and choices set in the parent configuration level.

## Descriptions Of The Attributes

[Implementations](configuration/implementations.md)

[Disposal](configuration/disposal.md)

[Decorator/Composite](configuration/decorator-composite.md)

[Generics](configuration/generics.md)

[Miscellaneous](configuration/miscellaneous.md)

## Configuration Levels

There are three levels of configuration:

1. Assembly
2. Container
3. (Transient) Scope

In the currently configured level, the configurations of the previous level are applied first, then the filter configurations, and then the aggregations and choices.

If you don't need to write a DI container implementation yourself, and therefore don't need to have many containers in the test project, then the benefit of having an assembly-level configuration may not be obvious. Typically, a single container would be sufficient for a code base, and I would actually recommend having only one container. If not for sharing configurations between multiple containers, then why would you need an assembly level configuration? A simple example use case would be: you want to aggregate all implementations at the container level, but with a few exceptions that should be omitted. An easy way to accomplish this would be to aggregate all implementations at the assembly level and filter the exceptions at the container level.

Another important aspect to note is that configurations of both transient and normal scopes inherit the configuration of the container they're in. This means that configuration-wise, transient scopes and normal scopes are peers.