# Decorator/Composite Configurations

## Purpose

As DIE doesn't just assume which implementation is a decorator or a composite, you'll have to tell it by - you've guessed it :) - configuration.

If a decorated interface has more than one decorator, then DIE won't assume the sequence of decorator which should be applied (meaning the order and which decorators should be applied). Therefore, there is an attribute to configure that as well.

## Attributes

- **DecoratorAbstractionAggregationAttribute** Of each given abstraction all their implementations will be interpreted as decorators. All of the abstractions have to have a single generic type parameter.
```csharp
[DecoratorAbstractionAggregation(typeof(IDecorator<>))]
```
- **FilterDecoratorAbstractionAggregationAttribute** Of each given abstraction all their implementations will be stopped being interpreted as decorators. All of the abstractions have to have a single generic type parameter.
```csharp
[FilterDecoratorAbstractionAggregation(typeof(IDecorator<>))]
```
- **DecoratorSequenceChoiceAttribute** Choses a sequence of decorators which'll be applied on the decorated implementation. This attribute is mandatory for all decorated implementations which have multiple decorators. You can either configure a sequence for the decoration interface type or for the concrete implementation. The configuration for the interface will be applied to all of its implementation, however, if existent the configurations for the concrete implementation are prioritized. First pass the decorated type interface, then the interface again (for the fallback) or the decorated implementation type (for the specific), and then a list of decorator implementation types. The decorator implementations will be applied in order, that is the decorated implementation instance will be injected in the first decorator implementation instance, that will be injected into the second and so on. You can also disable decoration by leaving the list of decorator types empty.
```csharp
[DecoratorSequenceChoice(typeof(IDecorated), typeof(DecoratedImplementation), typeof(DecoratorA), typeof(DecoratorB))]
[DecoratorSequenceChoice(typeof(IDecorated), typeof(IDecorated), typeof(DecoratorC), typeof(DecoratorD))]
```
- **FilterDecoratorSequenceChoiceAttribute** Discards an active decoration sequence choice. 
```csharp
[FilterDecoratorSequenceChoice(typeof(IDecorated), typeof(DecoratedImplementation))]
[FilterDecoratorSequenceChoice(typeof(IDecorated), typeof(IDecorated))]
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