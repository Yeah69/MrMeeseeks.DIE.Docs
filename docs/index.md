# Welcome to MrMeeseeks.DIE

The DIE is a secret agency organized by a bunch of Mr. Meeseekses. Its goal is to gather the information necessary to resolve your dependencies. Therefore …

> The acronym DIE stands for **D**ependency **I**njection DI**E**.

Let the secret agency DIE compile this information to build factory methods that create instances of types of your choice.

## Introduction

MrMeeseeks.DIE (just DIE in this documentation) is a compile-time dependency injection container for .Net. As such, it generates factory methods that create the instances you need. Instead of relying on reflection, the generated code uses the good old `new` operator to create instances, just as you'd probably do yourself if you were creating a pure DI container.

## Nuget

The easiest way to use DIE is to get it via nuget. Here is the package page:

[https://www.nuget.org/packages/MrMeeseeks.DIE/](https://www.nuget.org/packages/MrMeeseeks.DIE/)

Either search for `MrMeeseeks.DIE` in the nuget manager of the IDE of your choice.

Or call the following PowerShell command:

```powershell
Install-Package MrMeeseeks.DIE
```


Alternatively, you can use `dotnet`:

```sh
dotnet add [your project] package MrMeeseeks.DIE
```

Or manually add the package reference to the target `.csproj`:

```xml
<PackageReference Include="MrMeeseeks.DIE" Version="[preferrably the current version]" />
```

## Characteristics Of DIE

- Compile-Time Code Generation 
    - Incomplete configurations will most likely result in a failed build
- Unambiguousness
    - Container doesn't resolve ambiguity through assumptions
    - Configuration features to resolve ambiguities
- Convenience
    - Default behaviors designed to reduce the amount of configuration required
    - Optional marker interfaces can be used for configurations
    - Mass configuration (e.g., register all implementations with a single configuration)
- Flexibility
    - Allows opt-in configuration style
    - Allows opt-out configuration style
- Feature richness
    - Scoping
    - Async support
    - Generics support
    - User-defined elements (factories, custom parameters, …)
    - Generated factories (Func<…>, Lazy<…>)
    - Decorators & Composites
- Maximum transparency
    - Only your configuration code needs to know about DIE
    - The rest of your code base can remain oblivious

## Further Docs

- [Getting Started](getting-started.md)
- Concepts And Configurations
    - [Configuration](configuration.md)
    - [Naming rules](naming-rules.md)
    - [Scoping](scoping.md)
    - [User-defined Elements](user-defined-elements.md)
    - [Disposal](disposal.md)
- Usage
    - [Injections](injections.md)
    - [Async Support](async-support.md)
    - [Generics support](generics-support.md)
    - [Decorators & Composites](decorators-composites.md)
    - [Initializers](initializers.md)
- [Glossary](glossary.md)

## Repositories And Licenses

This project has separate repositories and licenses for the source code and for this documentation.

### Source Code

The source code is hosted on github under the [MIT](https://spdx.org/licenses/MIT.html) license:

[https://github.com/Yeah69/MrMeeseeks.DIE](https://github.com/Yeah69/MrMeeseeks.DIE)

### Documenation

The documentation is hosted on github under [The Unlicense](https://spdx.org/licenses/Unlicense.html) license:

[https://github.com/Yeah69/MrMeeseeks.DIE.Docs](https://github.com/Yeah69/MrMeeseeks.DIE.Docs)