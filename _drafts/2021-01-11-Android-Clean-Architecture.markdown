clean architecture 에서의 presentation, domain, data 레이어 구분은 항상 옳은가?

Let's go back to the time when we started a discussion about Android Clean Architecture.

## The origins
[Architecting Android... The clean way?](https://fernandocejas.com/blog/engineering/2014-09-03-architecting-android-the-clean-way/)


### Dependency Rule
Source code dependencies can only pointinwards and nothing in an inner circle can know anything at all about something in an outer circle.

### Clean Architecture
The idea is simple: clean architecture stands for a group of practices that produce systems that are:

Independent of Frameworks.
Testable.
Independent of UI.
Independent of Database.
Independent of any external agency.

The purpose is the separation of concerns by keeping the business rules not knowing anything at all about the outside world, thus, they can can be tested without any dependency to any external element.


## separated layers using Top-level gradle modlues
Thease layers, which are each labeled domain, presentation, and data, are each separated from one another using top-level gradle modules.


[PresentationDomainDataLayering](https://martinfowler.com/bliki/PresentationDomainDataLayering.html)

>  it’s usually not best to use presentation-domain-data as the higher level of modules. Often frameworks encourage you to have something like view-model-data as the top level namespaces; that’s OK for smaller systems, but once any of these layers gets too big you should split your top level into domain oriented modules which are internally layered.


> Don't use layers as the top level modules in a complex application instead make your top level modules be full-stack


