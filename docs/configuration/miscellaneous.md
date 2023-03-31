# Miscellaneous Configurations

## Purpose

These are the attributes that don't fit into the other categories. Their purpose is to define initializers and containers/create functions.

## Attributes

- **InitializedInstancesAttribute** Can be used on container and scope classes. Instances of given implementation types will be created automatically in time of initialization of the relevant container or scope.
```csharp
[InitializedInstances(typeof(OperatingSystemObserver), typeof(DatabaseInitialization))]
```
- **InitializerAttribute** Lets you specify which types have an initialization method that should be used during instantiation. Pass the type that owns the initialization method first, then the name of the method. There is one constraint on the method: it must return either nothing (void), a `Task`, or a `ValueTask`.
```csharp
[Initializer(typeof(IAsyncInitializer), "InitializeAsync")]
```
- **FilterInitializerAttribute** Discards an active initializer method definition. Pass the type that owns the initializer method.
```csharp
[FilterInitializer(typeof(IAsyncInitializer))]
```
- **CreateFunctionAttribute** Defines a create function that DIE should create in the container. This attribute defines which classes should be interpreted as containers. Pass first the type to be returned by the create function and then the name to be given to the create function.
```csharp
[CreateFunction(typeof(IAppRoot), "Create")]
```