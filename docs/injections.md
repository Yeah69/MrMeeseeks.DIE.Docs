# Injections

DIE supports different kind of injections:

- Instance injection
- Collection injection
- Factory injection
- Scope injection

On this page working with these injections will be explained.

## Instance Injection

Instance injections are ordinary dependency injections. Meaning you declare which type you need and you'll get a singular instance of that type. Instance injections can be subdivided into two distinct parts. One being injecting implementation types and the other being injecting abstraction types. Understanding DIE's definition of an implementation type and an abstraction type is necessary in order to understand when which injection behavior will be applied. Definition:

- Implementation types
    - Non-abstract class types
        - Including non-abstract record types
    - Struct types
        - Including struct record types
- Abstraction types
    - Interface types
    - Abstract class types
        - Including abstract record types

### Implementations

Whenever an implementation type has to be injected, DIE will per default inject an instance of exactly that type. Even if the implementation type is a parent class and has inhereting child classes.

However, there is an option to modify this behavior by configuring implementation choices. Alternatively, user-defined factories can be used to alter this behavior as well.

Example:

```csharp
internal record Dependency;

internal class Parent
{
    internal readonly Dependency _dependency;
    internal Parent(Dependency dependency) => // is an instance of type Dependency
        _dependency = dependency;
}
```

### Abstraction

Whenever an abstraction type has to be injected, DIE will per default will use the single known implementation type which implements the abstration in its place. 

If multiple implementation are known, then usage of configuration features like the implementation choice or a user-defined factory are mandetory for selting an implementation unabigously. 

Example:

```csharp
internal interface IDependency {}

internal record Dependency : IDependency;

internal class Parent
{
    internal readonly IDependency _dependency;
    internal Parent(IDependency dependency) => // is an instance of type Dependency
        _dependency = dependency;
}
```

### Nullability

If the dependency type is a nullable abstraction type and DIE can't chose a singular implementation (no implementations; multiple implementations; no implementation choice), then DIE will inject the null value instead.

Example:

```csharp
internal interface IDependency {}

internal class Parent
{
    internal readonly IDependency? _dependency;
    internal Parent(IDependency? dependency) => // is null
        _dependency = dependency;
}
```

### Generics

Instance injections support generic types.

Example:

```csharp
internal record Dependency<T>;

internal class Parent
{
    internal readonly Dependency<int> _dependency;
    internal Parent(Dependency<int> dependency) => // is an instance of type Dependency<int>
        _dependency = dependency;
}
```

## Collection Injection

Collection Injections aren't constraint inject a singular implementation. Therefore, the injected instance will be a collection type which contains instances for each distinct implementation of its item type as items.

The currently supported collection types are: 

- IEnumerable<…>
- IReadOnlyList<…>
- IReadOnlyCollection<…>

If you would like to narrow the used implementations of the collection injection, then you can use an implementation collection choice as a configuration option.

You can also combine the collection type with `ValueTask<…>` or `Task<…>` (e.g. `IReadOnlyList<ValueTask<IDependency>>`), if you might need to wrap asynchronous resolutions of one or multiple implementations.

Example:

```csharp
internal interface IDependency {}

internal record DependencyA : IDependency;

internal record DependencyB : IDependency;

internal class Parent
{
    internal readonly IReadOnlyList<IDependency> _dependencies;
    internal Parent(IReadOnlyList<IDependency> dependencies) => // is a collection of a DependencyA- and a DependencyB-instance
        _dependencies = dependencies;
}
```

## Factory Injection

Instead of injecting the dependency directly with factory injections have the option to inject generated factories which will create the dependencies on demand. 

DIE support `Func<…>` and `Lazy<…>` as factory wrapper types. The created dependency type (the last generic parameter for `Func<…>`s and the only generic parameter for `Lazy<…>`) may be any other type of injection (i.e. instance, collection, or scope injection).

As soon as the scope under which the factory was instantiated is disposed, no further usage of the factory is allowed. If still invoked from a disposed scope, then the factory will throw an exception.

### Func

The parameters of a `Func<…>` factory are supported as well. The parameters are used as overrides for the remaining resolution, if not overriden again at a later point. The overriding semantic is an inspiration from another great DI-container: Shout out to DryIoc!

Example:

```csharp
internal record Dependency(int Number, string Text);

internal class Parent
{
    internal readonly Func<int, string, Dependency> _dependencyFactory;
    internal Parent(Func<int, string, Dependency> dependencyFactory) => // is a factory that creates an instance of type Dependency
        _dependencyFactory = dependencyFactory;
}
```

### Lazy

While technically being no functor ("first-citizen function") itself, `Lazy<…>`s get a functor injected which it then uses. In DIE `Lazy<…>`s are interpreted as parameterless factories which can only create one single instance but where the creation can be delayed until the first usage.

Example:

```csharp
internal record Dependency(int Number, string Text);

internal class Parent
{
    internal readonly Lazy<Dependency> _dependency;
    internal Parent(Lazy<Dependency> dependency) => // is a lazy object that creates an instance of type Dependency upon first usage
        _dependency = dependency;
}
```

## Scope Injection

Scope injection are much like instance injection if looking at the usage side. The difference is that the scope injection will start a new (transient) scope and create the injected instance (which is called scope root) from that there. For a more comprehensive explanation see the [scoping docu page](scoping.md).

Example:

```csharp
internal record Dependency;

internal record ScopeRoot(Dependency Dependency) : IScopeRoot;

internal class Parent
{
    internal readonly ScopeRoot _scopeRoot;
    internal Parent(ScopeRoot scopeRoot) => // Starts a new scope in which the ScopeRoot- and the Dependency-instance is created
        _scopeRoot = scopeRoot;
}
```

## ValueTuple

ValueTuple injection can be seen as special case, because it can be understood as a combinations of injections. For each type of the tuple an own resolution will be started very similar to how it would have happen to an ordinary dependency injection. The result of these resolutions will be composed into the tuple. DIE supports both, the syntax and the non-syntax ValueTuple.

Example:

```csharp
internal record DependencyA;

internal record DependencyB;

internal class Parent
{
    internal readonly (DependencyA, DependencyB) _tuple;
    internal Parent((DependencyA, DependencyB) tuple) => // is a tuple of a DependencyA- and a DependencyB-instance
        _tuple = tuple;
}
```