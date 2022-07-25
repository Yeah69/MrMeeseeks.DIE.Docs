# Naming Rules

Container classes (i.e. classes which get at least one `CreateFunction`-attribute applied) will be used by DIE to generate code into it and can be use by you as the consumer. In principal, you can give this class any member you would like. However, in order to prevent collisions of identifiers of members of the generated code and your code DIE has some naming rules for container classes. You'll need to follow them, but rest assured that the validation of DIE will check any violation and point you to the violating member.

The naming rules don't only apply to container classes but to (transient) scope classes in the same way.

There a three stages to the naming rules. Sorted descendingly by strictness:

1. **Member identifiers that you aren't allowed to use fall into two categories:**
   1. **Don't use identifiers like `{number}_{number}` or identifiers with the suffix `_{number}_{number}` on any member.** With a few exceptions all of the generated code will use this pattern. These paterns are deliberately chosen cryptically, so that the prohibition to use them can be tolerated as well as possible.
   1. **`Dispose` & `DisposeAsync` is reserved for the generated code as well.** If possible DIE will generate this function so you are not allowed to do that on your own. If you need to append some instances for disposal manually, then you can use the special user-defined elements for that (**todo reference to the user-defined element documentation**). 
1. **Use identifiers for members with the prefix "DIE_" only for user-defined elements.** To an extend you can chose the name of certain user-defined element freely. However, there are constraints involved which are specific to the kind of user-defined element which you would like to use.
1. **Every identifier that doesn't match the above rules is allowed to be chosen freely by you.** In that case DIE will guarantee you to be collision-free with the generated code.