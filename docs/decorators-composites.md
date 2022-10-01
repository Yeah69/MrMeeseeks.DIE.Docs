# Decorators & Composites

The purpose of both decorators & composites is to transparently add functionality to an instance of an interface. They do this by wrapping one or more instances of that interface and implementing the interface themselves. The main difference is that while a decorator wraps a single instance, a composite wraps a collection of instances. Therefore, the decorator is used to extend functionality to a decorated instance, and the composite is used to aggregate over multiple instances. The book "Design Patterns" by the Gang of Four is recommended for a better and more thorough description of these two patterns.

## Why Is The Container Responsible For Decorators & Composites?

Because decorators and composites implement the same interface they're wrapping, they become transparent as soon as they're injected as that interface. The consuming side that gets an instance of such an interface injected is completely unaware of it. The consumer neither knows nor makes any assumptions about whether the injected instance is the implementation, or whether there is a chain of decorators in between, or whether it is a composition of implementations. And that is exactly what we are doing with dependency injection: externalizing such responsibilities (decorating & composing interfaces). When using a container, the external entity that manages decorators and composites can only be the container, because it is the entity that injects the dependencies. This is why DIE supports the use of decorators & composites.

## Special Kind Of Implementation

Decorators & Composites are considered implementations in DIE and must be registered/aggregated as such. However, they are a special type of implementation. They won't be used as standalone implementations, but always only in addition to "pure" implementations.

## Decorators

### Example

```csharp
internal interface IInterface
{
    IInterface Decorated { get; }
}

internal class Dependency : IInterface
{
    public IInterface Decorated => this;
}

internal class Decorator : IInterface, IDecorator<IInterface>
{
    public Decorator(IInterface decoratedNormal) => 
        Decorated = decoratedNormal;

    public IInterface Decorated { get; }
}
```

### Requirements

- Implement the interface you want to decorate (e.g., `IInterface`)
- Implement the marker decorator interface (e.g. `IDecorator<>`), which will be registered with a `DecoratorAbstractionAggregation` attribute.
    - The decorator interface should have its generic parameter set to the decorated interface: `IDecorator<IInterface>`.
- Should have a constructor parameter of the type of the decorated interface

### Some Remarks

If a decorated interface has multiple decorators registered, you must configure the order in which the decorators are applied, since DIE can't assume any by itself. You can configure an individual decorator sequence per decorated type and/or a fallback sequence configuration for the decorated interface. If you configure an empty sequence, you're effectively disabling decoration.

If a decorated interface has only one decorator, then a sequence decoration isn't necessary. DIE assumes the intent of decorator usage by its existence, and there's no ambiguity about which decorator to apply first.

Decorators are never scoped instances, even if the decorated instance is scoped.

## Composites

### Example

```csharp
internal interface IInterface
{
    IReadOnlyList<IInterface> Composites { get; }
}

internal class BasisA : IInterface
{
    public IReadOnlyList<IInterface> Composites => new List<IInterface> { this };
}

internal class BasisB : IInterface
{
    public IReadOnlyList<IInterface> Composites => new List<IInterface> { this };
}

internal class Composite : IInterface, IComposite<IInterface>
{
    public Composite(IReadOnlyList<IInterface> composites) => 
        Composites = composites;

    public IReadOnlyList<IInterface> Composites { get; }
}
```

### Requirements

- Implement the interface that will be composited (e.g., `IInterface`)
- Implement the marker composite interface (e.g. `IComposite<>`), which will be registered with a `CompositeAbstractionAggregation` attribute.
    - The composite interface should have its generic parameter set to the composited interface: `IComposite<IInterface>`.
- Should have a constructor parameter of the collection type of the composite interface

### Some Remarks

Composites are never scoped instances, even if all of the composite instances are scoped.

You can have only one composite type per interface.

## Combinations

If the interface has multiple implementations and is both decorated and composited, then you can combine decorators and composites however you like. You can even decorate the composite.