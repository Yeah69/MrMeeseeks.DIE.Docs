# Disposal

The disposal is an integral part of the containers in DIE. Per default disposables (either `IDisposable`- or `IAsyncDisposable`-implementations) will be managed by the container or the scope in which they are instantiated. Managment means that the disposables will be cached until its scope or the container is disposed and disposed as well.

By marking disposable implementations as transient the container's responsibility to manage the disposable is discarded. There are three options:

- Sync-transient (discarding managment for `IDisposable`s)
- Async-transient (discarding managment for `IAsyncDisposable`s)
- Transient (discarding managment for both, `IDisposable`s & `IAsyncDisposable`s)

That means if an implementation implements both disposal interfaces, then on disposal of the container …

- Both of the disposal methods will get called if it isn't marked transient in any way.
- Only the sync disposal method will get called if is marked async-transient.
- Only the async disposal method will get called if is marked sync-transient.
- None of the disposal methods will get called if it is marked transient.

Additionaly, there is the option to add disposables manually to the disposal managment by using certain user defined elements (`DIE_AddForDisposal` or `DIE_AddForDisposalAsync`). 

DIE will generate the disposal methods on the container and the scope classes automatically, so the user isn't allowed to implement them. If managed async disposables exist, then only the `IAsyncDisposable` interface will be implemented (this prevents blocking disposal calls in the generated code). Otherwise, both disposal interfaces will be implemented. 

On disposal of the container it is sufficient to call either of the disposal methods, if both are existing. As soon as you call one of the disposal methods the container is locked off for further instantiations. That means subsequent calls of create functions or factories which got injected will lead to an exception being thrown.

## Scope's Disposal Behavior

In many ways the disposal behavior of scopes is same as of the container. They implement the disposal interfaces, they manage disposables, you can discard managment by marking implementations as transient, and you can use the user-defined elements for manual disposable attachment.

However, disposing the scopes themselves is differently handled. Thereby even the ordinary scope and the transient scope have different behaviors. The ordinary scope isn't intended to be disposed manually and itself is attached for disposal to the parent scope (the one which it got created from). 

The transient scope isn't attached to it's parent scope. Instead, it's scope root implementation can get an `IDisposable` or `IAsyncDisposable` dependency injected which can be used to eagerly & manually dispose the transient scope before the containers disposal. However, transient scopes are still attached to the containers disposal. Therefore, transient scopes can be used to be disposed eagerly, but rest assured that they be disposed at latest when the container is disposed.

Hint: If async disposables are managed, then `IDisposable` injections won't work in transient scope root implementations. It'll lead to a compile-time error. Therefore it is always safer and therefore advised to use an `IAsyncDisposable` injection which should always work instead.