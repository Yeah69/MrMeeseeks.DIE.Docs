# Decorator/Composite Configurations

## Purpose

In general, it is possible for a generic parameter to remain open at the time of resolution. If you configure generic parameter substitutes (for collections) and/or singular choices (for singular injections), then the implementation where the configured generic parameter would otherwise remain open can still be resolved. 

## Attributes

- **GenericParameterSubstitutesChoiceAttribute** Aggregates generic type substitutes for the given unbound generic implementation and the selected generic parameter.
```csharp
[GenericParameterSubstitutesChoice(typeof(Implementation<,>), "T0", typeof(int), typeof(string))]
```
- **FilterGenericParameterSubstitutesChoiceAttribute** Discards generic type substitutes for the given unbound generic implementation and the selected generic parameter.
```csharp
[FilterGenericParameterSubstitutesChoice(typeof(Implementation<,>), "T0")]
```
- **GenericParameterChoiceAttribute** Determines generic type choice for the given unbound generic implementation and the selected generic parameter. For collections the this choice will be added automatically.
```csharp
[GenericParameterChoice(typeof(Implementation<,>), "T0", typeof(int))]
```
- **FilterGenericParameterChoiceAttribute** Discards generic type choice for the given unbound generic implementation and the selected generic parameter.
```csharp
[FilterGenericParameterChoice(typeof(Implementation<,>), "T0")]
```

## Recommendations

- Be aware that if a generic implementation is resolved into a collection (for example `IReadOnlyList<…>`) with multiple open generic type parameters, DIE will resolve closed versions of the implemetation for all possible combination of the substitutes. For example, if an implentation has three open generic parameters each of which gets five substitutes configured, then `5 * 5 * 5 = 125` closed versions of the implementation will be resolved. The amount of closed implementation can grow fast, so it is recommended to be catious
- The singular choices (from `GenericParameterChoice`) will be automatically interpreted as an additional substitute for collections. Therefore, if you have a `GenericParameterChoice` and a `GenericParameterSubstitutesChoice` for the same implementation/type-parameter combination, then it isn't necessary to the chosen type from the `GenericParameterChoice` in the `GenericParameterSubstitutesChoice` as well.