# Scoping

Scoping is a means of subdividing the container. With DIE, there are several reasons to subdivide into scopes:

- **Configuration** Scopes can be used to change configurations at the container level. For example, you can use different implementation choices, and scopes can have their own custom elements such as factories. Almost any configuration can be changed at the scope level.
- **Instance Sharing** Instances can be shared for scopes instead of the whole container.
- **Collective Disposal** Scopes have also their own disposal management. This means that managed disposals instantiated in a scope will be disposed of when the scope is disposed of. Special scopes, called "transient scopes", can be used to decouple the disposal from the container disposal and be disposed of eagerly.

## How to use scopes?

The answer to this must be twofold. One is to configure scopes, and the other is to indirectly spawn scopes in the codebase wherever you want.

Take this example, which includes both sides of scope usage.

```csharp
internal interface IDependency {}

internal class DependencyContainer : IDependency {}

internal class DependencyTransientScope : IDependency {}

internal class DependencyScope : IDependency {}

internal class TransientScope : ITransientScopeRoot
{
    public TransientScope(IDependency dependency) => Dependency = dependency;

    public IDependency Dependency { get; }
}

internal class Scope : IScopeRoot
{
    public Scope(IDependency dependency) => Dependency = dependency;

    public IDependency Dependency { get; }
}

[TransientScopeRootAbstractionAggregation(typeof(ITransientScopeRoot))]
[ScopeRootAbstractionAggregation(typeof(IScopeRoot))]
[CreateFunction(typeof(IDependency), "Create0")]
[CreateFunction(typeof(TransientScope), "Create1")]
[CreateFunction(typeof(Scope), "Create2")]
[FilterImplementationAggregation(typeof(DependencyScope))]
[FilterImplementationAggregation(typeof(DependencyTransientScope))]
internal sealed partial class Container
{
    [FilterImplementationAggregation(typeof(DependencyContainer))]
    [ImplementationAggregation(typeof(DependencyTransientScope))]
    private sealed partial class DIE_DefaultTransientScope
    {
        
    }

    [FilterImplementationAggregation(typeof(DependencyContainer))]
    [ImplementationAggregation(typeof(DependencyScope))]
    private sealed partial class DIE_DefaultScope
    {
        
    }
}
```

Let's start with configuration. Implicitly, there's always at least one configuration for a default scope and one for a default transient scope. If you want to customize them, you can do so by declaring a nested class `DIE_DefaultScope` and/or a nested class `DIE_DefaultTransientScope` in the container class. In the example above, we use the default scope configuration classes to filter and aggregate implementations in such a way that the normal scope and the transient scope would map a different implementation to the `IDependency` interface. The nested scope configuration classes must be `private`, `sealed`, and `partial`.

The other part of using scopes is to define from where in your codebase to start a new scope, and which scope to start. This is done by declaring scope roots. In the example, the marker interface `IScopeRoot` is used to mark the implementation `Scope` as the scope root. In general, you could just inject the scope root into any implementation to start a scope there. In this minimalist example, the return type of the create function is the scope root. Therefore, the scope will be the first thing created.

### Custom Scopes

In addition to the default scope and default transient scope, you can also configure custom scopes for scope roots of your choice. See this example:

```csharp
[CreateFunction(typeof(IInterface), "Create0")]
[CreateFunction(typeof(TransientScopeRoot), "Create1")]
[CreateFunction(typeof(TransientScopeRootSpecific), "Create2")]
[CreateFunction(typeof(ScopeRoot), "Create3")]
[CreateFunction(typeof(ScopeRootSpecific), "Create4")]
[DecoratorSequenceChoice(typeof(IInterface), typeof(IInterface), typeof(ContainerDecorator))]
internal sealed partial class Container
{
    [DecoratorSequenceChoice(typeof(IInterface), typeof(IInterface), typeof(TransientScopeDecorator))]

    [CustomScopeForRootTypes(typeof(TransientScopeRootSpecific1))]
    private sealed partial class DIE_TransientScope_A
    {
        
    }
    
    [DecoratorSequenceChoice(typeof(IInterface), typeof(IInterface), typeof(TransientScopeSpecificDecorator))]

    [CustomScopeForRootTypes(typeof(TransientScopeRootSpecific0), typeof(TransientScopeRootSpecific2))]
    private sealed partial class DIE_TransientScope_B
    {
        
    }

    [DecoratorSequenceChoice(typeof(IInterface), typeof(IInterface), typeof(ScopeDecorator))]

    [CustomScopeForRootTypes(typeof(ScopeRootSpecific1))]
    private sealed partial class DIE_Scope_A
    {
        
    }
    
    [DecoratorSequenceChoice(typeof(IInterface), typeof(IInterface), typeof(ScopeSpecificDecorator))]

    [CustomScopeForRootTypes(typeof(ScopeRootSpecific0), typeof(ScopeRootSpecific1))]
    private sealed partial class DIE_Scope_B
    {
        
    }
}
```

Custom (transient) scopes are declared similarly to the default (transient) scope. However, the custom transient scope identifier must start with `DIE_TransientScope` and the custom scope identifier must start with `DIE_Scope`. Also, all custom (transient) scopes must have at least one `CustomScopeForRootTypesAttribute` attribute with at least one type parameter. The type parameters must be (transient) scope root implementations. On a match, DIE will use the custom (transient) scope instead of the default.

It is possible to define a different (transient) scope for each individual (transient) scope root. However, keep in mind that code must be generated for each distinct (transient) scope. This isn't necessarily a bad thing, you may decide it's worth it for your project.

## Scope Instances

Similar to container instances, which are guaranteed to be created once and shared across the entire container, you can configure implementations to be treated as scope instances. As such, they're created once per scope and shared across the scope.

In addition to scope instances, there are also transient scope instances. However, the latter have one important difference: they are shared by the entire transient scope and all ordinary scopes created directly or transitively from the transient scope. 

Containers can also create scope and transient scope instances. This means that in resolution situations where no scope or transient scope has yet been created, the container can effectively act as one. The same principle applies to transient scopes and scope instances.

### Use Transient Scope Instances Sparingly

Transient scope instances are by far the most complex compared to container and scope instances. To understand this, let's first deduce why container instances are simpler.

Scopes get an instance of the container injected. This instance is then used by the scope to access the container instances. This is simple because all scopes know only one instance of the container type, so it is very clear what to use.

However, the same situation becomes very unclear with transient scopes. A scope can be created under a transient scope or under the container. So when a scope needs to access a transient scope instance, which type should be used? 

The approach taken for DIE was to introduce an interface for accessing transient scope instances. So instead of a concrete type, scopes get an instance of an interface type injected, so the scope doesn't have to care what type its big parent has. This interface must be implemented by all different transient scopes and by the container. However, this means that, for example, the container must be able to generate functions for transient scope instances even if they aren't used in practice. The same problem applies to all different transient scopes.

Because of all this, transient scope instances can be difficult to work with, and they'll result in significantly more generated code than the other types of shared instances. Therefore, it is recommended to use transient scope instances only when you really need them (i.e., when you need to share an instance in the transient scope/container and all its underlying scopes).

## Configuration Restrictions

Configurations such as the container instance configuration only make sense on a container-wide basis. Therefore, they aren't allowed to be used on (transient) scopes. These configurations are

- Container instance
- Transient scope instance
- Transient scope root
- Scope root

Any configurations (aggregation, filtering, implementation, abstraction) that affect these listed aspects aren't allowed on (transient) scopes.