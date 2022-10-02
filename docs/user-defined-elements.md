# User-Defined Elements

User-defined elements are features that allow customization of certain parts of the generated container. User-defined elements must always be defined within the container, transient scope, or scope class. They only apply to the scope in which they are defined, i.e. they don't inherit from the container to child scopes.

## Factories

Factories can be used to shortcut the container and provide custom creation logic. You can choose from three types of factories: methods, properties, or fields. The identifiers of all factories must begin with `DIE_Factory`.

### Requirements

- Identifier starts with `DIE_Factory`
- `private`
- Non-static
- Method
    - Non-void
    - Parameters are all ordinary (not out/in/ref/params)
- Property
    - Has to have a `get`
- Field
    - No further requirements

### Sample

```csharp
[CreateFunction(typeof(Root), "Create")]
internal sealed partial class Container
{
    private int DIE_Factory_int = 69;
    private string DIE_Factory_Yeah => "Yeah";
    private FileInfo DIE_Factory_CurrentFile(ICurrentPath currentPath) => new (currentPath.Value);
}
```

### Remarks

- The parameters of method factories are method injections and are created in the same way as regular constructor and property injections, i.e. the same configurations apply.

- Note that in this case, DIE won't automatically manage the disposables created by the factory. However, you can use the `AddForDisposal' custom element to manually add disposables for disposal management.

## Constructor Parameters

Instead of customizing the creation of a type with an user-defined factory, this will allow you to override certain constructor parameters.

### Requirements

- Identifier starts with `DIE_ConstrParams`
- `private`
- Non-static
- void
- `UserDefinedConstructorParametersInjection`-attribute
- Two kinds of parameters allowed
    - Ordinary
    - Out parameters each of which has to match a constructor parameter name & type


### Sample

```csharp
[CreateFunction(typeof(Dependency), "Create")]
internal sealed partial class Container
{
    [UserDefinedConstructorParametersInjection(typeof(Dependency))]
    private void DIE_ConstrParam_Dependency(OtherDependency otherDependency, out int number) => number = otherDependency.Number;
}
```

### Remarks

- The ordinary parameters are method injections and are created in the same way as regular constructor and property injections, i.e. the same configurations apply.
- You can match and override multiple constructor parameters

## Properties

Instead of customizing the creation of a type with an user-defined factory, this will allow you to override certain properties.

### Requirements

- Identifier starts with `DIE_Props`
- `private`
- Non-static
- void
- `UserDefinedPropertiesInjection`-attribute
- Two kinds of parameters allowed
    - Ordinary
    - Out parameters each of which has to match a property name & type


### Sample

```csharp
[CreateFunction(typeof(Dependency), "Create")]
internal sealed partial class Container
{
    [UserDefinedPropertiesInjection(typeof(Dependency))]
    private void DIE_ConstrParam_Dependency(OtherDependency otherDependency, out int Number) => Number = otherDependency.Number;
}
```

### Remarks

- The ordinary parameters are method injections and are created in the same way as regular constructor and property injections, i.e. the same configurations apply.
- You can match and override multiple properties

## Initializer Parameters

Rather than customizing the creation of a type with an user-defined factory, this allows certain initializer parameters to be overridden.

### Requirements

- Identifier starts with `DIE_InitParams`
- `private`
- Non-static
- void
- `UserDefinedInitializerParametersInjection`-attribute
- Two kinds of parameters allowed
    - Ordinary
    - Out parameters each of which has to match a constructor parameter name & type


### Sample

```csharp
[CreateFunction(typeof(Dependency), "Create")]
internal sealed partial class Container
{
    [UserDefinedInitializerParametersInjection(typeof(Dependency))]
    private void DIE_ConstrParam_Dependency(OtherDependency otherDependency, out int number) => number = otherDependency.Number;
}
```

### Remarks

- The ordinary parameters are method injections and are created in the same way as regular constructor and property injections, i.e. the same configurations apply.
- You can match and override multiple initializer parameters

## Adding For Disposal

You can declare partial methods that can be used to add disposables to the disposal management of the container (or scope).

### Requirements

- Identifier should be `DIE_AddForDisposal` (for `IDisposable`s) or `DIE_AddForDisposalAsync` (for `IAsyncDisposable`s)
- `private`
- Non-static
- partial
- void
- One parameter, either `IDisposable` or `IAsyncDisposable`
- no own implementation


### Sample

```csharp
[CreateFunction(typeof(Dependency), "Create")]
internal sealed partial class Container
{
    private Dependency DIE_Factory_Dependency
    {
        get
        {
            var dependency = new Dependency();
            DIE_AddForDisposal(dependency);
            return dependency;
        }
    }

    private partial void DIE_AddForDisposal(IDisposable disposable);
}
```

### Remarks

- When such a function is declared, the container assumes that it has disposables. Therefore, it will always generate the disposal code in that case, even if this user-defined function isn't called once.