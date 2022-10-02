# Generics Support

DIE supports the use of generic types. This includes both configuration and usage.

## Configuration

Generic implementations can be configured to do the same things as non-generic implementations. However, generic implementations must be passed as unbound types. This means that instead of passing a concrete generic type parameter, it is left empty. Configurations for this unbound generic type then apply to all of its bound generic type variants. For example:

```csharp
[ContainerInstanceImplementationAggregation(
    typeof(DependencyA<>),
    typeof(DependencyB<,>))]
```

This will configure all variants of the generic implementation `DependencyA<…>` (e.g. `DependencyA<int>` or `DependencyA<string>`) to be a container instance. The same logic applies to `DependencyB<…,…>`, which has two generic parameters.

## Usage

Fortunately, C# doesn't allow the use of unbound generic types except in the `typeof` operator. This makes the use of implementation instance injections straightforward:

```csharp
internal class Dependency<T0> { }

internal class DependencyHolder
{
    internal DependencyHolder(Dependency<int> dependency) { }
}
```

Generic interface instance injections become more interesting. Straightforward usage is only possible for cases where each of the implementation types' generic parameters can be determined based on the interface type. For example:

```csharp
public interface IInterface<T0, T1> { }

internal class Dependency<T0, T1> : IInterface<T1, T0> { }

internal class DependencyHolder
{
    internal DependencyHolder(IInterface<int, string> dependency) { } // A Dependency<string, int> instance
}
```

Cases with unspecified generic type parameters are supported by DIE. However, more specialized configuration would be required, as described in the next section …

## Unspecified Generic Type Parameters With Interface Injections

There are situations where DIE can't automatically determine a generic type parameter for an implementation when injecting through an interface. Here is a minimal example:

```csharp
public interface IInterface<T> { }

internal class Dependency<T0, T1> : IInterface<T1> { }

internal class DependencyHolder
{
    internal DependencyHolder(IInterface<int> dependency) { } // What should be put into T0?
}
```

So, in this example, if DIE needs to resolve `DependencyHolder` and `Dependency<T0, T1>` is the only implementation of `Interface<T>`, then it is clear that `T1` will be assigned `int`. However, in this situation `T0` can't be resolved. 

To resolve such situations, DIE provides a configuration feature. You can make a generic type parameter choice for instance injections (singular) or a generic type parameter collection choice for collection injections. Both are configured by implementation type. You can also mix these into types of choices. If you make only a singular choice, then it's also selected as a singular choice for collection injections. If you configure a single type in the collection choice, and don't configure a single choice, then it will be selected for instance injections. 

To resolve the example, the following configuration example can be applied:

```csharp
[GenericParameterChoice(typeof(Dependency<,>), "T0", typeof(string))]
```

If you want to add type substitution for collection injection, you can use the following configuration:

```csharp
[GenericParameterSubstitutesChoice(typeof(Dependency<,>), "T0", typeof(int), typeof(byte))]
```

With these two configurations in place, an `IReadOnlyList<IInterface<int>>` collection injection will contain a `Dependency<string, int>`, a `Dependency<int, int>`, and a `Dependency<byte, int>` instance.

Let's consider another example:

```csharp
public interface IInterface { }

internal class Dependency<T0, T1> : IInterface { }

internal class DependencyHolder
{
    internal DependencyHolder(IReadOnlyList<IInterface> dependencies) { }
}
```

And the following configuration accordingly:



```csharp
[GenericParameterSubstitutesChoice(typeof(Dependency<,>), "T0", typeof(int), typeof(byte))]
[GenericParameterSubstitutesChoice(typeof(Dependency<,>), "T1", typeof(string), typeof(long))]
```

In this example, `DependencyHolder` gets a collection injection with one instance of each of the types `Dependency<int, string>`, `Dependency<int, long>`, `Dependency<byte, string>`, and `Dependency<byte, long>`. This instantiates every possible combination of configured and open generic parameters.

If the collection choices are used excessively, the numbers can quickly become large. Consider a class `Dependency<T0, T1, T2, T3>` that implements `IInterface` with each generic parameter configured with five types. A collection injection would contain `5 * 5 * 5 * 5 = 625` instances. Note that this will imply code generation for all of them and(!) their own dependencies.