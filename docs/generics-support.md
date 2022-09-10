# Generics Support

DIE support usage of generic types. This involves the configuration and the usage. 

## Configuration

Generic implementations can be configured for the same things as the non-generic implementations. However, generic implementations have to be 

// Basic generics support

// Implementations

// Interfaces

## Undeterminable Generic Type Parameters With Interface Injections

Situations are possible in which DIE can't automatically determine a generic type parameter for an implementation when injecting through an interface. Here is a minimal example:

```csharp
public interface IInterface<T> { }

internal class Dependency<T0, T1> : IInterface<T1> { }

internal class Parent
{
    internal Parent(IInterface<int> dependency) { } // What should be put into T0?
}
```

So, in this example if DIE needs to relsolve `Parent` and `Dependency<T0, T1>` is the only implementation of `IInterface<T>`, then it is clear that `T1` gets `int` assigned. However, in this situation `T0` can't be resolved. 

For resolutions of such situations DIE offers a configuration feature. You can make a generic type paramter choice for instance injections (singular) or a generic type parameter collection choice for collection injections. Both are configured per implementation type. You can also mix these to kinds of choices. If you do only a singular choice, then it'll be chosen as single item for collection injections as well. If you configure a single type in the collection choice and no singular choice, then it'll be chosen for instance injections. 

In order to resolve the example following configuration example can be applied:

```csharp
[GenericParameterChoice(typeof(Dependency<,>), "T0", typeof(string))]
```

If you to add type substitutes for collection injection, you can use the following configuration:

```csharp
[GenericParameterSubstitutesChoice(typeof(Dependency<,>), "T0", typeof(int), typeof(byte))]
```

With that two configurations in place a `IReadOnlyList<IInterface<int>>` collection injection will contain a `Dependency<string, int>`, a `Dependency<int, int>`, and a `Dependency<byte, int>` instance.

Let's consider another example:

```csharp
public interface IInterface { }

internal class Dependency<T0, T1> : IInterface { }

internal class Parent
{
    internal Parent(IReadOnlyList<IInterface> dependencies) { }
}
```

And accordingly following configuration:



```csharp
[GenericParameterSubstitutesChoice(typeof(Dependency<,>), "T0", typeof(int), typeof(byte))]
[GenericParameterSubstitutesChoice(typeof(Dependency<,>), "T1", typeof(string), typeof(long))]
```

In that example `Parent` will get a collection injection with one instance of each types `Dependency<int, string>`, `Dependency<int, long>`, `Dependency<byte, string>`, and `Dependency<byte, long>`. Thus, every possible combination of the configured and open generic parameters is instantiated.

If using the collection choice exessively the numbers can become great fast. Consider a class `Dependency<T0, T1, T2, T3>` implementing `IInterface` with each generic parameter getting five types configured. A collection injection would contain `5 * 5 * 5 * 5 = 625` instances. Keep in mind that this'll imply code generation for all of them and(!) their own dependencies.