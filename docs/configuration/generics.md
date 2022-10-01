# Decorator/Composite Configurations

## Purpose

In general, it is possible for a generic parameter to be left open at resolution. If you configure generic parameter substitutes (for collections) and/or singular choices (for singular injections), then the implementation in which the configured generic parameter would otherwise remain open can still be resolved.

## Attributes

- **GenericParameterSubstitutesChoiceAttribute** Aggregates generic type substitutes for the given unbound generic implementation and the selected generic parameter.
```csharp
[GenericParameterSubstitutesChoice(typeof(Implementation<,>), "T0", typeof(int), typeof(string))]
```
- **FilterGenericParameterSubstitutesChoiceAttribute** Discards generic type substitutions for the given unbound generic implementation and the selected generic parameter.
```csharp
[FilterGenericParameterSubstitutesChoice(typeof(Implementation<,>), "T0")]
```
- **GenericParameterChoiceAttribute** Specifies the generic type choice for the given unbound generic implementation and the selected generic parameter. For collections, this choice is automatically added.
```csharp
[GenericParameterChoice(typeof(Implementation<,>), "T0", typeof(int))]
```
- **FilterGenericParameterChoiceAttribute** Discards the generic type choice for the given unbound generic implementation and the selected generic parameter.
```csharp
[FilterGenericParameterChoice(typeof(Implementation<,>), "T0")]
```

## Recommendations

- Be aware that if a generic implementation is resolved into a collection (for example, `IReadOnlyList<...>`) with multiple open generic type parameters, DIE will resolve closed versions of the implementation for all possible combinations of substitutes. For example, if an implementation has three open generic parameters, each of which has five substitutes configured, then `5 * 5 * 5 = 125` closed versions of the implementation will be resolved. The number of closed implementations can grow quickly, so it is recommended to be cautious.
- The singular choices (from `GenericParameterChoice`) are automatically interpreted as additional substitutes for collections. So if you have a `GenericParameterChoice` and a `GenericParameterSubstitutesChoice` for the same implementation/type parameter combination, then it's not necessary to have the chosen type from the `GenericParameterChoice` in the `GenericParameterSubstitutesChoice` as well.