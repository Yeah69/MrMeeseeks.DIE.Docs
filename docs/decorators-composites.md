# Decorators & Composites

The purpose of both, decorators & composites, is to enhance an interface. They accomplish that by wrapping instance(s) of that interface and implementating the interface themselves. The main difference is: while a decorator wraps a singular instance, the composite wraps a collection of instances. Therefore, the decorator is used to enhance functionality for a decorated instance, and the composite is used to aggregate over multiple instances. The book "Design Patterns" by the Gang of Four is recommended for a better and more thorough description of these two patterns.

## Why Is The Container Responsible Of Decorators & Composites?

Because decorators and composites do implement the same interface that they are wrapping, they become transparent as soon as they'll be injected as that interface. The consuming side which gets an instance of such an interface injected is completely oblivious. The consumer neither knows nor makes assumptions of wheter the injected instance is the implementation or whether there is a chain of decorators in between or whether it is a composition of implementation. And that is exactly what we are going for with dependency injection: such responsibilities (decoration & composition of interfaces) are externalized. When using a container the extenal entity managing decorators and composites can only be the container, because it is the entity injecting the dependencies. Therefore, DIE supports the use of decorators & composites.

## Special Kind Of Implementation

Decorators & Composites are considered implementations in DIE and have to be registered/aggregated as such. However, they are a special kind of implementation. They won't be used as standalone implementation but always only supplementary to "pure" implementations.

## Decorators

### Requirements

- Implement the interface which will be decorated (e.g. `IInterface`)
- Implement marker decorator interface (e.g. `IDecorator<>`) which will be registered with a `DecoratorAbstractionAggregation` attribute
    - The decorator interface should have set its generic parameter to the decorated interface: `IDecorator<IInterface>`
- Should have a constructor parameter of type of the decorated interface

### Sample

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

### Some Remarks

If a decorated interface has multiple decorators registered, then you'll need to configure the sequence of decorators which should be applied, because DIE can't assume any on its own. You can configure an individual decorater sequence per decorated type and/or a fallback sequence configuration for the decoraded interface. If you configure an empty sequence, then you'll effectively disable decoration.

If a decorated interface has only one decorator, then a sequence decoration isn't necessary. DIE will assume the intetion of decorator usage by its existence and there isn't any ambiguity which decorator to apply first.

Decoraters will never be scoped instances even if the decorated instace is scoped.

## Composites

### Requirements

- Implement the interface which will be composited (e.g. `IInterface`)
- Implement marker composite interface (e.g. `IComposite<>`) which will be registered with a `CompositeAbstractionAggregation` attribute
    - The composite interface should have set its generic parameter to the decorated interface: `IComposite<IInterface>`
- Should have a constructor parameter of collection type of the composited interface

### Sample

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

### Some Remarks

Composites will never be scoped instances even if all the composited instace is scoped.

You can only use one composite type per interface.

## Combinations

If the interface has multiple implementations and is both, decorated and composited, then you can combine decorators and composite the way you like to. You can even decorate the composite as well.