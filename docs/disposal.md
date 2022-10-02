# Disposal

Disposal is an integral part of containers in DIE. By default, disposables (either `IDisposable` or `IAsyncDisposable` implementations) are managed by the container or scope in which they are instantiated. Managing means that the disposables are cached until the scope or container is disposed of, and then disposed of as well.

Marking a disposable implementation as transient relieves the container of the responsibility of managing the disposable. There are three options:

- Sync-transient (discard management for `IDisposable`)
- Async-transient (discard management for `IAsyncDisposable`)
- Transient (discard management for both, `IDisposable` & `IAsyncDisposable`)

That is, if an implementation implements both disposal interfaces, then when the container is disposed of …

- Both disposal methods will be called if it's not marked as transient in any way.
- Only the sync disposal method will be called if it is marked async-transient.
- Only the async method will be called if it is marked as sync-transient.
- None of the disposal methods will be called if marked transient.

In addition, there is an option to manually add disposables to disposal management using certain user-defined elements (`DIE_AddForDisposal` or `DIE_AddForDisposalAsync`). 

DIE will automatically generate the disposal methods on the container and scope classes, so the user isn't allowed to implement them. If managed async disposables exist, only the `IAsyncDisposable` interface will be implemented (this prevents blocking disposal calls in the generated code). Otherwise, both disposal interfaces are implemented. 

When disposing of the container, it is sufficient to call one of the disposal methods if both are present. Once you call one of the disposal methods, the container is locked against further instantiation. This means that subsequent calls to create functions or factories that have been injected will cause an exception to be thrown.

## Scope's Disposal Behavior

In many ways, the disposal behavior of scopes is the same as that of containers. They implement the disposal interfaces, they manage disposables, you can manage disposal by marking implementations as transient, and you can use the user-defined elements to manually attach disposables.

However, disposing of the scopes themselves is handled differently. Even the normal scope and the transient scope have different behaviors. The normal scope isn't intended to be manually disposed of, and is itself attached to the parent scope (the one it was created from) for disposal. 

The transient scope isn't attached to its parent scope. Instead, it's scope root implementation can get an `IDisposable` or `IAsyncDisposable` dependency injected which can be used to eagerly & manually dispose of the transient scope prior to container disposal. However, transient scopes will still be attached to the containers disposal. Therefore, transient scopes can be used to eagerly dispose, but rest assured that they will be disposed at the latest when the container is disposed.

Note: If async disposables are managed, then `IDisposable` injections won't work in transient scope root implementations. It'll cause a compile time error. Therefore it is always safer and recommended to use an `IAsyncDisposable` injection instead, which should always work.