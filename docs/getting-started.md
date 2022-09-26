# Getting Started

This tutorial-like section is intended for beginners and users who are completely new to DIE. It's just intended to get you started.

## Our First Container

First we need an implementation which can later be created by the container. We'll start with:

```csharp
namespace GettingStarted;

internal class Logger
{
    public void Log(string message) => Console.WriteLine(message);
}
```

That's sufficient for a first small container configuration:

```csharp
using MrMeeseeks.DIE.Configuration.Attributes;

namespace GettingStarted;

[ImplementationAggregation(typeof(Logger))]

[CreateFunction(typeof(Logger), "Create")]
internal sealed partial class Container
{
    
}
```

Here we have our container. The `ImplementationAggregation`-attribute registers our `Logger` class as an implementation which should be considered by the container. The `CreateFunction`-attribute instructs the container to generate a `Logger Create()`-function (i.e. a factory method which returns an instance of type `Logger`).

We are ready to use it:

```csharp
using var container = new GettingStarted.Container();
var logger = container.Create();
logger.Log("Hello, World!");
```

This is it! Much likely the simple most example which can be thought up, but it's a start. ;)

The `using` will dispose the container asynchronously at the end. In this example it isn't necessary, because we have no disposables in use yet. However, this will make use prepared for future changes. But keep in mind that it isn't force upon you. If you don't need it you can remove it at your own risk of missing to dispose resources.

## Making It Dependency Injection Idiomatic

Let's spice things up a bit. In dependency injection it's in most cases better style to depend upon interfaces (abstractions) instead of concrete classes (implementations). What do we need to change for that? Let's start again with the `Logger` class:

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

And now a class which depends upon it:

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

… and the usage:

```csharp
using var container = new GettingStarted.Container();
var mrMeeseeks = container.Create();
mrMeeseeks.Greet();
```

Now we are using abstractions and have a true dependency (i.e. `MrMeeseeks` depends on an instance of type `ILogger`).

Please note that mapping interaces to a specific implementation is only required if the interface has multiple configured implementations. If the interfaces has a single configured implementation, then DIE will automatically chose it. With multiple implementations a manually configured implementation choice is necessary, but that is a more advanced topic.

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

If you want to have a look at the generated code yourself, you should add following to the `.csproj` which contains the container:

```xml
<EmitCompilerGeneratedFiles>true</EmitCompilerGeneratedFiles>
<CompilerGeneratedFilesOutputPath>$(BaseIntermediateOutputPath)\GeneratedFiles</CompilerGeneratedFilesOutputPath>
```

Then a copy of the generated code will be put into `obj\GeneratedFiles\…`. Strongly recommended to have a look into the generated code, if you are interested in learning how DIE works. 

The generated code for this example doesn't look like much for this small example. However, first of all you need to consider that all of this is boilerplate code, which becomes tedious to write quickly. Instead with DIE only little configuration is required to be written. And second, as your projects grow it'll become more and more of a burden to keep a pure DI container maintained yourself. While a DIE-container can be configured to almost seamlessly adjust to your continues changes with the help of convenience functions. 