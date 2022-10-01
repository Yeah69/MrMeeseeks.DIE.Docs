# Scoping Configurations

## Purpose

There are two purposes for scoping configurations:

- **Scoped Instances** Scoped instances aren't instantiated per dependency injection, but at most once for a given scope. DIE cannot simply assume which of the implementations should be treated as scoped instances and for which type of scope (the container, the transient scope, or the scope). So if you want to share instances of certain implementations, you must tell DIE which implementation in which scope.
- **Scope Roots** are implementations that mark the beginning of a scope. When DIE needs to instantiate a scope root, it creates a new scope and resolves the scope root as it's root instance. That is, the scope root is the first instance of its scope. DIE doesn't expect any implementations to be scope roots, so it needs to be configured.

## Attributes

- **ContainerInstanceAbstractionAggregationAttribute** For each given abstraction, all of its implementations are marked as scoped instances for the container.
```csharp
[ContainerInstanceAbstractionAggregation(typeof(IAllTheSingles))]
```
- **FilterContainerInstanceAbstractionAggregationAttribute** For each given abstraction, all of its implementations are discarded as scoped instances for the container.
```csharp
[FilterContainerInstanceAbstractionAggregation(typeof(INotSingleAnymore))]
```
- **ContainerInstanceImplementationAggregationAttribute** Given implementations are marked as scoped instances for the container.
```csharp
[ContainerInstanceImplementationAggregation(typeof(Single))]
```
- **FilterContainerInstanceImplementationAggregationAttribute** Given implementations are discarded as scoped instances for the container.
```csharp
[FilterContainerInstanceImplementationAggregation(typeof(NotSingleAnymore))]
```

- **TransientScopeInstanceAbstractionAggregationAttribute** For each given abstraction, all of its implementations are marked as scoped instances for transient scopes.
```csharp
[TransientScopeInstanceAbstractionAggregation(typeof(IAllTheSingles))]
```
- **FilterTransientScopeInstanceAbstractionAggregationAttribute** For any given abstraction, all of its implementations are discarded as scoped instances for transient scopes.
```csharp
[FilterTransientScopeInstanceAbstractionAggregation(typeof(INotSingleAnymore))]
```
- **TransientScopeInstanceImplementationAggregationAttribute** Given implementations are marked as scoped instances for transient scopes.
```csharp
[TransientScopeInstanceImplementationAggregation(typeof(Single))]
```
- **FilterTransientScopeInstanceImplementationAggregationAttribute** Given implementations are discarded as scoped instances for transient scopes.
```csharp
[FilterTransientScopeInstanceImplementationAggregation(typeof(NotSingleAnymore))]
```

- **ScopeInstanceAbstractionAggregationAttribute** For each given abstraction, all of its implementations are marked as scoped instances for scopes.
```csharp
[ScopeInstanceAbstractionAggregation(typeof(IAllTheSingles))]
```
- **FilterScopeInstanceAbstractionAggregationAttribute** Of any given abstraction, all of its implementations are discarded as scoped instances for scopes.
```csharp
[FilterScopeInstanceAbstractionAggregation(typeof(INotSingleAnymore))]
```
- **ScopeInstanceImplementationAggregationAttribute** Given implementations are marked as scoped instances for scopes.
```csharp
[ScopeInstanceImplementationAggregation(typeof(Single))]
```
- **FilterScopeInstanceImplementationAggregationAttribute** Given implementations are discarded as scoped instances for scopes.
```csharp
[FilterScopeInstanceImplementationAggregation(typeof(NotSingleAnymore))]
```

- **TransientScopeRootAbstractionAggregationAttribute** For each given abstraction, all of its implementations are marked as scope roots for transient scopes.
```csharp
[TransientScopeRootAbstractionAggregation(typeof(IBeginningOfSomething))]
```
- **FilterTransientScopeRootAbstractionAggregationAttribute** For any given abstraction, all of its implementations are discarded as scope roots for transient scopes.
```csharp
[FilterTransientScopeRootAbstractionAggregation(typeof(IOrdinaryDependency))]
```
- **TransientScopeRootImplementationAggregationAttribute** Given implementations are marked as scope roots for transient scopes.
```csharp
[TransientScopeRootImplementationAggregation(typeof(Ceo))]
```
- **FilterTransientScopeRootImplementationAggregationAttribute** Given implementations are discarded as scope roots for transient scopes.
```csharp
[FilterTransientScopeRootImplementationAggregation(typeof(Trainee))]
```

- **ScopeRootAbstractionAggregationAttribute** For each given abstraction, all of its implementations are marked as scope roots for scopes.
```csharp
[ScopeRootAbstractionAggregation(typeof(IBeginningOfSomething))]
```
- **FilterScopeRootAbstractionAggregationAttribute** For any given abstraction, all of its implementations are discarded as scope roots for scopes.
```csharp
[FilterScopeRootAbstractionAggregation(typeof(IOrdinaryDependency))]
```
- **ScopeRootImplementationAggregationAttribute** Given implementations are marked as scope roots for scopes.
```csharp
[ScopeRootImplementationAggregation(typeof(Ceo))]
```
- **FilterScopeRootImplementationAggregationAttribute** Given implementations are discarded as scope roots for scopes.
```csharp
[FilterScopeRootImplementationAggregation(typeof(Trainee))]
```

## Recommendations

- Use transient scoped instances as needed. This is because the complexity of transient scoped instances is greater than that of container instances and scoped instances. Therefore, if an instance can be used throughout the container, you should consider marking it as a container instance. And if an instance can be limited to being shared in a scope, then consider marking it as a scope instance.