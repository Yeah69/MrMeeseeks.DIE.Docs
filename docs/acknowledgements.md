# Acknowledgements

Dependency Injection containers now have a longer history in .Net. So it will come as no surprise that DIE has predecessors and sources of inspiration in the form of other DI container projects. This page is intended as an appreciation for those very projects (sorted alphabeticaly).

This part will be very opinionated and reflects my personal experience. I have not tried very many DI container projects. So I can't say anything about many of them.

One last thing before the acknowledgements. DI containers besides DIE certainly do exist, it makes no sense denying it. They surely can also be better suited for your use case, if so go for it. If you had a look at DIE and other container projects and decided for an alternative, I would like to hear about the drivers and motivations. Feel free to write about it in the [discussions](https://github.com/Yeah69/MrMeeseeks.DIE/discussions).

## Autofac

Project page: [Autofac](https://github.com/autofac/Autofac)

Autofac is probably the most popular of all DI containers in the .Net world. It has accompanied me throughout my professional career. Due to its popularity, Autofac was usually convincing to the teams that I worked with, as it is the most familiar to most people.

Autofac has earned this reputation. It is a rock-solid Reflection-based container. It has all the features you need. I will forever be grateful to the project for introducing me to Dependency Injection supported by containers.

## DryIoc

Project page: [DryIoc](https://github.com/dadhi/DryIoc)

Compared to other projects, I think DryIoc stands out because it dares to go its own way. For example, the interpretation of func parameters as overrides for transitive injections is something I have only discovered in this project. Which is one thing that was adopted by DIE.

DryIoc started as a reflection-based container. In the meantime, however, it also has possibilities to be used as a compile-time container, which is called DryIocZero.

I can heartily recommend everyone to take a look at this project.

## StrongInject

Project page: [StrongInject](https://github.com/YairHalberstadt/stronginject)

As one of the first source generator-based DI containers, I learned a lot from StrongInject. It has very quickly reached a high level of maturity and you can see in all corners that there is someone at work who knows his stuff.

In terms of features, StrongInject has already come a long way and can certainly keep up with many long-established Reflection-based containers, which is impressive.