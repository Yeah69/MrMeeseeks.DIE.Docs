# Getting Started

This tutorial-like section is intended for beginners and users who are completely new to DIE. It's just meant to get you started.

## Our First Container

First, we need an implementation that can later be built by the container. We'll start with:

```csharp
namespace GettingStarted;

internal class Logger
{
    public void Log(string message) => Console.WriteLine(message);
}
```

That's enough for a first small container configuration:

```csharp
using MrMeeseeks.DIE.Configuration.Attributes;

namespace GettingStarted;

[ImplementationAggregation(typeof(Logger))]

[CreateFunction(typeof(Logger), "Create")]
internal sealed partial class Container
{
    private Container() { }
}
```

Here we have our container. The `ImplementationAggregation` attribute registers our `Logger` class as an implementation to be considered by the container. The `CreateFunction` attribute instructs the container to create a `Logger Create()` function (i.e. a factory method that returns an instance of type Logger).

With DIE a container needs to have at least one explicit constructor and all constructors should be private. These are necessary constraints of DIE in order to guarantee intitialization features (which we won't use in this example, however).

We are ready to use it:

```csharp
await using var container = GettingStarted.Container.DIE_CreateContainer();
var logger = container.Create();
logger.Log("Hello, World!");
```

This is it! Probably the simplest example you can think of, but it's a start ;)

The `await using` will asynchronously dispose of the container at the end. In this example it's not necessary because we don't have any disposables in use yet. However, this will prepare the use for future changes. But keep in mind that it's not forced upon you. If you don't need it you can remove it at your own risk of missing to dispose resources.

Please note that we don't use the container class constructor directly, but the a generated static function that creates a container instance via that constructor. If we would use the before mentioned initialization features, then they would be placed at this static function and therefore guaranteed to be processed as well.

## Making It Dependency Injection Idiomatic

Let's spice things up a bit. With dependency injection, in most cases it's better style to depend on interfaces (abstractions) rather than concrete classes (implementations). So what do we need to change? Let's start with the `Logger` class again:

```csharp
namespace GettingStarted;

public interface ILogger
{
    void Log(string message);
}

internal class Logger : ILogger
{
    public void Log(string message) => Console.WriteLine(message);
}
```

And now a class that depends on it:

```csharp
namespace GettingStarted;

internal interface IMrMeeseeks
{
    void Greet();
}

internal class MrMeeseeks : IMrMeeseeks
{
    private readonly ILogger _logger;

    internal MrMeeseeks(ILogger logger) => 
        _logger = logger;

    public void Greet() => 
        _logger.Log("I'm MrMeeseeks! Look at me!");
}
```

Some adjustments to the container, …

```csharp
using MrMeeseeks.DIE.Configuration.Attributes;

namespace GettingStarted;

[ImplementationAggregation(
    typeof(Logger), 
    typeof(MrMeeseeks))]

[CreateFunction(typeof(MrMeeseeks), "Create")]
internal sealed partial class Container
{
    
}
```

… and using it:

```csharp
using var container = new GettingStarted.Container();
var mrMeeseeks = container.Create();
mrMeeseeks.Greet();
```

Now we are using abstractions and have a true dependency (i.e. `MrMeeseeks` depends on an instance of type `ILogger`).

Note that mapping interfaces to a specific implementation is only necessary if the interface has multiple configured implementations. If the interface has a single configured implementation, DIE will automatically choose it. With multiple implementations, a manual choice of the configured implementation is required, but that is a more advanced topic.

## Generated Code

Let's have a look at the generated code:

```csharp
#nullable enable
namespace MrMeeseeks.DIE.Samples.GettingStarted
{
    sealed partial class Container : global::MrMeeseeks.DIE.Samples.GettingStarted.Container.ITransientScope_0_11, global::System.IAsyncDisposable, global::System.IDisposable
    {
        public static global::MrMeeseeks.DIE.Samples.GettingStarted.Container DIE_CreateContainer()
        {
            global::MrMeeseeks.DIE.Samples.GettingStarted.Container container_0_10 = new global::MrMeeseeks.DIE.Samples.GettingStarted.Container();
            return container_0_10;
        }

        internal global::MrMeeseeks.DIE.Samples.GettingStarted.MrMeeseeks Create()
        {
            if (Disposed_0_2)
                throw new System.ObjectDisposedException("global::MrMeeseeks.DIE.Samples.GettingStarted.Container", $"[DIE] This scope \"global::MrMeeseeks.DIE.Samples.GettingStarted.Container\" is already disposed, so it can't create a \"global::MrMeeseeks.DIE.Samples.GettingStarted.MrMeeseeks\" instance anymore.");
            global::MrMeeseeks.DIE.Samples.GettingStarted.MrMeeseeks functionCallResult_2_1 = (global::MrMeeseeks.DIE.Samples.GettingStarted.MrMeeseeks)CreateMrMeeseeks_2_0();
            if (Disposed_0_2)
                throw new System.ObjectDisposedException("global::MrMeeseeks.DIE.Samples.GettingStarted.Container", $"[DIE] This scope \"global::MrMeeseeks.DIE.Samples.GettingStarted.Container\" is already disposed, so it can't create a \"global::MrMeeseeks.DIE.Samples.GettingStarted.MrMeeseeks\" instance anymore.");
            return functionCallResult_2_1;
        }

        private global::MrMeeseeks.DIE.Samples.GettingStarted.MrMeeseeks CreateMrMeeseeks_2_0()
        {
            global::MrMeeseeks.DIE.Samples.GettingStarted.Logger logger_2_4 = new global::MrMeeseeks.DIE.Samples.GettingStarted.Logger();
            global::MrMeeseeks.DIE.Samples.GettingStarted.ILogger iLogger_2_3 = (global::MrMeeseeks.DIE.Samples.GettingStarted.ILogger)logger_2_4;
            global::MrMeeseeks.DIE.Samples.GettingStarted.MrMeeseeks mrMeeseeks_2_2 = new global::MrMeeseeks.DIE.Samples.GettingStarted.MrMeeseeks(logger: iLogger_2_3);
            return mrMeeseeks_2_2;
        }

        private global::System.Collections.Concurrent.ConcurrentBag<global::System.IAsyncDisposable> concurrentBag_0_7 = new global::System.Collections.Concurrent.ConcurrentBag<global::System.IAsyncDisposable>();
        private global::System.Collections.Concurrent.ConcurrentBag<global::System.IDisposable> concurrentBag_0_6 = new global::System.Collections.Concurrent.ConcurrentBag<global::System.IDisposable>();
        private int _disposed_0_0 = 0;
        private bool Disposed_0_2 => _disposed_0_0 != 0;
        public async global::System.Threading.Tasks.ValueTask DisposeAsync()
        {
            var disposed_0_1 = global::System.Threading.Interlocked.Exchange(ref _disposed_0_0, 1);
            if (disposed_0_1 != 0)
                return;
            global::System.Collections.Generic.List<global::System.Exception> aggregateException_0_4 = new global::System.Collections.Generic.List<global::System.Exception>();
            try
            {
                while (transientScopeDisposal_0_8.Count > 0)
                {
                    var transientScopeToDispose_0_9 = System.Linq.Enumerable.FirstOrDefault(transientScopeDisposal_0_8.Keys);
                    if (transientScopeToDispose_0_9 is not null && transientScopeDisposal_0_8.TryRemove(transientScopeToDispose_0_9, out _))
                    {
                        try
                        {
                            (transientScopeToDispose_0_9 as global::System.IDisposable)?.Dispose();
                        }
                        catch (global::System.Exception exceptionToAggregate_0_5)
                        {
                            // catch and aggregate so other disposals are triggered
                            aggregateException_0_4.Add(exceptionToAggregate_0_5);
                        }
                    }
                }

                transientScopeDisposal_0_8.Clear();
                while (concurrentBag_0_7.Count > 0 && concurrentBag_0_7.TryTake(out var iDisposable_0_3))
                {
                    try
                    {
                        await iDisposable_0_3.DisposeAsync();
                    }
                    catch (global::System.Exception exceptionToAggregate_0_5)
                    {
                        // catch and aggregate so other disposals are triggered
                        aggregateException_0_4.Add(exceptionToAggregate_0_5);
                    }
                }

                while (concurrentBag_0_6.Count > 0 && concurrentBag_0_6.TryTake(out var iDisposable_0_3))
                {
                    try
                    {
                        iDisposable_0_3.Dispose();
                    }
                    catch (global::System.Exception exceptionToAggregate_0_5)
                    {
                        // catch and aggregate so other disposals are triggered
                        aggregateException_0_4.Add(exceptionToAggregate_0_5);
                    }
                }
            }
            finally
            {
            }

            if (aggregateException_0_4.Count == 1)
                throw aggregateException_0_4[0];
            else if (aggregateException_0_4.Count > 1)
                throw new System.AggregateException(aggregateException_0_4);
        }

        public void Dispose()
        {
            var disposed_0_1 = global::System.Threading.Interlocked.Exchange(ref _disposed_0_0, 1);
            if (disposed_0_1 != 0)
                return;
            global::System.Collections.Generic.List<global::System.Exception> aggregateException_0_4 = new global::System.Collections.Generic.List<global::System.Exception>();
            try
            {
                while (transientScopeDisposal_0_8.Count > 0)
                {
                    var transientScopeToDispose_0_9 = System.Linq.Enumerable.FirstOrDefault(transientScopeDisposal_0_8.Keys);
                    if (transientScopeToDispose_0_9 is not null && transientScopeDisposal_0_8.TryRemove(transientScopeToDispose_0_9, out _))
                    {
                        try
                        {
                            (transientScopeToDispose_0_9 as global::System.IDisposable)?.Dispose();
                        }
                        catch (global::System.Exception exceptionToAggregate_0_5)
                        {
                            // catch and aggregate so other disposals are triggered
                            aggregateException_0_4.Add(exceptionToAggregate_0_5);
                        }
                    }
                }

                transientScopeDisposal_0_8.Clear();
                while (concurrentBag_0_7.Count > 0 && concurrentBag_0_7.TryTake(out var iDisposable_0_3))
                {
                    try
                    {
                        (iDisposable_0_3 as global::System.IDisposable)?.Dispose();
                    }
                    catch (global::System.Exception exceptionToAggregate_0_5)
                    {
                        // catch and aggregate so other disposals are triggered
                        aggregateException_0_4.Add(exceptionToAggregate_0_5);
                    }
                }

                while (concurrentBag_0_6.Count > 0 && concurrentBag_0_6.TryTake(out var iDisposable_0_3))
                {
                    try
                    {
                        iDisposable_0_3.Dispose();
                    }
                    catch (global::System.Exception exceptionToAggregate_0_5)
                    {
                        // catch and aggregate so other disposals are triggered
                        aggregateException_0_4.Add(exceptionToAggregate_0_5);
                    }
                }
            }
            finally
            {
            }

            if (aggregateException_0_4.Count == 1)
                throw aggregateException_0_4[0];
            else if (aggregateException_0_4.Count > 1)
                throw new System.AggregateException(aggregateException_0_4);
        }

        private global::System.Collections.Concurrent.ConcurrentDictionary<global::System.IDisposable, global::System.IDisposable> transientScopeDisposal_0_8 = new global::System.Collections.Concurrent.ConcurrentDictionary<global::System.IDisposable, global::System.IDisposable>();
        private interface ITransientScope_0_11
        {
        }
    }
}
#nullable disable
```

If you want to look at the generated code yourself, you should add the following to the `.csproj` that contains the container:

```xml
<EmitCompilerGeneratedFiles>true</EmitCompilerGeneratedFiles>
<CompilerGeneratedFilesOutputPath>$(BaseIntermediateOutputPath)\GeneratedFiles</CompilerGeneratedFilesOutputPath>
```

Then a copy of the generated code is placed in `obj\GeneratedFiles\…`. It is highly recommended to take a look at the generated code if you are interested in learning how DIE works. 

The generated code doesn't look like much for this small example. However, you have to keep in mind that this is boilerplate code, which can be tedious to write quickly. Instead, with DIE, there is very little configuration that needs to be written. And second, as your projects grow, it's going to become increasingly burdensome to maintain a pure DIE container yourself. A DIE container, on the other hand, can be configured to adapt almost seamlessly to your ongoing changes using convenience features.