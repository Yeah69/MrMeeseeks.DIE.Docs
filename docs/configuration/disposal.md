# Disposal Configurations

## Purpose

DIE automatically manages disposables for you and disposes of them when the container or (temporary) scope is discarded. However, sometimes you want to handle the disposal yourself. In these cases you need to tell the container or scopes not to manage these specific implementations. 

The attributes come in three flavors, which can be distinguished by the following terms:

- **SyncTransient** ignores `IDisposable` implementations for disposal management.
- **AsyncTransient** ignores `IAsyncDisposable` implementations for disposal management. 
- **Transient** ignores `IDisposable` & `IAsyncDisposable` implementations for disposal management.

## Attributes

- **TransientAbstractionAggregationAttribute** For any given abstraction, all of its implementations are completely ignored for disposal management.
```csharp
[TransientAbstractionAggregation(typeof(IWillCleanItUpMyself))]
```
- **FilterTransientAbstractionAggregationAttribute** For each given abstraction, all of its implementations are considered again for disposal management.
```csharp
[FilterTransientAbstractionAggregation(typeof(IWillCleanItUpMyselfAsync))]
```
- **TransientImplementationAggregationAttribute** Any given implementation is completely ignored for disposal management.
```csharp
[TransientImplementationAggregation(typeof(DisposeMeAsap))]
```
- **FilterTransientAbstractionAggregationAttribute** Each given implementation will be considered again for disposal management.
```csharp
[FilterTransientImplementationAggregation(typeof(DisposeMeAsapAsync))]
```
- **SyncTransientAbstractionAggregationAttribute** For any given abstraction, all of its implementations are ignored for sync disposal management.
```csharp
[SyncTransientAbstractionAggregation(typeof(IWillCleanItUpMyself))]
```
- **FilterSyncTransientAbstractionAggregationAttribute** For each given abstraction, all of its implementations are considered again for sync disposal management.
```csharp
[FilterSyncTransientAbstractionAggregation(typeof(IWillCleanItUpMyself))]
```
- **SyncTransientImplementationAggregationAttribute** Any given implementation will be ignored for sync disposal management.
```csharp
[SyncTransientImplementationAggregation(typeof(DisposeMeAsap))]
```
- **FilterSyncTransientAbstractionAggregationAttribute** Any given implementation will be considered again for sync disposal management.
```csharp
[FilterSyncTransientImplementationAggregation(typeof(DisposeMeAsap))]
```
- **AsyncTransientAbstractionAggregationAttribute** For any given abstraction, all of its implementations are ignored for async disposal management.
```csharp
[AsyncTransientAbstractionAggregation(typeof(IWillCleanItUpMyselfAsync))]
```
- **FilterAsyncTransientAbstractionAggregationAttribute** For each given abstraction, all of its implementations are considered again for async disposal management.
```csharp
[FilterAsyncTransientAbstractionAggregation(typeof(IWillCleanItUpMyselfAsync))]
```
- **AsyncTransientImplementationAggregationAttribute** Any given implementation is ignored for async disposal management.
```csharp
[AsyncTransientImplementationAggregation(typeof(DisposeMeAsapAsync))]
```
- **FilterAsyncTransientAbstractionAggregationAttribute** Any given implementation is considered for async disposal management.
```csharp
[FilterAsyncTransientImplementationAggregation(typeof(DisposeMeAsapAsync))]
```

## Recommendations

- Use the **Transient** flavor when an implementation implements both disposal interfaces and you want to ignore both.
- Or if you want the implementation's disposal to be ignored regardless of future changes to its disposal interfaces.
- If an implementation implements both disposal interfaces, but you only want one of them to be ignored, use either the **SyncTransient** or **AsyncTransient** flavor.
- Pass `IDisposable` and `IAsyncDisposable` to the **TransientAbstractionAggregationAttribute** if you want to effectively disable disposal management for implementations.