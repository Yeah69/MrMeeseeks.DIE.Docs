# Implementation Configurations

## Purpose

Implementations must be collected in order to be considered by DIE. Otherwise, they won't be considered as substitutes for their interface, and injecting the type explicitly still won't compile. The container or scope needs to know which implementations to consider, so the purpose of implementation configuration is to tell them.

Other configuration options include choosing constructors (if an implementation has multiple), properties for property injections, an implementation for an abstraction (if it has multiple implementations), and collections of implementations for collection injections of abstractions.

## Attributes

- **AllImplementationAggregationAttribute** Aggregates all implementations of the current assembly and all referenced assemblies. This includes .Net assemblies and assemblies from nuget packages.
```csharp
[assembly:AllImplementationsAggregation]
```
- **FilterAllImplementationAggregationAttribute** Filters all implementations.
  
```csharp
[FilterAllImplementationsAggregation]
```
- **AssemblyImplementationsAggregationAttribute** Aggregates all implementations of a given assembly. Assemblies are passed by referencing any type from the assembly, because assemblies themselves cannot be referenced in code. You may pass multiple types to this attribute.
```csharp
[AssemblyImplementationsAggregation(typeof(MrMeeseeks.DIE.TestNotInternalsVisibleToChild.AssemblyInfo))]
```
- **FilterAssemblyImplementationsAggregationAttribute** Filters all implementations of a given assembly. Assemblies are passed by any type from the assembly because assemblies themselves cannot be referenced in code. You may pass multiple types to this attribute.
```csharp
[FilterAssemblyImplementationsAggregation(typeof(MrMeeseeks.DIE.TestNotInternalsVisibleToChild.AssemblyInfo))]
```
- **ImplementationAggregationAttribute** Aggregates all given implementations. Types passed must be implementations. You may pass multiple types to this attribute.
```csharp
[ImplementationAggregation(typeof(DateTime), typeof(FileInfo))]
```
- **FilterImplementationAggregationAttribute** Filters all given implementations. Types passed must be implementations. You may pass multiple types to this attribute.
```csharp
[FilterImplementationAggregation(typeof(DateTime), typeof(FileInfo))]
```

- **ConstructorChoiceAttribute** Selects a constructor for the given implementation type to be used by DIE. If an implementation has multiple constructors that could potentially be used, choosing a constructor is mandatory for the implementation to be usable. Pass the implementation type first, then the types of the constructor parameters in the same order. To choose the parameterless constructor, just pass the only implementation type.
```csharp
[ConstructorChoice(typeof(Implementation), typeof(string), typeof(int))]
```
- **FilterConstructorChoiceAttribute** Discards the inherited constructor choice for the given implementation type. Just pass the implementation type.
```csharp
[FilterConstructorChoice(typeof(Implementation))]
```
- **PropertyChoiceAttribute** Selects the properties to be injected during instantiation. These properties must be mutable for the container (i.e. either public set/init or internal set/init within the same assembly or with appropriate `InternalsVisibleTo` usage). If no property choice is active, DIE will inject all accessible init properties by default. On the other hand, if a property choice is active, DIE will not inject any init properties that are not passed to the choice. Pass the implementation type first, followed by the names of the properties to be injected as strings.
```csharp
[PropertyChoice(typeof(Implementation), "Logger", "Settings")]
```
- **FilterConstructorChoiceAttribute** Discards the current property choice for the given implementation type. Just pass the implementation type.
```csharp
[FilterPropertyChoice(typeof(Implementation))]
```
- **ImplementationChoiceAttribute** For the given abstraction type, it chooses the given implementation type, even if multiple implementations are registered. Pass the abstraction type first and the implementation type second.
```csharp
[ImplementationChoice(typeof(IInterface), typeof(Implementation))]
```
- **FilterImplementationChoiceAttribute** Discards the current implementation choice for the given abstraction type. Pass only the abstraction type.
```csharp
[FilterImplementationChoice(typeof(IInterface))]
```
- **ImplementationCollectionChoiceAttribute** For the given abstraction type, it chooses the implementation types for collection injections. Pass the abstraction type first, then the implementation types.
```csharp
[ImplementationCollectionChoice(typeof(IInterface), typeof(ImplementationA), typeof(ImplementationB))]
```
- **FilterImplementationCollectionChoiceAttribute** Discards the inherited implementation collection choice for the given abstraction type. Pass only the abstraction type.
```csharp
[FilterImplementationCollectionChoice(typeof(IInterface))]
```

## Recommendations

- Use `AllImplementationsAggregation` at the assembly level, so that you can opt out with smaller filters starting at the container level.
- If you want to configure each type explicitly, use only `ImplementationAggregation'.
- Constructor choices are only mandatory if the implementation has multiple constructor candidates. If you know for sure that an implementation has only one usable constructor, you don't need to configure it.
- Constructor choice is especially important for implementations of assemblies that you don't have control over (such as .Net assemblies), because you can't make sure that these implementations have only one constructor. 
- Because DIE takes full control of instantiation, init properties are injected by default. This doesn't apply to set properties. If you want to override this behavior, use property choices. If a property choice is active that doesn't contain the name of an init property, then that property will not be injected. If a property choice is active that contains the name of a set property, then the property will be injected. To completely disable property injection for an implementation, just pass the implementation type to the property choice.