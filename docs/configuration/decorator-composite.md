# Decorator/Composite Configurations

## Purpose

As DIE doesn't just assume which implementation is a decorator or a composite, you'll have to tell it by - you've guessed it :) - configuration. 

## Attributes

- **DecoratorAbstractionAggregationAttribute** Of each given abstraction all their implementations will be interpreted as decorators. All of the abstractions have to have a single generic type parameter.
```csharp
[DecoratorAbstractionAggregation(typeof(IDecorator<>))]
```
- **FilterDecoratorAbstractionAggregationAttribute** Of each given abstraction all their implementations will be stopped being interpreted as decorators. All of the abstractions have to have a single generic type parameter.
```csharp
[DecoratorAbstractionAggregation(typeof(IDecorator<>))]
```

- **CompositeAbstractionAggregationAttribute** Of each given abstraction all their implementations will be interpreted as composites. All of the abstractions have to have a single generic type parameter.
```csharp
[CompositeAbstractionAggregation(typeof(IComposite<>))]
```
- **FilterCompositeAbstractionAggregationAttribute** Of each given abstraction all their implementations will be stopped being interpreted as composites. All of the abstractions have to have a single generic type parameter.
```csharp
[FilterCompositeAbstractionAggregation(typeof(IComposite<>))]
```

## Recommendations

- Do mark decorators and composites as such, otherwise DIE will interpret them as being additional implementation types of their interface. The latter may cause inconveniences by requiring more configuration (implementation choices or filtering). When marked, decorators and composites will be excluded from regular uses of implementations.