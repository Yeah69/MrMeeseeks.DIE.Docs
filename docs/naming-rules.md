# Naming Rules

Container classes (i.e. classes that have at least one `CreateFunction` attribute applied to them) are used by DIE to generate code into them, and can be used by you as a consumer. In principle, you can give this class any member you want. However, to prevent collisions between the member identifiers of the generated code and your code, DIE has some naming rules for container classes. You'll need to follow them, but rest assured that DIE's validation will catch any violation and point you to the offending member.

The naming rules don't just apply to container classes, but to (transient) scope classes as well.

These are the three levels of naming rules, in descending order of strictness:

1. **Member identifiers that you can't use fall into two categories:**
    1. **Don't use identifiers like `{number}_{number}` or identifiers with the suffix `_{number}_{number}` on any member.** With a few exceptions, all generated code will use this pattern. These patterns are deliberately chosen to be cryptic, so that the prohibition to use them can be tolerated as much as possible.
    1. **`Dispose` & `DisposeAsync` are also reserved for the generated code.** If possible, DIE will generate this function, so you are not allowed to do it yourself. If you need to manually append some instances for disposal, you can use the special user-defined elements (see [user-defined elements page](user-defined-elements.md)).
2. **Use member identifiers with the `DIE_` prefix for user-defined elements only.** To an extend you can chose the name of certain user-defined elements freely. However, there are restrictions that are specific to the type of user-defined element you want to use. For example, user-defined factory methods/properties/fields must start with `DIE_Factory`; any letter or number that follows is up to you, as long as you don't break the rules above (see [user-defined elements page](user-defined-elements.md)).
3. **Any identifier that doesn't conform to the above rules is up to you.** In this case, DIE guarantees that you will be collision-free with the generated code.