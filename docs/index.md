# Welcome to MrMeeseeks.DIE

DIE is a secret agency organized by a bunch of Mr. Meeseekses. Its goal is to gather the information necessary to resolve your dependencies. Therefore, …

> The acronym DIE stands for **D**ependency **I**njection DI**E**

Let the secret agency DIE compose these info in order to build factory methods which create instances of types of your choice.

## Introduction

MrMeeseeks.DIE (in this documentation just referred to as DIE) is a compile-time dependency injection container for .Net. As such it generates factory methods which create instances that you need. Instead of relying on reflection the generated code uses the good old `new` operator to create instances like you would probably do yourself if you'd create a pure DI container.

- Concepts
    - Purpose-focused registrations (instead of implementation-focused)
    - Scoping
- [Async support](async-support.md)
- [Configuration](configuration.md)
- [Decorators](decorators.md)
- Composites
- Lazy & Func
- Scoped Instances
- Collections
- ValueTuple
- Initializers
    - Sync
    - Async (Task & ValueTask)
- Nullability
- User-defined Elements
  - Factories (functions, properties, fields)
  - AddForDisposal(Async)
  - Constructor Parameters
- [Naming rules](naming-rules.md)
- Disposal
- [Glossary](glossary.md)