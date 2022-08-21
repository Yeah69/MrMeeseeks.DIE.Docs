# Scoping

Scoping is a means of subdivision of the container. With DIE there are several reasons to subdivide into scopes:

- **Configuration** Scopes can be used to alter the configurations at container level. For example, you can use different implementation choices and scopes can have their own user-defined elements like factories. Almost all configurations can be changed on the scope level.
- **Instance Sharing** Instances can be shared for scopes instead of the whole container.
- **Collective Disposal** Scopes have also their own disposal management. That means that managed disposals which were instantiated in a scope will get disposed as soon as the scope is disposed. Specials scopes called "transient scopes" can be used to decouple disposal from the container disposal and be disposed eagerly.

## How to use scopes?

The answer to this has to be two-fold. One is configuring scopes and the other is to indirectly spawn scopes in the codebase where ever wanted.

Take this example which contains both sides of the scope usage.

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

Let's start with the configuration. Implicitly there'll always be at least one configuration for a default scope and one for a default transient scope. If you would like to make adjustments to them you have the option by declaring a nested class `DIE_DefaultScope` and/or a nested class `DIE_DefaultTransientScope` in the container class. In the example above we use the default scope configuration classes in order to filter and aggregate implemenations in such a way that the ordinary scope and the transient scope would map another implementation to the interface `IDependency`. The nested scope configuration classes have to be `private`, `sealed`, and `partial`.

The other part of using scopes is to define from where in your codebase a new scope should be started and which one. This is done by declaring scope roots. In the example the marker interface `IScopeRoot` is used to mark the implementation `Scope` as a scope root. Generally you could now just inject the scope root into any implementation to start a scope there. In this minimalistic example the return type of the create function is the scope root. Therefore, the scope will be the first thing which will be created.

### Custom Scopes

Additionally to the default scope and the default transient scope, you can also configure custom scopes for scope roots of your choice. See this example:

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

Custom (transient) scopes are declared much like the default (transient) scope. However, the custom transient scope's identifier has to start with `DIE_TransientScope` and the custom scope's identifier has to start with `DIE_Scope`. Furthermore, all custom (transient) scopes need to have at least one `CustomScopeForRootTypesAttribute`-attribute with at least one type-parameter. The type parameters have to be (transient) scope root implementations. On a match DIE will use the custom (transient) scope instead of the default one.

Potentially, a distinct (transient) scope can be defined for each individual (transient) scope root. However, keep in mind that code has to be generated for each distinct (transient) scope. That doesn't necessarily has to be a bad thing, you'll may decide for your project that it's worth it.

## Scope Instances

Much like the container instances, which are guaranteed to be created once and shared throughout the whole container, you can configure implementations to be treated as scope instances. As such they'll be created once per scope and shared throughout the scope.

Additionally, to scope instances there are also transient scope instances. However, the latter have one essential distinction: they are shared throughout the transient scope and and all ordinary scopes which were created directly or transitively from the transient scope. 

Container can create scope and transient scope instances as well. That means in resolution situations were no scope or transient scope is created yet, the container can effectively act as one. Same principle applies to transient scopes and scope instances.

### Use Transient Scope Instances Sparingly

Transient scope instances are by far the most complex when compared with container and scope instances. In order to understand that let's first deduct why container instances are simpler.

Scopes will get an instance of the container injected. That instance is then used by the scope in order to access the container instances. It is simple, because all scopes only know of one container type and instance, so it is very clear what to use.

However, the same situation gets very unclear with transient scopes. A scope could be created below a transient scope or below the container. So if a scope needs to access a transient scope instance which type should be used? 

The approach that was chosen for DIE was to introduce an interface for accessing transient scope instances. So instead of a concrete type, scopes will get an instance of an interface type injected, hence, the scope doesn't need to care which type its big parent has. This interface needs to be implemented by all distinct transient scopes and by the container. However, that implies that for example the container needs to be able to generate functions for transient scope instances even if they won't be used in practise. The same issue applies to all distinct transient scopes.

Because of all this transient scope instances may become difficult to work with and they'll lead to substantially more generated code then the other kinds of shared instances. Therefore, it is recommended to only use transient scope instances when they are really needed (i.e. if you need to share an instances in the transient scope/container and all its underlying scopes).

## Configuration Restrictions

Configurations like which implementations to interpret as container instances only make sense container-wide. Therefore they aren't allowed to be used on (transient) scopes. These configurations are:

- Container instance
- Transient scope instance
- Transient scope root
- Scope root

Any configurations (aggregation, filtering, implementation, abstraction) concerning these listed aspects won't be allowed on (transient) scopes.