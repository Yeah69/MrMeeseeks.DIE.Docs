# Welcome to MrMeeseeks.DIE

DIE is a secret agency organized by a bunch of Mr. Meeseekses. Its goal is to gather the information necessary to resolve your dependencies. Therefore, …

> The acronym DIE stands for **D**ependency **I**njection DI**E**

Send out Mr. Meeseeks spies in order to gather crucial information and let the secret agency DIE compose these info in order to build factory methods which create instances of types of your choice.

## Introduction

MrMeeseeks.DIE (in this documentation just referred to as DIE) is a compile-time dependency injection container for .Net. As such it generates factory methods which create instances that you need. Instead of relying on reflection the generated code uses the good old `new` operator to create instances like you would probably do yourself if you'd create a pure DI container yourself.

- Concepts
    - Purpose-focused registrations (instead of implementation-focused)
    - Spying
    - Scoping
- Async support
    - Wrapping
    - Async creation
    - Disposal
- Configuration
- Decorators
- Composites
- Lazy & Func
- Scoped Instances
- Collections
- ValueTuple
- Type Initializers
    - Sync
    - Async (Task & ValueTask)
- Nullability
- Custom factories, properties and fields
- Naming rules of containers