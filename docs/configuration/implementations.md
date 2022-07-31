# Implementation Configurations

## Purpose

Implementations need to be gathered in order to be considered by DIE. If not they won't be considered as a substitute for its interface and injecting the type explicitly will still not compile. The container or the scope need to know which implementations they have to consider, hence the purpose of the implementation configurations is to tell them exactly that.

Other configuration options include choosing constructors (if an implementation has multiple), properties for property injections, an implementation for an absraction (if it has multiple implementations), and collections of implementations for collection injections of abstractions.

## Attributes
The listed attributes are sorted by the potential amount of implementation that they aggregate/filter from more to less:

- **AllImplementationAggregationAttribute** Aggregates all implementations of current assembly and all referenced assemblies. That includes the .Net-assemblies and assemblies from nuget packages as well.
```csharp
[assembly:AllImplementationsAggregation]
```
- **FilterAllImplementationAggregationAttribute** Filters all implementations.
  
```csharp
[FilterAllImplementationsAggregation]
```
- **AssemblyImplementationsAggregationAttribute** Aggregates all implementations of given assemblies. Assemblies are passed by referencing any type from the assembly, because assemblies themselves cannot be referenced in code. You can pass multiple types to this attribute.
```csharp
[AssemblyImplementationsAggregation(typeof(MrMeeseeks.DIE.TestNotInternalsVisibleToChild.AssemblyInfo))]
```
- **FilterAssemblyImplementationsAggregationAttribute** Filters all implementations of given assemblies. Assemblies are passed by any type from the assembly, because assemblies themselves cannot be referenced in code. You can pass multiple types to this attribute.
```csharp
[FilterAssemblyImplementationsAggregation(typeof(MrMeeseeks.DIE.TestNotInternalsVisibleToChild.AssemblyInfo))]
```
- **ImplementationAggregationAttribute** Aggregates all given implementations. Passed types have to be implementations. You can pass multiple types to this attribute.
```csharp
[ImplementationAggregation(typeof(DateTime), typeof(FileInfo))]
```
- **FilterImplementationAggregationAttribute** Filters all given implementations. Passed types have to be implementations. You can pass multiple types to this attribute.
```csharp
[FilterImplementationAggregation(typeof(DateTime), typeof(FileInfo))]
```

- **ConstructorChoiceAttribute** Choses a constructor for the given implementation type which will be used by DIE. If an implemetation has multiple constructors which could be potentially used the constructor choice is mandatory for the implementation to be usable. Pass the implementation type first and then the types of the constructor parameters in the same order. In order to choose the parameterless constructor pass only the implementation type.
```csharp
[ConstructorChoice(typeof(Implementation), typeof(string), typeof(int))]
```
- **FilterConstructorChoiceAttribute** Discards the inherited constructor choice for the given implementation type. Pass the implementation type only.
```csharp
[FilterConstructorChoice(typeof(Implementation))]
```
- **PropertyChoiceAttribute** Choses the properties which be injected during the instantiation. These properties have to be mutable for the container (i.e. either public set/init or internal set/init within the same assembly or with appropriate `InternalsVisibleTo` usage). If no properties choice is active DIE injects all accessible init-properties per default. On the other hand, if a properties choice is active DIE will not inject init-properties which are not passed to the choice. Pass the implementation type first followed by the names of the properties which should be injected as strings.
```csharp
[PropertyChoice(typeof(Implementation), "Logger", "Settings")]
```
- **FilterConstructorChoiceAttribute** Discards the inherited properties choice for the given implementation type. Pass the implementation type only.
```csharp
[FilterPropertyChoice(typeof(Implementation))]
```
- **ImplementationChoiceAttribute** For the given abstraction type it chooses the given implementation type even if multiple implementations are registered. Pass the abstraction type first and the implementation type second.
```csharp
[ImplementationChoice(typeof(IInterface), typeof(Implementation))]
```
- **FilterImplementationChoiceAttribute** Discards the inherited implementation choice for the given abstraction type. Pass the abstraction type only.
```csharp
[FilterImplementationChoice(typeof(IInterface))]
```
- **ImplementationCollectionChoiceAttribute** For the given abstraction type it chooses the given implementation types for collection injections. Pass the abstraction type first and then implementation types.
```csharp
[ImplementationCollectionChoice(typeof(IInterface), typeof(ImplementationA), typeof(ImplementationB))]
```
- **FilterImplementationCollectionChoiceAttribute** Discards the inherited implementation collection choice for the given abstraction type. Pass the abstraction type only.
```csharp
[FilterImplementationCollectionChoice(typeof(IInterface))]
```

## Recommendations

- Use `AllImplementationsAggregation` at assembly level, so that you can opt-out with smaller filters starting from container level.
- If you would like to configure each type explicitly, use `ImplementationAggregation` only.
- Constructor choice are only mandatory if the implementation has multiple constructor candidates. If you know for sure that an implementation has only one usable constructor, then you don't need to configure it.
- Constructor choices are especially relevant for implementations from assemblies that you have no influence over (for example .Net-assemblies), because you can't make sure that these implementations have one constructor only. 
- Because DIE takes over the full control of instantiation init-properties are injected per default. This doesn't apply to set-properties. If you want to override this behavior use property choices. If a property choice is active which doesn't contain the name of an init-property, then the property won't be injected. If a property choice which contains the name of a set-property is active, then the property will be injected. In order to disable property injection for an implementation completely just pass the implementation type only to the property choice.