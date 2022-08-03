# Miscellaneous Configurations

## Purpose

These are the attributes which didn't fit into the other categories. Their purpose is to define initializers and containers/create functions.

## Attributes

- **TypeInitializerAttribute** Lets you define which types have an initilization method wich should be used during instantiation. Pass the type which owns the initializing method first, then the name of the method. There are some constraints for the method: it isn't allowed to have parameters and it has to either return nothing (void), a `Task` or a `ValueTask`.
```csharp
[TypeInitializer(typeof(IAsyncInitializer), "InitializeAsync")]
```
- **FilterTypeInitializerAttribute** Discards an active initializer method definition. Pass the type which owns the initializing method.
```csharp
[FilterTypeInitializer(typeof(IAsyncInitializer))]
```
- **CreateFunctionAttribute** Defines a create function that DIE should generate into the container. This attribute defines which classes should be interpreted as a container. Pass first the type which should be returned by the create function and then the name that the create function should have.
```csharp
[CreateFunction(typeof(IAppRoot), "Create")]
```

## Recommendations

- 