# Samples

This page highlights existing sample projects that interested people are recommended to take a look at.

## Test Project

[The test project of DIE](https://github.com/Yeah69/MrMeeseeks.DIE/tree/main/Test) contains hundreds of mini containers each of which can be considered as a full-fledged sample. However, the bulk of it would focus on a single feature or aspect of DIE. Therefore, it is a nice source of getting to learn to use a certain feature, but not so much suited as a real-world example.

Foot note: DIE also has a project called [Sample](https://github.com/Yeah69/MrMeeseeks.DIE/tree/main/Sample). However, it is just used as a vehicle to quickly test stuff during development. So consider the containers there as pretty random.

## Self-Containerization

DIE is a complex project, that means it makes sense to use dependency injection with a container there as well. Retorical question: Which container would be suitable for this? - DIE itself, of course. [Here is the configuration](https://github.com/Yeah69/MrMeeseeks.DIE/blob/release/v2.0.0/Main/MsContainer/MsContainer.cs) that builds the container of DIE.

The self-containerization of DIE can keep up as a real world sample. For example, it uses the concepts of scope to a great extend. 

## Discontinued Hobby Project

The container configuration of a discontinued hobby project of Yeah69 is another sample for a non-simple use case. You can find the configuration [here](https://github.com/Yeah69/BFF/blob/experimental/MrMeeseeks.DIE/Composition.DIE/Container.cs). However, be aware that it might be one of the messiest of the samples.