# Scoping Configurations

## Purpose

There are two purposes to configure scoping:

- **Scoped Instances** Scoped instances aren't instantiated per dependency injection but at most once for specific scope. DIE cannot just assume which of the implementations should be treated as scoped instances and for which kind of scope (the container, the transient scope, or the scope). So if you would like to have instances of certain implementations shared, then you need to tell to DIE which implementation in which scope.
- **Scope Roots** Scope roots are implementations that mark the start of a scope. As soon as DIE needs to instantiate a scope root it'll create a new scope and resolve the scope root as the root instance there. Meaning that the scope root is the first instance of its scope. DIE doesn't assume any implementations to be scope roots, hence it needs to be configured

## Attributes

- **ContainerInstanceAbstractionAggregationAttribute** Of each given abstraction all their implementations will be marked as scoped instances for the container.
```csharp
[ContainerInstanceAbstractionAggregation(typeof(IAllTheSingles))]
```
- **FilterContainerInstanceAbstractionAggregationAttribute** Of each given abstraction all their implementations will be opted-out as scoped instances for the container.
```csharp
[FilterContainerInstanceAbstractionAggregation(typeof(INotSingleAnymore))]
```
- **ContainerInstanceImplementationAggregationAttribute** Given implementations will be marked as scoped instances for the container.
```csharp
[ContainerInstanceImplementationAggregation(typeof(Single))]
```
- **FilterContainerInstanceImplementationAggregationAttribute** Given implementations will be opted-out as scoped instances for the container.
```csharp
[FilterContainerInstanceImplementationAggregation(typeof(NotSingleAnymore))]
```

- **TransientScopeInstanceAbstractionAggregationAttribute** Of each given abstraction all their implementations will be marked as scoped instances for transient scopes.
```csharp
[TransientScopeInstanceAbstractionAggregation(typeof(IAllTheSingles))]
```
- **FilterTransientScopeInstanceAbstractionAggregationAttribute** Of each given abstraction all their implementations will be opted-out as scoped instances for transient scopes.
```csharp
[FilterTransientScopeInstanceAbstractionAggregation(typeof(INotSingleAnymore))]
```
- **TransientScopeInstanceImplementationAggregationAttribute** Given implementations will be marked as scoped instances for transient scopes.
```csharp
[TransientScopeInstanceImplementationAggregation(typeof(Single))]
```
- **FilterTransientScopeInstanceImplementationAggregationAttribute** Given implementations will be opted-out as scoped instances for transient scopes.
```csharthe containerp
[FilterTransientScopeInstanceImplementationAggregation(typeof(NotSingleAnymore))]
```

- **ScopeInstanceAbstractionAggregationAttribute** Of each given abstraction all their implementations will be marked as scoped instances for scopes.
```csharp
[ScopeInstanceAbstractionAggregation(typeof(IAllTheSingles))]
```
- **FilterScopeInstanceAbstractionAggregationAttribute** Of each given abstraction all their implementations will be opted-out as scoped instances for scopes.
```csharp
[FilterScopeInstanceAbstractionAggregation(typeof(INotSingleAnymore))]
```
- **ScopeInstanceImplementationAggregationAttribute** Given implementations will be marked as scoped instances for scopes.
```csharp
[ScopeInstanceImplementationAggregation(typeof(Single))]
```
- **FilterScopeInstanceImplementationAggregationAttribute** Given implementations will be opted-out as scoped instances for scopes.
```csharp
[FilterScopeInstanceImplementationAggregation(typeof(NotSingleAnymore))]
```

- **TransientScopeRootAbstractionAggregationAttribute** Of each given abstraction all their implementations will be marked as scope roots for transient scopes.
```csharp
[TransientScopeRootAbstractionAggregation(typeof(IBeginningOfSomething))]
```
- **FilterTransientScopeRootAbstractionAggregationAttribute** Of each given abstraction all their implementations will be opted-out as scope roots for transient scopes.
```csharp
[FilterTransientScopeRootAbstractionAggregation(typeof(IOrdinaryDependency))]
```
- **TransientScopeRootImplementationAggregationAttribute** Given implementations will be marked as scope roots for transient scopes.
```csharp
[TransientScopeRootImplementationAggregation(typeof(Ceo))]
```
- **FilterTransientScopeRootImplementationAggregationAttribute** Given implementations will be opted-out as scope roots for transient scopes.
```csharp
[FilterTransientScopeRootImplementationAggregation(typeof(Trainee))]
```

- **ScopeRootAbstractionAggregationAttribute** Of each given abstraction all their implementations will be marked as scope roots for scopes.
```csharp
[ScopeRootAbstractionAggregation(typeof(IBeginningOfSomething))]
```
- **FilterScopeRootAbstractionAggregationAttribute** Of each given abstraction all their implementations will be opted-out as scope roots for scopes.
```csharp
[FilterScopeRootAbstractionAggregation(typeof(IOrdinaryDependency))]
```
- **ScopeRootImplementationAggregationAttribute** Given implementations will be marked as scope roots for scopes.
```csharp
[ScopeRootImplementationAggregation(typeof(Ceo))]
```
- **FilterScopeRootImplementationAggregationAttribute** Given implementations will be opted-out as scope roots for scopes.
```csharp
[FilterScopeRootImplementationAggregation(typeof(Trainee))]
```

## Recommendations

- Use transient scoped instances on demand basis. The reason for this is that the complexity is higher with transient scoped instances compared to container instances and scoped instances. Therefore, if an instance may as well be shared in the whole container, then consider marking it as container instance. And if an instance can be limited to be shared in a scope, then consider marking it as scope instance.