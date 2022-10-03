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
    
}
```

Here we have our container. The `ImplementationAggregation` attribute registers our `Logger` class as an implementation to be considered by the container. The `CreateFunction` attribute instructs the container to create a `Logger Create()` function (i.e. a factory method that returns an instance of type Logger).

We are ready to use it:

```csharp
await using var container = new GettingStarted.Container();
var logger = container.Create();
logger.Log("Hello, World!");
```

This is it! Probably the simplest example you can think of, but it's a start ;)

The `await using` will asynchronously dispose of the container at the end. In this example it's not necessary because we don't have any disposables in use yet. However, this will prepare the use for future changes. But keep in mind that it's not forced upon you. If you don't need it you can remove it at your own risk of missing to dispose resources.

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
namespace GettingStarted
{
    partial class Container : global::System.IAsyncDisposable, global::System.IDisposable
    {
        private global::GettingStarted.MrMeeseeks CreateMrMeeseeks_2_0()
        {
            if (this.Disposed_1_7)
                throw new System.ObjectDisposedException("Container", $"[DIE] This scope \"Container\" is already disposed, so it can't create a \"global::GettingStarted.MrMeeseeks\" instance anymore.");
            global::GettingStarted.Logger logger_2_2 = new global::GettingStarted.Logger();
            global::GettingStarted.ILogger iLogger_2_3 = (global::GettingStarted.ILogger)logger_2_2;
            global::GettingStarted.MrMeeseeks mrMeeseeks_2_1 = new global::GettingStarted.MrMeeseeks(logger: iLogger_2_3);
            if (this.Disposed_1_7)
                throw new System.ObjectDisposedException("Container", $"[DIE] This scope \"Container\" is already disposed, so it can't create a \"global::GettingStarted.MrMeeseeks\" instance anymore.");
            return mrMeeseeks_2_1;
        }

        public global::GettingStarted.MrMeeseeks Create()
        {
            if (this.Disposed_1_7)
                throw new System.ObjectDisposedException("Container", $"[DIE] This scope \"Container\" is already disposed, so it can't create a \"global::GettingStarted.MrMeeseeks\" instance anymore.");
            global::GettingStarted.MrMeeseeks ret_2_4 = (global::GettingStarted.MrMeeseeks)this.CreateMrMeeseeks_2_0();
            if (this.Disposed_1_7)
                throw new System.ObjectDisposedException("Container", $"[DIE] This scope \"Container\" is already disposed, so it can't create a \"global::GettingStarted.MrMeeseeks\" instance anymore.");
            return ret_2_4;
        }

        public global::System.Threading.Tasks.Task<global::GettingStarted.MrMeeseeks> CreateAsync()
        {
            if (this.Disposed_1_7)
                throw new System.ObjectDisposedException("Container", $"[DIE] This scope \"Container\" is already disposed, so it can't create a \"global::System.Threading.Tasks.Task<global::GettingStarted.MrMeeseeks>\" instance anymore.");
            global::GettingStarted.MrMeeseeks ret_2_5 = (global::GettingStarted.MrMeeseeks)this.CreateMrMeeseeks_2_0();
            global::System.Threading.Tasks.Task<global::GettingStarted.MrMeeseeks> task_1_1 = global::System.Threading.Tasks.Task.FromResult(ret_2_5);
            if (this.Disposed_1_7)
                throw new System.ObjectDisposedException("Container", $"[DIE] This scope \"Container\" is already disposed, so it can't create a \"global::System.Threading.Tasks.Task<global::GettingStarted.MrMeeseeks>\" instance anymore.");
            return task_1_1;
        }

        public global::System.Threading.Tasks.ValueTask<global::GettingStarted.MrMeeseeks> CreateValueAsync()
        {
            if (this.Disposed_1_7)
                throw new System.ObjectDisposedException("Container", $"[DIE] This scope \"Container\" is already disposed, so it can't create a \"global::System.Threading.Tasks.ValueTask<global::GettingStarted.MrMeeseeks>\" instance anymore.");
            global::GettingStarted.MrMeeseeks ret_2_6 = (global::GettingStarted.MrMeeseeks)this.CreateMrMeeseeks_2_0();
            global::System.Threading.Tasks.ValueTask<global::GettingStarted.MrMeeseeks> task_1_2 = global::System.Threading.Tasks.ValueTask.FromResult(ret_2_6);
            if (this.Disposed_1_7)
                throw new System.ObjectDisposedException("Container", $"[DIE] This scope \"Container\" is already disposed, so it can't create a \"global::System.Threading.Tasks.ValueTask<global::GettingStarted.MrMeeseeks>\" instance anymore.");
            return task_1_2;
        }

        private int _disposed_1_5 = 0;
        private bool Disposed_1_7 => _disposed_1_5 != 0;
        public void Dispose()
        {
            var disposed_1_6 = global::System.Threading.Interlocked.Exchange(ref this._disposed_1_5, 1);
        }

        public global::System.Threading.Tasks.ValueTask DisposeAsync()
        {
            Dispose();
            return new global::System.Threading.Tasks.ValueTask(global::System.Threading.Tasks.Task.CompletedTask);
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