# Decorator/Composite Configurations

## Purpose

Since DIE doesn't just assume which implementation is a decorator or a composite, you've got to tell it by - you guessed it :) - configuration.

When a decorated interface has more than one decorator, DIE doesn't assume the order in which the decorators should be applied. Therefore, there is an attribute that can be used to configure this as well.

## Attributes

- **DecoratorAbstractionAggregationAttribute** Of each given abstraction, all its implementations are interpreted as decorators. All abstractions are required to have a single generic type parameter.
```csharp
[DecoratorAbstractionAggregation(typeof(IDecorator<>))]
```
- **FilterDecoratorAbstractionAggregationAttribute** Of each given abstraction, all its implementations are stopped being interpreted as decorators. All abstractions are required to have a single generic type parameter.
```csharp
[FilterDecoratorAbstractionAggregation(typeof(IDecorator<>))]
```
- **DecoratorSequenceChoiceAttribute** Selects a sequence of decorators to apply to the decorated implementation. This attribute is mandatory for all decorated implementations that have multiple decorators. A sequence can be configured either for the decorator interface type or for the concrete implementation. The configuration for the interface will be applied to all of its implementations, but if present, the configurations for the concrete implementation will take precedence. First, pass the decorated type interface, then the interface again (for fallback) or decorated implementation type (for specific), and then a list of decorator implementation types. The decorator implementations will be applied in order, that is, the decorated implementation instance will be injected into the first decorator implementation instance, which will be injected into the second, and so on. You can also disable decoration by leaving the list of decorator types empty.
```csharp
[DecoratorSequenceChoice(typeof(IDecorated), typeof(DecoratedImplementation), typeof(DecoratorA), typeof(DecoratorB))]
[DecoratorSequenceChoice(typeof(IDecorated), typeof(IDecorated), typeof(DecoratorC), typeof(DecoratorD))]
```
- **FilterDecoratorSequenceChoiceAttribute** Cancels an active Decoration Sequence selection.
```csharp
[FilterDecoratorSequenceChoice(typeof(IDecorated), typeof(DecoratedImplementation))]
[FilterDecoratorSequenceChoice(typeof(IDecorated), typeof(IDecorated))]
```

- **CompositeAbstractionAggregationAttribute** Of each given abstraction, all its implementations are interpreted as composites. All abstractions are required to have a single generic type parameter.
```csharp
[CompositeAbstractionAggregation(typeof(IComposite<>))]
```
- **FilterCompositeAbstractionAggregationAttribute** Of each given abstraction, all its implementations are interpreted as composites. All abstractions are required to have a single generic type parameter.
```csharp
[FilterCompositeAbstractionAggregation(typeof(IComposite<>))]
```

## Recommendations

- Decorators and composites should be marked as such, otherwise DIE will interpret them as additional implementation types of their interface. The latter can be inconvenient, requiring more configuration (implementation choices or filtering). When marked, decorators and composites are excluded from regular use of implementations.