# Injections

DIE supports several types of injections:

- Instance injection
- Collection injection
- Factory injection
- Scope injection

This page explains how to work with these injections.

## Instance Injection

Instance injections are ordinary dependency injections. That is, you declare what type you need, and you'll get a single instance of that type. Instance injections can be divided into two different parts. One is injection of implementation types, and the other is injection of abstraction types. Understanding DIE's definition of an implementation type and an abstraction type is necessary to understand when to use which injection behavior. Definition:

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

Example:

```csharp
internal record Dependency;

internal class DependencyHolder
{
    internal readonly Dependency _dependency;
    internal DependencyHolder(Dependency dependency) => // is an instance of type Dependency
        _dependency = dependency;
}
```

By default, whenever an implementation type needs to be injected, DIE will inject an instance of exactly that type. This is true even if the implementation type is a parent class that has inheriting child classes.

However, there is a way to modify this behavior by configuring implementation choices. Alternatively, user-defined factories can be used to change this behavior as well.

### Abstraction

Example:

```csharp
internal interface IDependency {}

internal record Dependency : IDependency;

internal class DependencyHolder
{
    internal readonly IDependency _dependency;
    internal DependencyHolder(IDependency dependency) => // is an instance of type Dependency
        _dependency = dependency;
}
```

Whenever an abstraction type (interface or abstract class) needs to be injected, DIE will by default use the only known implementation type that implements the abstraction in its place. 

If multiple implementations are known, then using configuration features such as implementation choice or a user-defined factory is mandatory to uniquely select an implementation.


### Nullability

Example:

```csharp
internal interface IDependency {}

internal class DependencyHolder
{
    internal readonly IDependency? _dependency;
    internal DependencyHolder(IDependency? dependency) => // is null
        _dependency = dependency;
}
```

If the dependency type is a nullable abstraction type and DIE can't choose a unique implementation (no implementations; multiple implementations; no implementation choice), then DIE will inject the null value instead.

### Generics

Example:

```csharp
internal record Dependency<T>;

internal class DependencyHolder
{
    internal readonly Dependency<int> _dependency;
    internal DependencyHolder(Dependency<int> dependency) => // is an instance of type Dependency<int>
        _dependency = dependency;
}
```

Instance injection supports generic types (see [the generics support page](generics-support.md)). 

## Collection Injection

Example:

```csharp
internal interface IDependency {}

internal record DependencyA : IDependency;

internal record DependencyB : IDependency;

internal class DependencyHolder
{
    internal readonly IReadOnlyList<IDependency> _dependencies;
    internal DependencyHolder(IReadOnlyList<IDependency> dependencies) => // is a collection of a DependencyA- and a DependencyB-instance
        _dependencies = dependencies;
}
```

Collection injections aren't limited to injecting a single implementation. Therefore, the injected instance will be a collection type that contains instances for each different implementation of its member type as items.

The currently supported collection types are 

- IEnumerable<…>
- IReadOnlyList<…>
- IReadOnlyCollection<…>
- Arrays

If you want to restrict the implementations of collection injection that are used, you can use an implementation collection choice as a configuration option.

You can also combine the collection type with `ValueTask<…>` or `Task<…>` (e.g. `IReadOnlyList<ValueTask<IDependency>>`), if you might need to wrap asynchronous resolutions of one or multiple implementations.

## Factory Injection

Instead of injecting the dependency directly with factory injections, you have the option to inject generated factories that will create the dependencies on demand. 

DIE supports `Func<…>` and `Lazy<…>` as factory wrapper types. The created dependency type (the last generic parameter for `Func<…>` and the only generic parameter for `Lazy<…>`) can be any other type of injection (i.e. instance, collection, or scope injection).

Once the scope in which the factory was instantiated is disposed of, no further use of the factory is allowed. If the factory is still called from a disposed scope, it will throw an exception.

### Func

Example:

```csharp
internal record Dependency(int Number, string Text);

internal class DependencyHolder
{
    internal readonly Func<int, string, Dependency> _dependencyFactory;
    internal DependencyHolder(Func<int, string, Dependency> dependencyFactory) => // is a factory that creates an instance of type Dependency
        _dependencyFactory = dependencyFactory;
}
```

The parameters of a `Func<…>` factory are also supported. The parameters are used as overrides for the remaining resolution, if not overridden again later. The override semantic is inspired by another great DI container: Shout out to [DryIoc](https://github.com/dadhi/DryIoc)!

### Lazy

Example:

```csharp
internal record Dependency(int Number, string Text);

internal class DependencyHolder
{
    internal readonly Lazy<Dependency> _dependency;
    internal DependencyHolder(Lazy<Dependency> dependency) => // is a lazy object that creates an instance of type Dependency upon first usage
        _dependency = dependency;
}
```

While technically not being a functor ("first-citizen function") itself, `Lazy<…>`s get a functor injected which they then use. In DIE, `Lazy<…>`s are interpreted as parameterless factories that can only create a single instance, but where the creation can be delayed until the first use.

## Scope Injection

Example:

```csharp
internal record Dependency;

internal record ScopeRoot(Dependency Dependency) : IScopeRoot;

internal class DependencyHolder
{
    internal readonly ScopeRoot _scopeRoot;
    internal DependencyHolder(ScopeRoot scopeRoot) => // Starts a new scope in which the ScopeRoot- and the Dependency-instance is created
        _scopeRoot = scopeRoot;
}
```

Scope injection is very similar to instance injection from a usage perspective. The difference is that scope injection starts a new (transient) scope and creates the injected instance (called the scope root) from it. For a more complete explanation, see the [scoping page](scoping.md).

## (Value)Tuple

Example:

```csharp
internal record DependencyA;

internal record DependencyB;

internal class DependencyHolder
{
    internal readonly (DependencyA, DependencyB) _tuple;
    internal DependencyHolder((DependencyA, DependencyB) tuple) => // is a tuple of a DependencyA- and a DependencyB-instance
        _tuple = tuple;
}
```

Tuple injection can be seen as a special case, because it can be understood as a combination of injections. For each type of the tuple, a separate resolution is started, very similar to what would have happened in an ordinary dependency injection. The result of these resolutions is assembled into the tuple. DIE supports `Tuple<…>` and both the syntax and non-syntax `ValueTuple<…>`.