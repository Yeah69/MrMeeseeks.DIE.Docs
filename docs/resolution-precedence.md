# Resolution Precedence

During resolution DIE processes the injected types. The resulting resolution (i.e. which kind of injection will take place) depends on the the type and precedence. The first matching rule of the following precendence list will be selected:

1. Special Overrides (`IDisposable` or `IAsyncDisposable` as a transient scope disposal hook or decorated instances or composited instances)
1. Overrides (Func- or Create-Function parameters)
1. User-defined factories (`DIE_Factory`-fields, -properties & -functions)
1. Wrapper types (`ValueTask<…>`, `Task<…>`, `ValueTuple<…>`, `Tuple<…>`)
1. Factory types (`Lazy<…>` & `Func<…>`)
1. Collection types (`IEnumerable<…>`, `IAsyncEnumerable<…>`,arrays, `IReadOnlyList<…>` and so on)
1. Abstractions (interface or abstract class types)
1. Implementations (structs or non-abstract class types)

Some remarks:
 
The special overrides are contextual. Meaning that this rule is only active in transient scopes (for `IDisposable` & `IAsyncDisposable`) or decorators (for decorated instances) or composites (for composited instances).

Wrapper types are resolved by starting the resolution process for its innet type anew. The same behavior applies to factory and collection types.

The substitution types for abstraction types will ignore this list and will straigth up resolve as an implementation.