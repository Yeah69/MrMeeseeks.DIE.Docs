# Implementation Configurations

## Purpose

Implementations need to be gathered in order to be considered by DIE. If not they won't be considered as a substitute for its interface and injecting the type explicitly will still not compile. The container or the scope need to know which implementations they have to consider, hence the purpose of the implementation configurations is to tell them exactly that.

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

## Recommendations

- Use `AllImplementationsAggregation` at assembly level, so that you can opt-out with smaller filters starting from container level.
- If you would like to configure each type explicitly, use `ImplementationAggregation` only.