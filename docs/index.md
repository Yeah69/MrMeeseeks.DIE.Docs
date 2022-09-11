# Welcome to MrMeeseeks.DIE

DIE is a secret agency organized by a bunch of Mr. Meeseekses. Its goal is to gather the information necessary to resolve your dependencies. Therefore, …

> The acronym DIE stands for **D**ependency **I**njection DI**E**

Let the secret agency DIE compose these info in order to build factory methods which create instances of types of your choice.

## Introduction

MrMeeseeks.DIE (in this documentation just referred to as DIE) is a compile-time dependency injection container for .Net. As such it generates factory methods which create instances that you need. Instead of relying on reflection the generated code uses the good old `new` operator to create instances like you would probably do yourself if you'd create a pure DI container.

## Nuget

The easiest way of using DIE is by getting it through nuget. Here is the package page:

https://www.nuget.org/packages/MrMeeseeks.DIE/

Either search for `MrMeeseeks.DIE` in the nuget manager of the IDE of your choice.

Or call following PowerShell-command:

> Install-Package MrMeeseeks.DIE

Or manually insert the package reference into the target `.csproj`:

```xml
<PackageReference Include="MrMeeseeks.DIE" Version="[preferrably the current version]" />
```

## Properties Of DIE

- Compile-Time Code Generation 
    - Incomplete configurations will most likely result in a failed build
- Unambiguity
    - Container doesn't resolve ambiguities by assumptions
    - Configuration features for resolving ambiguities
- Convenience
    - Default behaviors intended to decrease required configuration
    - Marker interfaces
    - Bulk configuration (e.g. register all implementations with a single configuration)
- Flexibility
    - Opt-in configuration style possible
    - Opt-out configuration style possible
    - Choose yourself
- Feature-richness
    - Scoping
    - Async support
    - Generics support
    - User-defined elements (Factories, custom parameters, …)
    - Generated Factories (Func<…>, Lazy<…>)
    - Decorators & Composites

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