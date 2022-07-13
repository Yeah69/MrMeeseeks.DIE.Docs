# Disposal Configurations

## Purpose

DIE manages disposables for you automatically and will dispose of them as soon as the container or the (transient) scope is disposed. However, sometimes you like to handle the disposal yourself. In these cases you need to tell the container or the scopes to not manage these specific implementations. 

The attributes come in three flavors can be distinguished by the following terms:

- **SyncTransient** ignores `IDisposable` implementations for disposal managment.
- **AsyncTransient** ignores `IAsyncDisposable` implementations for disposal managment. 
- **Transient** ignores `IDisposable` & `IAsyncDisposable` implementations for disposal managment.

## Attributes

- **TransientAbstractionAggregationAttribute** Of each given abstraction all their implementations will be ignored for disposal management completely.
```csharp
[TransientAbstractionAggregation(typeof(IWillCleanItUpMyself))]
```
- **FilterTransientAbstractionAggregationAttribute** Of each given abstraction all their implementations will be considered for disposal management again.
```csharp
[FilterTransientAbstractionAggregation(typeof(IWillCleanItUpMyselfAsync))]
```
- **TransientImplementationAggregationAttribute** Each given implementation will be ignored for disposal management completely.
```csharp
[TransientImplementationAggregation(typeof(DisposeMeAsap))]
```
- **FilterTransientAbstractionAggregationAttribute** Each given implementation will be considered for disposal management again.
```csharp
[FilterTransientImplementationAggregation(typeof(DisposeMeAsapAsync))]
```
- **SyncTransientAbstractionAggregationAttribute** Of each given abstraction all their implementations will be ignored for sync disposal management.
```csharp
[SyncTransientAbstractionAggregation(typeof(IWillCleanItUpMyself))]
```
- **FilterSyncTransientAbstractionAggregationAttribute** Of each given abstraction all their implementations will be considered for sync disposal management.
```csharp
[FilterSyncTransientAbstractionAggregation(typeof(IWillCleanItUpMyself))]
```
- **SyncTransientImplementationAggregationAttribute** Each given implementation will be ignored for sync disposal management.
```csharp
[SyncTransientImplementationAggregation(typeof(DisposeMeAsap))]
```
- **FilterSyncTransientAbstractionAggregationAttribute** Each given implementation will be considered for sync disposal management.
```csharp
[FilterSyncTransientImplementationAggregation(typeof(DisposeMeAsap))]
```
- **AsyncTransientAbstractionAggregationAttribute** Of each given abstraction all their implementations will be ignored for async disposal management.
```csharp
[AsyncTransientAbstractionAggregation(typeof(IWillCleanItUpMyselfAsync))]
```
- **FilterAsyncTransientAbstractionAggregationAttribute** Of each given abstraction all their implementations will be considered for async disposal management.
```csharp
[FilterAsyncTransientAbstractionAggregation(typeof(IWillCleanItUpMyselfAsync))]
```
- **AsyncTransientImplementationAggregationAttribute** Each given implementation will be ignored for async disposal management.
```csharp
[AsyncTransientImplementationAggregation(typeof(DisposeMeAsapAsync))]
```
- **FilterAsyncTransientAbstractionAggregationAttribute** Each given implementation will be considered for async disposal management.
```csharp
[FilterAsyncTransientImplementationAggregation(typeof(DisposeMeAsapAsync))]
```

## Recommendations

- Use the **Transient** flavor if an implementation implements both disposal interfaces and you would like to ignore both.
    - Or if you would like to have the disposal of the implementation ignored independent of future changes of its disposal interfaces
- If an implementation implements both disposal interfaces but you would like to have only one of them ignored, use either the **SyncTransient** or the **AsyncTransient** flavor, respectively.
- Pass `IDisposable` and `IAsyncDisposable` into **TransientAbstractionAggregationAttribute** if you want to practically disable the disposal management of implementations